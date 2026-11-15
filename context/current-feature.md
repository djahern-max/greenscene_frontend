# Current Feature — Housekeeping after the fork (docs only)

**Date:** 2026-11-15
**Repo:** greenscene_frontend

## Objective
Make `CLAUDE.md` and `context/` tell the truth about this repo, the same way
the backend's were fixed today (see `greenscene_backend/context/CHANGELOG.md`,
top entry). Docs only. Nothing under `src/`, `public/`, `index.html`,
`package.json`, or any `.env*` file changes in this task.

## Context
On 2026-11-15 this repo was created as a copy of `ryzerecruiting_frontend` at
commit `758ef68` ("Removed Login with LinkedIn from Auth.jsx", 2026-07-29) and
pushed to `github.com/djahern-max/greenscene_frontend`. The original repo and
its folder (`/Users/dane/code/ryzerecruiting_frontend`) are Dane's preserved
fallback and must never be modified, committed to, or pushed from.

Product direction (Dane, 2026-11-15): the name on screen is **Greenscene**,
domain **greenscene.io** — a multi-tenant recruiting **and onboarding** tool
for green-industry companies (landscaping and similar). Rye Beach Landscaping,
LLC is the intended first real tenant. Onboarding is a future module; nothing
for it exists yet. The code still says RYZE everywhere; rebranding is a
separate queued task.

Facts from a read-only inspection on 2026-11-15 (Claude, chat). **Verify each
against the repo and the machine before writing it down; do not copy on trust.**
The backend spec had one wrong fact (Redis) — assume this one may too.
- `CLAUDE.md` is titled "RYZE.ai Frontend" and describes "a multi-tenant ATS
  for recruiting firms".
- `CLAUDE.md` "Deploy workflow" gives on-server steps. There is no server: the
  droplet that hosted the old product is gone and hosting is TBD.
- `ryze` is a **functional tenant slug** in five places, not just branding:
  `user.tenant_id !== 'ryze'` in `src/components/Header.jsx`,
  `src/pages/CandidateDashboard.jsx` and `src/pages/CandidateSelfProfile.jsx`
  (gates "powered by RYZE" and two tenant-name lines); a `|| 'ryze'` fallback
  in `src/contexts/AuthContext.jsx` (analytics property); and the
  `/ryze/invite` route in `src/App.jsx`.
- `.env`, `.env.example` and `.env.production` are all **tracked in git**, and
  the repo is public. `.env.production` sets `VITE_API_URL=https://api.ryze.ai`
  — a domain Dane sold and no longer controls. A production build today would
  send every API call, including logins, to someone else's domain.
- `src/pages/SaasLanding.jsx` appears to be imported by nothing (`App.jsx`
  routes `/` to `Landing.jsx`).
- The blank template in `context/current-feature.md` hard-codes
  `**Repo:** ryzerecruiting_frontend`.
- The original repo has `context/rebrand-audit.md` (commit `d96ef09`,
  2026-11-02), a read-only classification of every `ryze` occurrence in the
  frontend. It was written against the same `src/` as this fork; five line
  numbers spot-checked on 2026-11-15 matched. It is not in this repo.

## Scope — IN
1. `CLAUDE.md` title and "What this is": Greenscene frontend, the fork origin
   (`ryzerecruiting_frontend` at `758ef68`, 2026-11-15), the product direction,
   the never-touch-the-original rule, and a plain statement that the code still
   says RYZE and the sections below describe the code as it is.
2. `CLAUDE.md` "Deploy workflow": no longer implies a live server. See
   Decision 1.
3. New `CLAUDE.md` section "Local development" with verified facts: repo path
   `/Users/dane/code/greenscene_frontend`; sibling backend at
   `/Users/dane/code/greenscene_backend` must be running on port 8000;
   `npm run dev` serves on `http://localhost:5173`; local `.env` points
   `VITE_API_URL` at `http://localhost:8000`; Google sign-in does not work
   locally (email and password only).
4. New `CLAUDE.md` guardrail, matching the backend's: `ryze` is a functional
   tenant slug in this repo — list the sites by file and count, **no line
   numbers** — and must not be renamed, removed or tidied unless a task's spec
   says so in words. Display-text rebranding and slug changes are different
   jobs. Cross-reference the backend guardrail.
5. New `CLAUDE.md` warning about `.env.production`: it points at a domain Dane
   no longer owns; do not produce or ship a production build until the rebrand
   task changes it. State the fact; change no file.
6. The blank template's `**Repo:**` line reads `greenscene_frontend` (applies
   when this task is archived and the file is reset).
7. `context/rebrand-audit.md` — see Decision 2.
8. Report, without changing anything: whether the "In-progress: API call
   consolidation" section of `CLAUDE.md` agrees with
   `context/parked-apifetch-migration.md`. If they disagree, say how and wait.

## Scope — OUT
- Any change under `src/`, `public/`, `index.html`, `package.json`,
  `package-lock.json`, `vite.config.js`, `eslint.config.js`, or any `.env*`.
- Rebranding of any kind, including the `.env.production` URL itself.
- Deleting or routing `SaasLanding.jsx`.
- `README.md` (still the Vite starter text — note it as a leftover).
- `context/CHANGELOG.md` and `context/dead-code-audit.md` body text (historical
  records). A one-line pre-fork header on `dead-code-audit.md` is allowed:
  "Written for ryzerecruiting_frontend before the 2026-11-15 fork."
- Anything in `greenscene_backend` or either original folder.

### Decisions to flag in the audit (do not pick — present options and wait)
**Decision 1 — old deploy text.** (a) Keep it under a heading marked
  HISTORICAL, with the one rule that outlives the host ("never `npm install`
  on the server before pulling") lifted out as a live rule. (b) Delete it.
  Recommend (a), matching the backend.

**Decision 2 — bring the frontend rebrand audit across.**
  (a) Copy it in read-only from the original repo's history —
      `git -C /Users/dane/code/ryzerecruiting_frontend show d96ef09:context/rebrand-audit.md`
      — add a one-line header saying where it came from, then spot-check at
      least ten rows across its categories against this repo and report any
      mismatch. `git show` reads; it does not modify the original.
  (b) Do not copy it; the rebrand task regenerates an audit from scratch.
  If that commit is not present in the local original folder, say so and stop
  — do not fetch, pull, or check out anything there.
  Recommend (a).

**Decision 3 — the tracked `.env` files.** This task changes none of them.
  For the audit only, state what each tracked `.env*` file contains by variable
  **name** (never print values) and whether any value looks like a secret
  rather than a public client-side setting. Then present: (a) leave as is and
  handle in the rebrand task; (b) open a separate small task to untrack `.env`.
  No recommendation — report and wait.

## Acceptance criteria
- [ ] Audit presented first: current text of each `CLAUDE.md` section to be
      changed, proposed replacement, every fact above marked verified or
      corrected, Decisions 1–3 with options. No edits before Dane confirms.
- [ ] `git diff --stat 758ef68 HEAD` lists only `CLAUDE.md` and files under
      `context/`.
- [ ] `grep -rn "ryzerecruiting_frontend" CLAUDE.md` → hits only in the Origin /
      never-touch-the-original text.
- [ ] `grep -n "on server" CLAUDE.md` → hits only inside the section marked
      HISTORICAL (or none, under Decision 1b).
- [ ] `git -C /Users/dane/code/ryzerecruiting_frontend status --porcelain` and
      `git -C /Users/dane/code/ryzerecruiting_frontend rev-parse HEAD` return
      the same output after the task as before it (proves the original was not
      touched). Record both outputs in the Session log.
- [ ] `git status` clean; commits pushed to `origin/main`.

## Session log
- 2026-11-15 — Task drafted (Claude, chat) from a read-only inspection of
  `greenscene_frontend` at `758ef68`. No code changed.
- 2026-11-15 — Audit presented (Claude Code). Spec facts verified against the
  repo and machine, with two exceptions. (1) Corrected: the rebrand audit
  commit `d96ef09` sits on top of `c6b49a0` ("customizing for new name", six
  `src/` files) in the original, so its tree is not the fork's `src/`; the
  audit's content still matches — all 123 line-numbered rows in categories A–E
  have a `ryze` hit at the stated line here. (2) Not verified: Google sign-in
  locally; sourced to the backend CHANGELOG instead, not tested. Also found:
  `.env.example` is empty; the `/ryze/invite` route is gated by
  `SuperAdminRoute`, the slug gate is on the backend endpoint it calls.
- 2026-11-15 — Dane confirmed Decision 1 (a), Decision 2 (a), Decision 3 (a);
  local `npm run build` allowed as a compile check only.
- 2026-11-15 — `CLAUDE.md` rewritten per Scope items 1–5 (commit `1b96f69`).
  `context/rebrand-audit.md` copied in via `git show` with a provenance header,
  body byte-identical. One-line pre-fork header added to
  `context/dead-code-audit.md`. No `.env*` file and nothing under `src/`
  changed. No build was run.
- 2026-11-15 — Original repo untouched. Before the task:
  `git -C /Users/dane/code/ryzerecruiting_frontend status --porcelain` → empty;
  `rev-parse HEAD` → `758ef68b43cb868274f1427e97ca98236622f323`. After: empty;
  `758ef68b43cb868274f1427e97ca98236622f323`.
- 2026-11-15 — Item 8 reported, nothing changed: `CLAUDE.md` and
  `context/parked-apifetch-migration.md` agree on the file list, order,
  exclusions and per-file check. They differ on (a) `apiFetch` auto-attach and
  402 handling — a goal in `CLAUDE.md`, recorded DONE in the parked file and
  present in `src/services/api.js`; (b) `CLAUDE.md` says "In-progress" and
  never names the parked file; (c) the parked file says to verify against the
  live site, which no longer exists; (d) neither lists `TranscriptModal.jsx` or
  `admin/UsageDashboard.jsx` (both on `apiFetch`) or `DemoRequest.jsx` (raw
  `fetch`).
- 2026-11-15 — Follow-ups, not done here: (1) both repos — the "Session
  workflow" section of `CLAUDE.md` names Status / Goals / Related Files /
  History, but the template uses Objective / Scope / Acceptance criteria /
  Session log; (2) the item 8 differences above; (3) `README.md` is still the
  Vite starter text; (4) Scope item 6 — the blank template's `**Repo:**` line
  becomes `greenscene_frontend` when this task is archived.
