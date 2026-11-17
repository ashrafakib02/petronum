# TASK_TRACKER.md — PPMS

Single source of truth for the roadmap **and** progress (D4). Roadmap version: **v1.3.1** (2026-11-17 — D21/D22: local-first, staging moved to 5.11–5.13).

**Progress:** 3/148 tasks done · 7/564 base hours
**Estimate:** 564h base · Expected 595h (PERT) · Realistic ~625h · Range 482–834h (4.5 productive h/day)
**Open blockers:** none · **Current task:** _awaiting owner approval → next: 0.3_

## How to update (Claude Code)
1. Before starting: set the task's Status to `In Progress` (only one at a time).
2. After all acceptance criteria pass **and tests were actually run**: set `Done`, fill *Done on*, add a short note (test command + result).
3. If blocked: set `Blocked` and write the reason in Notes; log any new decision in `DECISIONS.md`.
4. Commit with `[<task-id>] <summary>`.

Status values: `Not Started` · `In Progress` · `Blocked` · `Done`
Risk: L ×0.9–1.2 · M ×0.85–1.5 · H ×0.8–2.0 of base hours.

## Demo milestones
| Milestone | Task | Shows |
|---|---|---|
| M1 | 3.12 | Setup wizard + PIN login on a real phone (local, same Wi-Fi) |
| M2 | 7.13 | Offline sale → sync → web; duplicate protection; sticky nozzle |
| M3 | 9.12 | Full shift cycle: duty → sales → handover → nozzle meters → approval → stock/COGS |
| M4 | 12.11 | Central receipt print, live dashboard, PDF/Excel reports |

## Phase 0 — Discovery & Design (3/8 done · 20h)

| ID | Task | Hrs | Risk | Prereq | Status | Done on | Notes | Deliverable | Acceptance criteria |
|---|---|---|---|---|---|---|---|---|---|
| 0.1 | Prepare hardware & site survey checklist | 2 | L | — | Done | 2026-11-16 | Checklist created; lean v2 after owner review | `HARDWARE_SURVEY.md` template | Checklist has a field for each of: machine count, nozzles/machine, meter type, meter decimals, rollover max, tank count & capacity, calibration chart availability, printer model/interface, internet type, attendant phone Android versions |
| 0.2 | Site visit: fill survey with photos | 3 | M | 0.1 | Done | 2026-11-16 | A1/A2 answered; A3/A4 → setup (D12); A5 → policy (D11); printer bought (D14) | Completed `HARDWARE_SURVEY.md` | Every checklist field filled or marked `UNAVAILABLE` with reason; ≥1 photo of each meter type and the calibration chart |
| 0.3 | Money / quantity / time / rounding conventions | 2 | L | — | Not Started |  |  | `DECISIONS.md` entry | Defines: Quick Sale entry = amount only (৳, 2 decimals); litres computed = amount ÷ effective price, stored 3 decimals (integer ml), half-up; timezone rule. Contains 5 worked examples incl. 500÷135=3.704 L and 123.27÷165=0.747 L (reused as fixtures in 1.4) |
| 0.4 | ERD part 1: users, devices, duty, machine assignment, shift, machines, tanks, meters | 3 | M | 0.2 | Not Started |  |  | ERD section in `ARCHITECTURE.md` | Auth session, duty session, machine assignment and business shift are 4 separate tables; meter readings keyed by nozzle (D2); you sign off |
| 0.5 | ERD part 2: sales, stock movements, FIFO layers, journals, print jobs | 3 | M | 0.4 | Not Started |  |  | ERD section in `ARCHITECTURE.md` | Sale table has no stock FK (D1); stock movement types listed; every money column NUMERIC; you sign off |
| 0.6 | Sync protocol specification | 3 | M | 0.5 | Not Started |  |  | Sync section in `ARCHITECTURE.md` | JSON request/response examples for: new batch, duplicate replay, partial failure, review_required, outdated app version |
| 0.7 | Journal posting map | 2 | M | 0.5 | Not Started |  |  | Posting table in `ARCHITECTURE.md` | Each of 11 events (sale, void, purchase, receiving, supplier claim accept, supplier claim reject, shift-close COGS, cash handover, expense, supplier payment, bank deposit) has Dr/Cr accounts and a balanced numeric example |
| 0.8 | Create CLAUDE.md, PROJECT_BLUEPRINT.md, ARCHITECTURE.md, DECISIONS.md, TASK_TRACKER.md | 2 | L | — | Done | 2026-11-17 | CLAUDE.md, PROJECT_BLUEPRINT.md, ARCHITECTURE.md (skeleton), DECISIONS.md, TASK_TRACKER.md created | 5 docs in repo root | CLAUDE.md contains the 15 working rules and stack bans; TASK_TRACKER.md lists every task in this roadmap with status Not Started |

## Phase 1 — Foundation & Early Staging (0/10 done · 29h)

| ID | Task | Hrs | Risk | Prereq | Status | Done on | Notes | Deliverable | Acceptance criteria |
|---|---|---|---|---|---|---|---|---|---|
| 1.1 | Monorepo (npm workspaces), Git, ESLint, Prettier | 3 | L | — | Not Started |  |  | Repo skeleton | `npm install` and `npm run lint` succeed at root with 0 errors; workspaces: api, web, mobile, shared, print-agent |
| 1.2 | Express skeleton: env validation, pino logger, error handler, /health | 4 | L | 1.1 | Not Started |  |  | `apps/api` | Missing required env var → process exits with named error; `GET /health` returns 200 with DB status; unknown route → JSON 404 |
| 1.3 | PostgreSQL + Knex migrations + separate test DB + seed | 4 | L | 1.2 | Not Started |  |  | Migration pipeline | `migrate:latest`, `migrate:rollback`, `seed:run` succeed on dev and test DB; test DB is reset before each test run |
| 1.4 | Shared money/quantity utilities | 3 | M | 1.1, 0.3 | Not Started |  |  | `packages/shared/money` | ≥20 unit tests pass, including the 5 fixtures from 0.3, zero amounts, max values and half-rounding cases |
| 1.5 | Shared zod validation setup used by api and web | 2 | L | 1.4 | Not Started |  |  | `packages/shared/schemas` | One sample schema imported by both api and web; invalid payload rejected identically in a test |
| 1.6 | React web skeleton (Vite) + router + API client | 3 | L | 1.2 | Not Started |  |  | `apps/web` | Home page shows API /health result; production build completes without warnings |
| 1.7 | React Native (Expo dev build) skeleton on a real Android phone | 5 | H | 1.1 | Not Started |  |  | `apps/mobile` debug APK | Uses latest stable Expo SDK with its default minimum Android version (D11, verified from SDK docs at task start); APK installs on the oldest surveyed phone and on one recent phone; app opens in <5 s |
| 1.8 | Test runner + supertest + CI pipeline | 3 | L | 1.3 | Not Started |  |  | CI workflow | CI runs lint + tests on every push; a deliberately failing test turns CI red |
| 1.9 | Centralised UI strings file (English only, D13) for web and mobile | 1 | L | 1.6, 1.7 | Not Started |  |  | `packages/shared/strings` | All skeleton UI text comes from one strings file (no hard-coded text in components, checked by lint rule); no i18n library added |
| 1.13 | Mobile dev build pointed at local API over LAN (D21) | 1 | L | 1.2, 1.7 | Not Started |  |  | Dev-configured APK | Phone on the same Wi-Fi as the dev PC shows local API /health OK via LAN IP; API URL comes from env config, not hard-coded |

## Phase 2 — Authentication & Access (0/14 done · 52h)

| ID | Task | Hrs | Risk | Prereq | Status | Done on | Notes | Deliverable | Acceptance criteria |
|---|---|---|---|---|---|---|---|---|---|
| 2.1 | Users, roles, permissions schema + owner seed | 4 | L | 1.3 | Not Started |  |  | Migration + seed | Roles owner/manager/supervisor/cashier/attendant seeded; owner account created from env; migration test passes |
| 2.2 | Audit log table + helper | 3 | L | 2.1 | Not Started |  |  | `audit_log` + helper | Helper writes actor, action, entity, before, after, timestamp; UPDATE/DELETE on audit_log rejected by DB permission or trigger (tested) |
| 2.3 | Password hashing + web login API (access token) | 3 | L | 2.1 | Not Started |  |  | `POST /auth/login` | Correct password → token; wrong password → 401; passwords never logged (log-capture test) |
| 2.4 | Refresh token rotation, logout, revoke | 4 | M | 2.3 | Not Started |  |  | Token lifecycle API | Reused refresh token → 401 and family revoked; logged-out token rejected; tests pass |
| 2.5 | RBAC middleware + permission-matrix test | 3 | M | 2.4 | Not Started |  |  | RBAC middleware | Matrix test covers every role × every protected route; attendant gets 403 on 100% of web-admin routes |
| 2.6 | Web login screen + protected routes + role menu | 4 | L | 2.5, 1.6 | Not Started |  |  | Login UI | Unauthenticated visit redirects to login; menu shows only permitted items per role (manual check per role recorded) |
| 2.7 | Device registration API | 3 | M | 2.5 | Not Started |  |  | Device API | New device creates `pending` record with its Android version and app version recorded; same device re-registering does not duplicate |
| 2.8 | Device approval web UI | 3 | L | 2.7, 2.6 | Not Started |  |  | Devices page | Supervisor approves/blocks device; action audited; blocked device login → 403 |
| 2.9 | Mobile PIN login API (hash, rate limit, lockout) | 5 | M | 2.7 | Not Started |  |  | `POST /auth/pin` | 5 wrong PINs → lockout (default 15 min, configurable), 6th attempt returns 423; unapproved device → 403; every attempt audited |
| 2.10 | RN login screen + secure token storage | 4 | M | 2.9, 1.13 | Not Started |  |  | Login screen | Token stored in secure storage (not AsyncStorage) — verified in code review; app restart keeps session |
| 2.11 | Optional biometric unlock | 3 | H | 2.10 | Not Started |  |  | Biometric toggle | Works on ≥1 phone with fingerprint; phones without biometrics fall back to PIN with no crash |
| 2.12 | Offline re-login with cached credential + expiry | 5 | H | 2.10 | Not Started |  |  | Offline login | Window read from system setting (default 12h, D3): offline login at 11h59m succeeds, at 12h01m fails, measured by monotonic clock (changing phone clock has no effect) |
| 2.13 | User management web UI (create, PIN reset, deactivate/revoke) | 5 | L | 2.6, 2.9 | Not Started |  |  | Users page | Revoked user's existing tokens rejected within 1 request; PIN reset forces new PIN; all actions audited |
| 2.14 | Authentication integration tests | 3 | L | 2.4, 2.5, 2.9, 2.12, 2.13 | Not Started |  |  | Test suite | Lockout, revoke, token expiry, offline window and RBAC suites all pass in CI |

## Phase 3 — Pump Configuration (0/12 done · 41h)

| ID | Task | Hrs | Risk | Prereq | Status | Done on | Notes | Deliverable | Acceptance criteria |
|---|---|---|---|---|---|---|---|---|---|
| 3.1 | Pump profile | 3 | L | 2.6 | Not Started |  |  | Profile page | Name, address, phone, receipt header saved and returned by API; non-owner cannot edit (403) |
| 3.2 | Fuel types seed + price version schema/API | 4 | M | 3.1 | Not Started |  |  | Price API | Exactly 3 fuels seeded; overlapping effective periods rejected; lookup by timestamp returns correct version (tests incl. exact boundary second) |
| 3.3 | Price history web UI with back-date permission | 3 | L | 3.2, 2.5 | Not Started |  |  | Price page | Back-dated price requires permission; change audited with old/new values |
| 3.4 | Tank CRUD | 3 | L | 3.1 | Not Started |  |  | Tanks page | Tank has fuel type & capacity; deactivate instead of delete |
| 3.5 | Tank dip mode (litre-marked or chart) + calibration chart + interpolation (D12) | 4 | H | 3.4, 0.2 | Not Started |  |  | Calibration module | Per-tank mode chosen in setup and editable later by owner: (a) litre-marked dipstick → direct entry, or (b) cm/mm + chart → interpolated litres within 0.1% of chart at ≥10 test points; out-of-range dip rejected; changing mode/chart is audited and never changes litres of past dip readings (stored at entry time) |
| 3.6 | Dispenser machine + nozzle CRUD with tank link | 4 | M | 3.4, 3.2, 0.4 | Not Started |  |  | Machines page | Machine with nozzles of different fuel types rejected; machine–tank fuel mismatch rejected |
| 3.7 | Meter metadata (digits/rollover) + dispensed-volume calculator | 3 | H | 3.6, 1.4 | Not Started |  |  | Meter util | Readings accept up to 3 decimals; tests pass for normal and rollover (e.g. 999999.99→0.50) cases |
| 3.8 | Meter replacement / reset event | 3 | M | 3.7 | Not Started |  |  | Reset event | Volume across a replacement equals old-meter final segment + new-meter segment (test) |
| 3.9 | Config pull API for mobile | 3 | L | 3.6, 3.2, 2.9 | Not Started |  |  | `GET /mobile/config` | Returns machines, nozzles, fuel types and price versions; ETag/version lets unchanged config return 304 |
| 3.10 | System settings: business day start, offline-login window, variance tolerances, test-pump daily limit | 4 | L | 3.1, 2.5 | Not Started |  |  | Settings page + API | Business day start (default 00:00), offline window (default 12h), variance tolerances, per-nozzle daily test-pump limit editable by owner only (403 for others); every change audited; values readable by API and mobile config; stock tolerance (D19) can be entered either as % or as 'X litres per 100 L' — both stay in sync, stored as one value, and UI shows a live example (e.g. 0.5% = 0.5 L per 100 L = 25 L on 5,000 L sold) |
| 3.11 | First-run Setup Wizard (pump → settings → prices → tanks → machines/nozzles A/B → tolerances → employees) | 5 | M | 3.10, 3.3, 3.5, 3.8, 2.13 | Not Started |  |  | Wizard UI | Fresh install with only owner account redirects to wizard; tolerance step offers % or 'per 100 L' input; completing all steps creates a configured pump with 0 validation errors; wizard can be resumed after browser close; later phases append steps (shift templates 6.1, opening balances 13.10, printer test 11.9) |
| 3.12 | **DEMO M1 — setup wizard + real phone login (local, same Wi-Fi)** (M1) | 2 | L | 3.11, 3.9, 2.10 | Not Started |  |  | Demo + notes in TASK_TRACKER | Live on dev PC: run wizard on fresh DB, approve device, PIN login on phone over local Wi-Fi, show configured machines/nozzles/prices |

## Phase 4 — Ledger Foundation (0/3 done · 10h)

| ID | Task | Hrs | Risk | Prereq | Status | Done on | Notes | Deliverable | Acceptance criteria |
|---|---|---|---|---|---|---|---|---|---|
| 4.1 | Chart of accounts seed | 2 | L | 0.7, 1.3 | Not Started |  |  | Accounts seed | All accounts referenced in the 0.7 posting map exist |
| 4.2 | Journal tables + posting service | 5 | M | 4.1 | Not Started |  |  | Posting service | Unbalanced entry rejected; posted lines cannot be updated (test); posting runs inside caller's DB transaction |
| 4.3 | Reversal entries + ledger invariant tests | 3 | M | 4.2 | Not Started |  |  | Reversal + tests | Reversal produces exact opposite lines linked to original; sum(debit)=sum(credit) invariant test passes on 1,000 random entries |

## Phase 5 — Offline Sync Infrastructure (0/14 done · 48h)

| ID | Task | Hrs | Risk | Prereq | Status | Done on | Notes | Deliverable | Acceptance criteria |
|---|---|---|---|---|---|---|---|---|---|
| 5.1 | RN SQLite setup (WAL, local migrations) | 4 | H | 1.7 | Not Started |  |  | Local DB module | Local migrations run on first launch and upgrade; data survives force-stop and phone reboot (manual test logged) |
| 5.2 | Mobile config cache | 2 | L | 5.1, 3.9 | Not Started |  |  | Cached config | With airplane mode on after one sync, machine list and prices still load |
| 5.3 | Outbox table + state machine | 4 | M | 5.1 | Not Started |  |  | Outbox module | States pending/sending/synced/review_required/failed; illegal transitions throw (unit tests) |
| 5.4 | Outbox persistence tests (logout, restart, reinstall warning) | 2 | M | 5.3 | Not Started |  |  | Test log | 50 pending items survive logout + app restart; logout with pending items shows warning, never deletes |
| 5.5 | Server sync endpoint (batch, idempotent UUID, per-item ack) | 5 | M | 2.9, 0.6 | Not Started |  |  | `POST /sync` | Same batch sent 3× creates records once and returns identical acks; one bad item does not reject the others |
| 5.6 | Min app version + device check on sync | 2 | L | 5.5 | Not Started |  |  | Version gate | Outdated version receives 426 with message; items stay pending on device |
| 5.7 | Sync worker (backoff, connectivity, foreground, manual) | 5 | M | 5.3, 5.5 | Not Started |  |  | Sync worker | After 30 min offline with 100 items, reconnect drains queue with no duplicates; backoff capped (test) |
| 5.8 | Client clock model (server offset + monotonic elapsed) | 3 | H | 5.7 | Not Started |  |  | Clock module | Phone clock moved ±2h after sync: estimated time stays within 1 min of true time (test) |
| 5.9 | Server time estimation + tolerance → review flag | 3 | M | 5.8, 5.5 | Not Started |  |  | Server validator | Estimated time outside tolerance (default 10 min, configurable) → item status review_required |
| 5.10 | Sync status UI | 3 | L | 5.7 | Not Started |  |  | Badge + screen | Shows pending count and last successful sync time; updates within 2 s of change |
| 5.11 | Staging VPS (Hostinger KVM 1): wipe, Ubuntu 24.04, hardening, install Dokploy (D21, D22) | 3 | M | 5.10 | Not Started |  |  | Staging server + Dokploy | SSH key-only login, root password login disabled, firewall allows only 22/80/443 (+ Dokploy port restricted); Dokploy dashboard reachable over HTTPS; verified by attempting password SSH (rejected) |
| 5.12 | Dockerfiles (api, web) + Dokploy app/PostgreSQL setup with TLS and WebSocket | 4 | M | 5.11, 1.2, 1.6 | Not Started |  |  | Staging HTTPS API + web | `https://<staging>/health` 200 with valid certificate; web loads; Socket.IO test connection succeeds through proxy; PostgreSQL not exposed publicly; container restarts automatically after kill |
| 5.13 | Staging deploy with migrations + mobile build pointed at staging | 3 | M | 5.12, 1.3, 1.13 | Not Started |  |  | Repeatable deploy | Push/deploy runs migrations automatically; second deploy is a no-op with no errors; phone shows staging /health OK on pump-style Wi-Fi AND on mobile data |
| 5.14 | Real-device offline test suite against staging | 5 | H | 5.2, 5.4, 5.6, 5.9, 5.10, 5.13 | Not Started |  |  | Test report | 12 scripted scenarios pass on ≥2 phones incl. router power-off, Wi-Fi up but no internet, Wi-Fi↔mobile-data switch mid-sync, airplane, kill, reboot, logout, version gate; 0 lost, 0 duplicate items |

## Phase 6 — Shift & Duty (0/6 done · 27h)

| ID | Task | Hrs | Risk | Prereq | Status | Done on | Notes | Deliverable | Acceptance criteria |
|---|---|---|---|---|---|---|---|---|---|
| 6.1 | Business shift open/close skeleton (midnight-crossing) | 5 | M | 3.6, 3.10 | Not Started |  |  | Shift API + web page | Shift 22:00–06:00 gets business_date per business-day setting; only one open shift at a time; shift-template step added to setup wizard |
| 6.2 | Meter reading entry (nozzle storage, machine-level UI) | 5 | M | 6.1, 3.8 | Not Started |  |  | Meter entry page | Reading lower than previous rejected unless reset event exists; machine total = sum of nozzle movements (test) |
| 6.3 | Duty event handlers on server (start / change machine / end) | 4 | M | 5.5, 6.1 | Not Started |  |  | Duty API | One active duty per user; change machine closes old assignment and opens new one at same timestamp |
| 6.4 | Duty events through mobile outbox | 3 | M | 6.3, 5.7 | Not Started |  |  | Offline duty | Duty start made offline syncs later with original estimated time |
| 6.5 | RN machine + nozzle selection (sticky, D6), duty screen, one-tap logout | 6 | L | 6.4, 5.2 | Not Started |  |  | Duty screens | After login, machine selection shown before any sale; double-nozzle machine asks nozzle A/B once; single-nozzle asks nothing; logout ends duty in ≤1 tap and keeps outbox |
| 6.6 | Duty conflict rules + tests | 4 | M | 6.3 | Not Started |  |  | Rules + tests | Overlapping duty or post-revocation duty → review_required, never dropped (tests) |

## Phase 7 — Fuel Sales (0/13 done · 48h)

| ID | Task | Hrs | Risk | Prereq | Status | Done on | Notes | Deliverable | Acceptance criteria |
|---|---|---|---|---|---|---|---|---|---|
| 7.1 | Sales schema | 3 | L | 6.3, 0.5 | Not Started |  |  | Migration | Client UUID PK, price snapshot, duty, machine, nozzle, amount (2 dp), computed litres (3 dp), device time, estimated time, status; no stock columns (D1) |
| 7.2 | Sale ingestion validation (schema, duty window, machine) | 4 | M | 7.1, 5.9 | Not Started |  |  | Ingestion step 1 | Sale outside its duty window or on wrong machine → review_required (tests) |
| 7.3 | Price-version validation | 3 | M | 7.2, 3.2 | Not Started |  |  | Ingestion step 2 | Sale whose snapshot ≠ price at estimated time → review_required; sale is never silently repriced (test) |
| 7.4 | Revenue journal posting on ingestion | 3 | M | 7.2, 4.2 | Not Started |  |  | Ingestion step 3 | Accepted sale posts Dr Cash-with-attendant / Cr Fuel Sales in same transaction; creates zero stock movements (test) |
| 7.5 | RN Quick Sale screen (amount-only entry D7, sticky nozzle D6) | 6 | M | 7.1, 5.2, 1.4 | Not Started |  |  | Quick Sale UI | Active machine preselected; double-nozzle shows A/B toggle preselected to last choice (1 tap to switch), single-nozzle shows none; amount entry only, litres shown read-only and match 0.3 fixtures; cash default |
| 7.6 | Quick Sale save to outbox + receipt flag | 3 | M | 7.5, 5.3 | Not Started |  |  | Save flow | Normal cash sale ≤3 taps after dispensing on single-nozzle (and on double-nozzle without nozzle change); saved in <300 ms offline; receipt checkbox enabled only when online; disabled (greyed) when offline (D20) |
| 7.7 | Device today's-sales list with sync state | 3 | L | 7.6 | Not Started |  |  | Sales list screen | Lists today's sales with pending/synced/review badge; totals match local DB |
| 7.8 | Web sales list + filters | 4 | L | 7.2 | Not Started |  |  | Sales page | Filter by date, machine, employee, status; 10,000 seeded sales load page in <2 s on staging |
| 7.9 | Review queue web UI | 5 | M | 7.3 | Not Started |  |  | Review page | Supervisor approves/corrects review_required items; every decision audited with reason |
| 7.10 | Void/correction request from attendant | 3 | M | 7.6 | Not Started |  |  | Request flow | Attendant can request but not apply a void (403 on direct void API) |
| 7.11 | Supervisor approval + reversal journal | 4 | M | 7.10, 4.3, 7.9 | Not Started |  |  | Approval flow | Approved void posts exact reversal; rejected void leaves sale unchanged; both audited |
| 7.12 | Sales integration tests | 5 | M | 7.4, 7.8, 7.11, 7.7 | Not Started |  |  | Test suite | Suites pass: duplicate replay, late sync (48h), price boundary, revoked user, wrong clock |
| 7.13 | **DEMO M2 — offline sale → sync → web, duplicate protection** (M2) | 2 | L | 7.12 | Not Started |  |  | Demo + notes | Live: 5 sales in airplane mode, reconnect, all 5 appear on web once; forced replay creates no duplicates |

## Phase 8 — Purchase & Inventory (0/12 done · 48h)

| ID | Task | Hrs | Risk | Prereq | Status | Done on | Notes | Deliverable | Acceptance criteria |
|---|---|---|---|---|---|---|---|---|---|
| 8.1 | Supplier master + search API | 5 | L | 2.6 | Not Started |  |  | Suppliers page | CRUD with deactivate instead of delete; search by partial name/phone returns matches in <300 ms on 1,000 suppliers; duplicate name warning |
| 8.2 | Purchase invoice entry (supplier search, autofill, inline add) + AP journal | 6 | M | 8.1, 4.2 | Not Started |  |  | Purchase page | Typing ≥2 letters lists suppliers; selecting one autofills its details; 'add new' creates supplier without leaving the form; invoice posts balanced AP entry; total = qty × rate within 1 poisha |
| 8.3 | Dip reading entry with calibration | 3 | L | 3.5 | Not Started |  |  | Dip page | Entering dip shows litres from 3.5; reading timestamped and attributed |
| 8.4 | Stock movement ledger (append-only) | 4 | M | 0.5, 3.4 | Not Started |  |  | Stock ledger | Types opening/receipt/verified_dispense/adjustment; UPDATE/DELETE rejected (test) |
| 8.5 | Fuel receiving (dip before/after, received qty) + supplier claim for shortage (D15) | 5 | H | 8.2, 8.3, 8.4 | Not Started |  |  | Receiving page | Received qty from dips recorded; invoice qty − received qty > 0 creates an open supplier claim (qty × invoice rate); inventory layer uses received qty only; journal balanced (Dr Inventory + Dr Supplier claim receivable / Cr Supplier payable) — test with 9,000 L invoiced vs 8,950 L received |
| 8.6 | FIFO layer creation on receiving | 3 | M | 8.5 | Not Started |  |  | Layer creation | Each receipt creates one layer per fuel type with qty and unit cost (D1: fuel-type level) |
| 8.7 | FIFO consumption with row locks | 5 | H | 8.6 | Not Started |  |  | FIFO consume | Consumption spanning 3 layers produces exact cost; 2 concurrent consumptions never over-consume a layer (concurrency test) |
| 8.8 | FIFO reversal | 3 | M | 8.7 | Not Started |  |  | FIFO reverse | Reversal restores layer remaining quantities exactly (test) |
| 8.9 | FIFO test suite | 5 | M | 8.7, 8.8 | Not Started |  |  | Test suite | ≥15 cases incl. partial layers, empty stock error, reversal after later consumption — all pass |
| 8.10 | Opening stock & opening cost layers | 3 | L | 8.6 | Not Started |  |  | Opening entry | Opening value report equals entered figures to the poisha |
| 8.11 | Provisional stock calculation (D1) | 3 | M | 8.4, 7.2 | Not Started |  |  | Stock status service | Provisional = last verified + receipts − unverified sales volume; shown separately from verified (test with known data) |
| 8.12 | Supplier claim resolution (accept / reject) | 3 | M | 8.5, 4.3 | Not Started |  |  | Claims page | Open claims listed per supplier; Accept → reduces supplier payable (or records cash refund); Reject → posts transit-loss expense; partial accept supported; every resolution audited and balanced (tests for all 3 paths) |

## Phase 9 — Cash, Reconciliation & Shift Close (0/14 done · 52h)

| ID | Task | Hrs | Risk | Prereq | Status | Done on | Notes | Deliverable | Acceptance criteria |
|---|---|---|---|---|---|---|---|---|---|
| 9.1 | Cash handover API + journal (multiple partial handovers per duty, D16) | 4 | M | 7.4 | Not Started |  |  | Handover API | Attendant can hand over any number of times during duty; each handover records amount, receiver (logged-in cashier/supervisor) and time; expected cash on hand = accepted sales − handovers so far; final handover at duty end shows remaining difference, stored, not written off (tests: 3 partial + 1 final) |
| 9.2 | Cash handover UI (cashier, web) | 3 | L | 9.1 | Not Started |  |  | Handover page | Cashier sees expected amount per attendant; handover audited |
| 9.3 | Closing meter readings + dispensed volume | 3 | M | 6.2 | Not Started |  |  | Closing entry | Dispensed volume per machine computed via 3.7 util; matches manual calculation for survey sample |
| 9.13 | Test-pump entry by supervisor + daily limit + owner approval (D17) | 3 | M | 9.3, 2.5, 3.10 | Not Started |  |  | Test-pump page | Supervisor records nozzle + litres; volume within per-nozzle daily limit (setting) is excluded from sales-vs-meter and treated as returned to tank; above limit stays 'pending' until owner approves (supervisor gets 403 on approve); every entry shows who/when/nozzle/litres and is audited |
| 9.4 | Sales vs meter comparison per nozzle and machine | 4 | M | 9.3, 7.2, 9.13 | Not Started |  |  | Comparison view | Per nozzle: meter litres vs sales litres, and meter litres × effective price vs entered amount; variance shown in litres, ৳ and %; within-tolerance differences (setting) marked OK; approved test-pump volume (9.13) excluded |
| 9.5 | Shift close review screen | 5 | M | 9.2, 9.4 | Not Started |  |  | Review screen | Shows meter volume, sales volume, cash expected/counted, variances for the shift on one screen |
| 9.6 | Shift approval, lock, late sales → review | 4 | M | 9.5 | Not Started |  |  | Approval flow | Approved shift rejects edits (test); sale synced after close → review_required, not lost |
| 9.7 | Verified dispense → stock movement + FIFO COGS journal | 5 | H | 9.6, 8.7 | Not Started |  |  | Shift-close posting | Stock deducted exactly once per approved shift (re-approval attempt is idempotent); COGS journal balanced |
| 9.8 | Cash variance handling (tolerance + attendant due / owner-approved write-off, D18) | 4 | M | 9.6 | Not Started |  |  | Cash variance flow | Difference within cash tolerance setting (default ±20 BDT) auto-posts to cash short/over account; above tolerance supervisor chooses 'attendant due' or 'write-off'; write-off stays pending until owner approves (supervisor 403); cash over always posts to other income; tests for all 4 paths |
| 9.14 | Attendant dues ledger + repayment entry | 2 | L | 9.8 | Not Started |  |  | Dues page | Outstanding due per attendant = dues − repayments (test); repayment entry by cashier posts balanced journal; visible on owner dashboard data |
| 9.9 | Stock variance handling (tolerance auto-post, above → owner approval, D19) | 3 | M | 9.7 | Not Started |  |  | Stock variance flow | Variance within stock tolerance auto-posts: loss at FIFO cost (oldest layers), gain as new layer at latest purchase cost; above tolerance stays pending (stock not adjusted) until owner approves, with dashboard alert; supervisor 403 on approve; tests for loss/gain × within/above |
| 9.10 | Daily tank reconciliation (book vs dip) | 4 | M | 9.7, 8.3 | Not Started |  |  | Tank reconciliation | Book stock vs dip stock per tank per day; difference compared with stock tolerance setting and routed to 9.9 |
| 9.11 | Full-day reconciliation E2E test | 6 | M | 9.8, 9.9, 9.10, 9.14 | Not Started |  |  | E2E test | Scripted day (2 shifts, 3 attendants, 1 receipt, midnight crossing) — trial balance and stock both reconcile to the poisha/ml |
| 9.12 | **DEMO M3 — complete shift cycle** (M3) | 2 | L | 9.11 | Not Started |  |  | Demo + notes | Live: open shift → duty → sales → handover → closing meters → approve → stock & COGS updated |

## Phase 10 — Accounts (0/7 done · 28h)

| ID | Task | Hrs | Risk | Prereq | Status | Done on | Notes | Deliverable | Acceptance criteria |
|---|---|---|---|---|---|---|---|---|---|
| 10.1 | Expense categories + expense entry + journal | 5 | L | 4.2 | Not Started |  |  | Expenses page | Expense posts balanced entry; category required; audited |
| 10.2 | Supplier payment + journal | 3 | L | 8.2 | Not Started |  |  | Payment page | Payment reduces supplier payable; overpayment warning |
| 10.3 | Supplier ledger view | 3 | L | 10.2 | Not Started |  |  | Supplier ledger | Running balance equals sum of invoices − payments − accepted claims (test); open claims shown separately |
| 10.4 | Cashbook (attendant / cashier / safe positions) | 5 | M | 9.1 | Not Started |  |  | Cashbook | Position totals equal cash ledger accounts (test) |
| 10.5 | Bank deposit / owner withdrawal entries | 3 | L | 10.4 | Not Started |  |  | Entries | Deposit moves cash safe → bank; withdrawal reduces equity; both balanced |
| 10.6 | Trial balance + integrity checks | 4 | M | 10.1, 10.3, 10.5 | Not Started |  |  | TB report | TB balances on seeded 30-day dataset; integrity check lists 0 orphan journals |
| 10.7 | Profit & Loss (gross, operating, net) | 5 | M | 10.6, 9.7 | Not Started |  |  | P&L | P&L on seeded data matches hand-calculated expected values; periods with unverified shifts labelled Provisional |

## Phase 11 — Central Printing (0/10 done · 34h)

| ID | Task | Hrs | Risk | Prereq | Status | Done on | Notes | Deliverable | Acceptance criteria |
|---|---|---|---|---|---|---|---|---|---|
| 11.1 | Print job schema + create on sale ingestion | 3 | L | 7.6, 7.2 | Not Started |  |  | Print jobs | Receipt-flagged sale made online creates exactly one original job; sale made offline never auto-creates a print job after sync (D20) |
| 11.2 | Claim/lease + ack API (idempotent) | 3 | M | 11.1 | Not Started |  |  | Agent API | Two agents claiming same job → only one succeeds; unacked lease expires and job is re-claimable |
| 11.3 | Print agent: device auth + polling | 4 | M | 11.2 | Not Started |  |  | `apps/print-agent` | Unauthorized agent → 401; authorized agent picks a job within 10 s via polling alone |
| 11.4 | Socket.IO notification (server + agent) | 3 | M | 11.3, 5.12 | Not Started |  |  | Realtime notify | Job picked within 2 s via socket; with socket disabled, polling still prints (test) |
| 11.5 | ESC/POS output on Xprinter XP-E200M (LAN) | 4 | H | 11.3, 0.2 | Not Started |  |  | Printer driver | Test receipt prints correctly on the XP-E200M over LAN at 58 mm mode (and 80 mm mode); auto-cut works |
| 11.6 | English receipt template (58 mm / 32-col base, 80 mm optional — D14) | 3 | L | 11.5 | Not Started |  |  | Receipt template | Paper width is a printer setting (58 mm = 32 cols default, 80 mm = 48 cols); same receipt fits both without wrapping key lines; shows pump header, receipt no, date/time, fuel, nozzle, amount, litres, price; header says Cash Receipt (not tax invoice); prints in <5 s |
| 11.7 | Controlled reprint + receipt search (web) | 5 | L | 11.2, 2.5 | Not Started |  |  | Receipts page | Reprint requires permission, prints 'COPY', audited; search by date/amount/receipt no |
| 11.10 | One-time 'Print Receipt' for synced offline sales (phone list + web) | 2 | L | 11.2, 11.7, 7.7 | Not Started |  |  | One-time print button | Button appears on a synced offline sale in both attendant's sales list and cashier web; first use from either place prints the original and removes the button everywhere (test with concurrent clicks: exactly 1 original); further receipts only via controlled COPY reprint (11.7) |
| 11.8 | Printer failure tests | 4 | M | 11.4, 11.6, 11.10 | Not Started |  |  | Test report | Paper-out, printer-off, agent-offline, network-drop scenarios: 0 lost, 0 duplicate originals |
| 11.9 | Print agent as OS service | 3 | M | 11.3 | Not Started |  |  | Service install guide | Agent auto-starts after PC reboot and reconnects without user action |

## Phase 12 — Reports & Dashboard (0/11 done · 46h)

| ID | Task | Hrs | Risk | Prereq | Status | Done on | Notes | Deliverable | Acceptance criteria |
|---|---|---|---|---|---|---|---|---|---|
| 12.1 | Report query layer + business-day filters | 4 | M | 6.1, 3.10 | Not Started |  |  | Report module | Midnight-crossing shift counted in correct business day (test) |
| 12.2 | Owner dashboard data API | 4 | M | 12.1, 8.11, 10.4, 7.9 | Not Started |  |  | Dashboard API | Returns today's sales by fuel, cash positions, provisional/verified stock, pending sync & review counts, today's test-pump entries (who/nozzle/litres); responds <1 s on 30-day data |
| 12.3 | Owner dashboard UI + Socket.IO refresh | 4 | L | 12.2, 11.4 | Not Started |  |  | Dashboard page | New synced sale appears on dashboard within 5 s without reload |
| 12.4 | Sales reports (daily, fuel, machine, employee) | 6 | L | 12.1 | Not Started |  |  | Sales reports | Report totals equal Fuel Sales ledger for the same period (test) |
| 12.5 | Shift & meter reconciliation reports | 5 | L | 9.6, 12.1 | Not Started |  |  | Shift reports | Values identical to approved shift-close screen for 3 sample shifts |
| 12.6 | Inventory & purchase reports | 5 | L | 8.11, 9.7, 12.1 | Not Started |  |  | Inventory reports | Closing stock equals stock ledger balance; purchase totals equal AP postings |
| 12.7 | Accounting reports (supplier ledger, expenses, cash, COGS, P&L) | 6 | M | 10.7, 10.3 | Not Started |  |  | Accounting reports | Each report total ties to trial balance (test) |
| 12.8 | PDF engine (HTML→PDF) | 3 | L | 12.4 | Not Started |  |  | PDF service | 30-day report PDF generated in <10 s with correct page breaks and pump header |
| 12.9 | PDF templates for all reports | 3 | L | 12.8, 12.5, 12.6, 12.7 | Not Started |  |  | PDF exports | Every report in 12.4–12.7 exports to PDF with pump header and date range |
| 12.10 | Excel export | 4 | L | 12.4, 12.5, 12.6, 12.7 | Not Started |  |  | XLSX exports | Numbers stored as numeric cells (not text); totals match on-screen report |
| 12.11 | **DEMO M4 — printing, dashboard, reports** (M4) | 2 | L | 12.3, 12.9, 12.10, 11.8 | Not Started |  |  | Demo + notes | Live: sale with receipt prints centrally, dashboard updates, PDF & Excel exported |

## Phase 13 — Production, Security & Pilot (0/14 done · 81h)

| ID | Task | Hrs | Risk | Prereq | Status | Done on | Notes | Deliverable | Acceptance criteria |
|---|---|---|---|---|---|---|---|---|---|
| 13.1 | Production server from staging setup (Dokploy) | 3 | L | 5.13 | Not Started |  |  | Production server | Same hardening checks as 5.11 pass; HTTPS health OK; separate DB and secrets from staging |
| 13.2 | Production deploy, secrets, rollback procedure | 3 | M | 13.1 | Not Started |  |  | Deploy + rollback doc | Rollback to previous release tested once successfully |
| 13.3 | Encrypted offsite backup job | 3 | M | 13.2 | Not Started |  |  | Backup job | Nightly encrypted dump stored on a different provider; backup file unreadable without key (checked) |
| 13.4 | Restore drill to a fresh server | 3 | M | 13.3 | Not Started |  |  | Restore report | Restore completes; row counts of 5 key tables match source; time-to-restore recorded |
| 13.5 | Monitoring & alerts | 3 | L | 13.2 | Not Started |  |  | Alerts | Downtime, error-rate and disk >80% alerts each fired once in test and received |
| 13.6 | Security review + finalize D3 | 4 | M | 2.14, 13.2 | Not Started |  |  | Security checklist | Dependency audit: 0 high/critical; rate limits verified; RBAC matrix re-run; offline-login default (12h) confirmed and D3 marked Final in DECISIONS.md |
| 13.7 | APK signing, distribution, forced update | 5 | M | 5.6 | Not Started |  |  | Release process | Signed release APK installs; older version is blocked from sync with a clear English update message; before enforcing, report shows 0 approved devices below the new SDK's minimum Android version (D11) |
| 13.8 | UAT scripts | 4 | L | 12.11 | Not Started |  |  | UAT document | ≥25 scripted cases covering every role, each with expected result |
| 13.9 | UAT execution with real data | 4 | M | 13.8, 13.2 | Not Started |  |  | UAT report | All UAT cases pass or have a logged, accepted defect |
| 13.10 | Go-live opening balances (stock, cash, supplier) | 3 | M | 8.10, 10.2, 13.2 | Not Started |  |  | Opening entries | Opening trial balance approved by owner in writing |
| 13.11 | User manual (English, simple) | 4 | L | 13.9 | Not Started |  |  | Manual (PDF) | Covers login, duty, Quick Sale, logout, sync status, handover in plain English with screenshots; ≤12 pages |
| 13.12 | Training session | 2 | L | 13.11 | Not Started |  |  | Training record | Each attendant completes a test sale unaided within 10 minutes |
| 13.13 | Pilot week 1 — parallel run with paper (time-boxed fix budget) | 20 | H | 13.9, 13.10, 13.12, 13.7 | Not Started |  |  | Pilot log | Daily paper vs system totals compared; every mismatch logged with root cause |
| 13.14 | Pilot week 2+ — fixes + workload measurement (time-boxed) | 20 | H | 13.13 | Not Started |  |  | Pilot report | 5 consecutive days with zero unexplained mismatches; admin time before/after measured against 50–60% goal |