# Current Feature — Narrow rebrand (frontend): "Greenscene" on screen, greenscene.io in config

**Date:** 2026-11-15
**Repo:** greenscene_frontend

## Objective
Rename what a person reads from RYZE / RYZE.ai to **Greenscene**, and move
configuration off domains Dane no longer controls — the frontend half of the
backend task archived today (`greenscene_backend/context/CHANGELOG.md`, top
entry: "Narrow rebrand (backend)"). Read that entry first; this task must end
up consistent with it.

## Context
Source of truth for scope: `context/rebrand-audit.md` in this repo (copied
2026-11-15 from the original repo's `d96ef09`; all 123 line-numbered rows were
confirmed present here on 2026-11-15). Its counts: A 4, B 1, C 73, D 28, E 19.
Of the C rows, 49 are live user-visible text across 24 files; 23 more are in
the unrouted `src/pages/SaasLanding.jsx`.

Decisions already made by Dane (2026-11-15) — do not reopen:
- Name on screen: **Greenscene**, that exact spelling. Domain: greenscene.io.
  Platform sending address: hello@greenscene.io.
- The `ryze` tenant slug stays (GUARDRAIL in `CLAUDE.md`). The five slug sites
  are not edited. This spec authorises no guardrail site.
- Twilio / SMS consent is left alone. On the backend the consent **sentence**
  naming "RYZE GROUP, Inc. (RYZE.ai)" was kept word for word, while the page
  title, wordmark and privacy link around it were updated (backend Decision 1c).
- "RYZE GROUP, Inc." is a legal entity name and stays wherever it appears.
- Display text only. Marketing copy is **renamed, not rewritten** — the
  recruiting-firm wording of the landing page, pricing and dashboards is a
  later product task.

## Scope — IN
1. **B** — the logged-out fallback literal in `src/components/Header.jsx`
   (`user?.tenant_brand_name || 'RYZE.ai'`) becomes `'Greenscene'`.
2. **C, live text** — every live, user-visible "RYZE" / "RYZE.ai" product name
   in the audit's C table outside the exceptions below: `index.html` title and
   meta tags, shared chrome, auth and signup pages, billing and upgrade pages,
   dashboards, chat page, tenant settings, demo request, CSS-generated text.
   This includes the *text* "powered by RYZE" in `Header.jsx` — rename the
   words, leave the `tenant_id !== 'ryze'` comparison that gates it untouched.
3. **`src/components/BookingCard.jsx`** — the SMS-consent sentence stays word
   for word, matching the backend. Any other brand text in that file is
   renamed. Show the exact lines you treat as "the consent sentence".
4. **D, config** — `.env.production` `VITE_API_URL` per Decision 3. Then update
   the production-build warning in `CLAUDE.md` to match the new state.
5. **Legal pages** (`src/pages/PrivacyPolicy.jsx`, `src/pages/TermsOfService.jsx`)
   — only as far as Decision 1 allows.
6. **Logo and favicon** (`public/RYZE_LOGO.png`, `src/assets/RYZE_LOGO.svg`,
   the `index.html` icon link, every import of either file) — only as far as
   Decision 2 allows. Do not design, generate or download a logo.
7. **Landing page** (`src/pages/Landing.jsx`, `Landing.module.css`) — product
   name renamed. The three demo-video URLs on the `ryzerecruiting` DigitalOcean
   Spaces bucket are **not** edited (the bucket name is not a domain and
   renaming it breaks the links). See Decision 4 for the videos themselves.
8. `CLAUDE.md` — only lines this task makes inaccurate ("The code still says
   RYZE", the `.env.production` warning). Show the diff before committing.
   `context/rebrand-audit.md` — one dated line under the header saying which
   categories this task acted on. Tables not rewritten.
9. A final report listing every remaining case-insensitive `ryze` hit under
   `src/`, `public/`, `index.html` and `.env*`, each with its reason.

## Scope — OUT
- The five slug sites (audit Section A; `CLAUDE.md` GUARDRAIL).
- `src/pages/SaasLanding.jsx` — unrouted; leave every line, including the
  LinkedIn permalinks, which must never be edited. Do not delete or route it.
- Rewriting any marketing, pricing or dashboard copy beyond the product name.
- Renaming files, variables, CSS class names, analytics event names or
  PostHog properties (audit category E), except a logo file under Decision 2.
- `.env` (local). `.env.example` is empty; leave it.
- Any change in `greenscene_backend` or either original folder.
- Untracking the `.env*` files (separate task).

### Decisions to flag in the audit (do not pick — present options and wait)
**Decision 1 — the legal pages.** First report, without changing anything,
  what each page says today: the entity it names, how it describes the
  service, any effective date, every contact address and link, and the
  SMS-programme paragraph. Facts to state: both pages give `dane@ryze.ai` as
  the contact for SMS HELP and data-rights requests and link to
  `https://ryze.ai` — a mailbox and a site on a domain Dane no longer
  controls; the backend consent page now links to `{FRONTEND_URL}/privacy`,
  i.e. to this Privacy Policy page.
  Options:
  (a) Leave both pages untouched.
  (b) Change only the dead contact address and website link.
  (c) As (b), and rename the product name ("RYZE.ai") to Greenscene where it
      is used as a product name, keeping "RYZE GROUP, Inc." as the entity.
  **No recommendation — these are legal documents and the choice is Dane's.**
  Under (b) or (c) Dane must name the contact address; it has to be a mailbox
  someone reads. Do not assume `hello@greenscene.io` — it is a sending
  address and may not receive mail. Ask.

**Decision 2 — logo and favicon.** Report what the two image files are, where
  each is used, and whether the image itself contains the old name or mark
  (describe it; do not guess from the filename).
  (a) Dane supplies replacement files; this task wires them in. Say what
      formats and sizes are needed.
  (b) Replace the image with a text wordmark "Greenscene" in the existing
      styles, and remove the favicon link until a real one exists.
  (c) Leave the images as they are for now and list them as a known leftover.
  No recommendation.

**Decision 3 — `.env.production`.** Proposed: `VITE_API_URL=https://api.greenscene.io`
  (the convention the backend's `.env.example` now shows). Hosting is still
  TBD, so this host does not exist yet. State what a production build does
  under the proposal (calls a host that does not resolve — fails safely)
  versus today (calls a host owned by someone else). Dane confirms the value.

**Decision 4 — the landing-page demo videos.** Report what the landing page
  shows and where the three media URLs are used. You cannot watch the videos;
  say so. They were produced for RYZE and very likely show and say the old
  name.
  (a) Leave the video section as is.
  (b) Hide the video section until new media exists (markup kept, not
      rendered) — say exactly what would be hidden.
  No recommendation.

**Decision 5 — anything ambiguous.** If a C row is not clearly a product name
  (for example a sentence where "RYZE" means the old recruiting business, a
  testimonial, a person's title, or text that would read wrongly after a
  plain rename), do not rename it silently. List each with the line, the
  current text and what a plain rename would produce, and wait.

## Acceptance criteria
- [ ] BEFORE any edit: `npm run lint` and `npm run build` results recorded in
      the Session log as the baseline. (A local build is a compile check only;
      `dist/` is never served, uploaded or deployed.)
- [ ] Audit presented first: every site re-located with current and proposed
      text, grouped by page; Decisions 1–5 with options. No edits before Dane
      confirms.
- [ ] No changed line contains `'ryze'`, `"ryze"` or `/ryze/` — the slug
      comparisons, the fallback and the route are untouched. State how checked.
- [ ] `git diff --stat` shows no change to `src/pages/SaasLanding.jsx`.
- [ ] The SMS-consent sentence in `BookingCard.jsx` is byte-identical before
      and after (show the `git diff` for that file).
- [ ] After: `git grep -nIi ryze -- src public index.html .env.production .env.example`
      — every remaining hit appears in the final report with its reason. None
      unexplained.
- [ ] `npm run lint` and `npm run build`: no new errors or warnings versus the
      baseline.
- [ ] A short checklist for Dane of pages to look at in the browser at
      `http://localhost:5173` (landing, login, signup, upgrade, privacy, terms,
      a booking card, the logged-in header), with what he should see on each.
      Claude Code does not claim visual verification it has not done.
- [ ] One concern per commit: display text; config; legal pages (if any);
      logo (if any); docs. `git status` clean; pushed to `origin/main`.

## Session log
- 2026-11-15 — Task drafted (Claude, chat) from `context/rebrand-audit.md` and
  a read-only look at `greenscene_frontend` at `34cf2a3`. No code changed.
- 2026-11-16 — Baseline at `06ee923`, before any edit (Node v26.13.1).
  `npm run lint`: 26 problems (25 errors, 1 warning) in 15 files —
  IntelligenceMessage 1, AuthContext 1, CandidateDashboard 1,
  CandidateProfile 3, CandidateSelfProfile 1, CandidatesPage 1, ChatPage 2
  (+1 warning), CompleteOAuthSignup 1, EmployerDashboard 1, EmployerRoster 2,
  EmployerSelfProfile 1, JobOrderRoster 2, OAuthCallback 1, SaasLanding 1,
  admin/DBExplorer 6. `npm run build`: succeeds, 359 modules, one warning
  (chunk larger than 500 kB). The `dist/` it wrote is gitignored and was not
  served. Audit presented to Dane; no code changed.
- 2026-11-16 — Dane's decisions. 1: beyond (c) — both legal pages written as
  if the operator is Greenscene, LLC and the product Greenscene, including
  the SMS paragraphs and Terms l.163/l.170; contact `hello@greenscene.io`,
  website `https://greenscene.io`, "Last Updated" November 16, 2026; names,
  contacts and dates only. This supersedes "RYZE GROUP, Inc. stays" for
  those two pages only; the `BookingCard.jsx` consent sentence stays
  byte-identical. 2: (c) leave logo files and icon link. 3: confirmed. 4: (b)
  hide the video section. 5: leave "AI FOR RECRUITERS"; rename the
  recruiting-business rows, the plan name, the placeholder and the two-tone
  wordmarks; watermarks renamed only; comments left and reported.
- 2026-11-16 — Display text committed (`1a0d8cb`): 37 lines in 19 files.
  Config committed (`71f2b66`): `.env.production` → `https://api.greenscene.io`.
  Landing video section hidden behind `SHOW_DEMO_VIDEO = false` (`e6b7560`).
- 2026-11-16 — Verification after those three commits plus the uncommitted
  legal-page edits: `npm run lint` 25 errors, 1 warning, per-file counts
  identical to baseline; `npm run build` succeeds, same single chunk-size
  warning. No changed line under `src`, `public`, `index.html` or
  `.env.production` contains `'ryze'`, `"ryze"` or `/ryze/` (grep over
  `git diff -U0 06ee923`). `SaasLanding.jsx` unchanged. `BookingCard.jsx`
  diff is line 186 only.
- 2026-11-16 — Follow-ups recorded at Dane's direction (also in `CLAUDE.md`
  as pre-deploy blockers): Greenscene, LLC does not exist yet; the
  `hello@greenscene.io` mailbox does not exist yet; Twilio registration is
  still under RYZE GROUP, Inc. (new application planned); the consent
  sentence here and on the backend still names RYZE GROUP, Inc.; check the
  Stripe product name before any deploy.
- 2026-11-16 — Dane reviewed the legal-page diff and changed two things: the
  repeated name is dropped ("Greenscene, LLC" alone at Privacy l.56, l.66,
  l.195 and Terms l.111, l.116), and Terms l.163 / l.170 name the company
  ("GREENSCENE, LLC" / "Greenscene, LLC"). `CLAUDE.md` diff approved,
  including the GUARDRAIL-list wording "powered by Greenscene", with a sixth
  pre-deploy blocker added: attorney review of both legal pages. Legal pages
  and docs committed separately and pushed to `origin/main`. Not archived —
  Dane's browser checklist is still open.

