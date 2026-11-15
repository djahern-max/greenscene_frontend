# Greenscene Frontend — CLAUDE.md

## What this is
React frontend for Greenscene (greenscene.io) — a multi-tenant recruiting and onboarding tool for green-industry companies (landscaping and similar). Rye Beach Landscaping, LLC is the intended first real tenant. Onboarding is a future module; nothing for it exists yet. Talks to the FastAPI backend at `VITE_API_URL` (local: `http://localhost:8000`). Serves three user types: admin/recruiter, employer, candidate.

**Origin:** forked 2026-11-15 from `ryzerecruiting_frontend` at commit `758ef68`. The original repo and its folder (`/Users/dane/code/ryzerecruiting_frontend`) are Dane's preserved fallback — **never modify, commit to, or push from them.**

**The code still says RYZE.** Wordmarks, copy, legal pages and config are un-rebranded; rebranding is a separate queued task. The sections below describe the code as it is today, so they still say RYZE where the code does.

## Stack
- React 19, React Router 7, Vite 7
- **Styling: CSS Modules** (`ComponentName.module.css` alongside each component) — this is the default; don't introduce Tailwind, styled-components, or another styling approach without asking
- **Three API-calling patterns currently coexist — see "In-progress: API call consolidation" below before assuming which one a file uses**
- `lucide-react` for icons, custom SVGs live in `src/assets/icons/`
- `react-markdown` for rendering AI-generated content (candidate/employer summaries, chat responses)
- `canvas-confetti` for celebration moments in the UI

## Repo layout
- `src/pages/` — route-level views (e.g. `CandidateProfile.jsx`, `EmployerProfile.jsx`, `CandidateSelfProfile.jsx`), each typically paired with a `.module.css` file
- `src/components/` — shared/reusable components (e.g. `Header`, `AdminHeader`)
- `src/contexts/` — `AuthContext.jsx` is the main one — owns user state, login/register/logout, and a global axios 402 interceptor for expired trials/subscriptions
- `src/services/` — API helpers (e.g. `api.js` with `apiFetch`)
- `src/utils/` — helpers like `imageResize.js`
- `src/App.jsx` — route definitions; routes are gated by `AdminRoute` / `SuperAdminRoute` wrappers

## Local development
Facts verified against this machine on 2026-11-15, except where a line says otherwise.

- Repo: `/Users/dane/code/greenscene_frontend`. Sibling backend: `/Users/dane/code/greenscene_backend` — it must be running on port 8000.
- `npm run dev` serves on `http://localhost:5173` (Vite default; no override in `vite.config.js`).
- The local `.env` points `VITE_API_URL` at `http://localhost:8000`.
- Google sign-in is not expected to work locally (backend CHANGELOG, Local dev environment entry, Decision 2: SessionMiddleware stays `https_only=True`). Not tested on 2026-11-15. Use email and password.

## GUARDRAIL: `ryze` is a functional tenant slug — do not rename it
The string `ryze` is not just branding. It is the platform tenant's slug, and the frontend compares against it. **Do not rename, remove, or "clean up" any of the sites below as part of any task unless that task's spec says so in words.** Display-text rebranding and slug changes are different jobs. The backend carries the matching guardrail (`greenscene_backend/CLAUDE.md`, same heading) — the slug must change in both repos together or not at all.

Where the slug is functional (files and counts as of the fork — five sites, one per file):
- **`user.tenant_id !== 'ryze'` (3):** `src/components/Header.jsx` (gates "powered by RYZE"), `src/pages/CandidateDashboard.jsx` and `src/pages/CandidateSelfProfile.jsx` (each gates a tenant-name line).
- **`|| 'ryze'` fallback (1):** `src/contexts/AuthContext.jsx` (analytics `tenant_id` property).
- **`/ryze/invite` route (1):** `src/App.jsx`. Gated by `SuperAdminRoute` here; the backend endpoint it calls is slug-gated.

## Auth model
- **Bearer token, not cookies.** JWT is stored in `localStorage` under the key `token` and sent as `Authorization: Bearer <token>`.
- `AuthContext` fetches `/api/auth/me` on load if a token exists, and redirects post-login/register based on `user_type` (`ADMIN` / `EMPLOYER` / candidate default).
- A global axios response interceptor in `AuthContext.jsx` watches for `402` (trial expired / inactive subscription) and redirects to `/upgrade`, skipping that redirect if already on `/upgrade` or `/billing`.
- **`apiFetch` (in `services/api.js`) has its own separate 402 check** — a second, independent implementation of the same idea, not routed through the axios interceptor.
- **Confirmed by a full call-site audit (July 2026): raw `fetch` — bypassing both 402 checks entirely — is used in 17+ files, covering most of the admin app** (`AdminDashboard`, `ChatPage`, `CandidatesPage`, `EmployerRoster`, `JobOrderRoster`, both self-profile pages, `DBExplorer`, `InviteForm`, and more — see the migration list below). This is not an edge case to flag occasionally; it's the current majority pattern for authenticated pages. Don't assume 402 handling exists on any page without checking.

## In-progress: API call consolidation
Goal: make `apiFetch` the single source of truth for authenticated calls (auto-attach `Authorization` from localStorage + 402 handling), then migrate every raw-`fetch` call site onto it. One file per session, test + commit between each — don't batch multiple files in one change.

**Already fine — don't touch:**
- Axios call sites: `BookingCard.jsx`, `AuthContext.jsx`, `CompleteOAuthSignup.jsx` — share the axios instance, get the interceptor for free.
- `apiFetch` call sites: `AdminDashboard.jsx`* (mixed), `CandidateDashboard.jsx`, `CandidateSelfProfile.jsx`* (mixed), `EmployerDashboard.jsx`, `EmployerSelfProfile.jsx`* (mixed), `admin/TenantSettings.jsx`. (*mixed = also has raw fetch, see migration list.)
- `OAuthCallback.jsx` — intentionally raw; validates a token before `AuthContext` state exists. Leave as-is.
- `UpgradePage.jsx` — intentionally raw; it's the 402 redirect's own destination and is explicitly excluded from the interceptor's redirect logic. Migrating it risks fighting that exclusion. Leave as-is.

**Migration order (raw `fetch` → `apiFetch`), highest-traffic first:**
1. `ChatPage.jsx`, `AdminDashboard.jsx` (finish — already partially on `apiFetch`)
2. `CandidatesPage.jsx`, `EmployerRoster.jsx`, `JobOrderRoster.jsx`
3. `CandidateProfile.jsx`, `EmployerProfile.jsx`, `JobOrderDetail.jsx`
4. `CandidateSelfProfile.jsx`, `EmployerSelfProfile.jsx` (finish — photo/banner upload calls)
5. `AdminHeader.jsx`, `CandidateModal.jsx`, `IntelligenceBrief.jsx`, `IntelligenceMessage.jsx`, `ChangePassword.jsx`, `admin/DBExplorer.jsx`, `admin/InviteForm.jsx`

**Verification per file:** temporarily set a test tenant's `status` to `expired` in the DB, confirm the `/upgrade` redirect fires from that specific page, then flip it back. Don't consider a migration done until this is checked once.

## Conventions
- **No `<form>` tags in artifacts/components that need onClick-style submission logic** — this project uses controlled inputs with explicit handlers throughout.
- File uploads (logos, banners, photos) use `FormData` — historically via raw `fetch`, now migrating to `apiFetch` (see migration list above). `apiFetch` still just wraps `fetch`, so passing a `FormData` body works unchanged — don't set a `Content-Type` header manually, let the browser set the multipart boundary.
- `API_BASE` / `API_URL` is read from `import.meta.env.VITE_API_URL` with a `http://localhost:8000` fallback — reuse this pattern, don't hardcode the URL in new files.
- Profile pages (`CandidateProfile`, `EmployerProfile`) are built as read-only, shareable views separate from the admin edit modals — don't collapse these back together without checking with me first.

## WARNING: production builds must not leave this machine
`.env.production` sets `VITE_API_URL` to `https://api.ryze.ai` — a domain Dane sold and no longer controls. Anything built with it sends every API call, including logins, to someone else's domain. Running `npm run build` locally as a compile check is fine. The `dist/` it produces must **never be served, uploaded or deployed** until the rebrand task changes `.env.production`. `.env`, `.env.example` and `.env.production` are all tracked in git and this repo is public.

## Deploy workflow — no host yet
There is no production host. The droplet that hosted the old product is gone and hosting is TBD. Until a host exists, a task ends at `git push origin main` plus local verification.

These rules hold wherever the app is hosted next:
- Never run `npm install` by hand on a host before pulling and building — it causes package.json/lockfile conflicts with what's committed.
- Once a host exists, deploy commands come to Dane as exact commands to run — don't execute them yourself.

### HISTORICAL — old droplet procedure (server no longer exists)
Kept for reference only; nothing below can be run.
```
# local
npm run build

# on server
git pull
npm run build
```

## How I want you to work
- **Audit before you refactor.** For anything beyond a trivial fix: read the relevant files and related CSS Modules, list what you'd change and why, and wait for me to confirm before writing code.
- **One concern per change** — don't mix a refactor with a styling pass with a rename.
- I prefer **complete drop-in replacement files** for components/pages that have gotten complex, rather than partial diffs — I'll say so explicitly when I want that.
- Small, clearly-scoped surgical edits are fine without asking first.
- Match the CSS Modules + component-per-file pattern already in use — don't introduce a different state management library (Redux, Zustand, etc.) or CSS approach without discussing it first.

## Session workflow — context/current-feature.md
At the start of every session, read `context/current-feature.md` — it holds the active task's Status, Goals, Related Files, and Verification steps. Work from it, don't start unrelated work without checking it first.

- When you finish a meaningful chunk of work (e.g. one file migrated and tested), add a dated line to its History section — earliest to latest.
- When a task is fully done and verified, tell me first and wait for my explicit confirmation. **Once I confirm**, archive it yourself: copy its final Goals summary + full History into `context/CHANGELOG.md` as a new entry at the **top**, matching the format of the existing entries, then reset `current-feature.md` — to the blank template, or to the next task's content if I've handed it to you in the same message. **Never archive unprompted or before I confirm.** The confirmation is the gate; nothing moves until I say the word.

- **Blank is a valid resting state.** Resetting `current-feature.md` to the blank template with *no* new task loaded is a normal close-out — Part 1 of a two-part swap (`context/session-opener.md`, Case A-close), not a sign the task was abandoned or that you should infer the next one. The next task may arrive in a later message; when it does, I'll place it in the file and point you at it (Case B). An empty `current-feature.md` is never license to start unrelated work — wait for the next task.

- If I ask you to start something not reflected in `current-feature.md`, ask whether to update the file first before writing code, rather than silently working off-script.
