# CLAUDE.md — PPMS (Petronum)

Petrol Pump Management System for Bangladesh. **Single-tenant**: one deployment manages exactly one petrol pump (Petrol, Diesel, Octane). The software is **English-only** (D13).

Read before every task: this file → `TASK_TRACKER.md` (current task) → `DECISIONS.md` (rules that override the original blueprint) → `ARCHITECTURE.md` / `PROJECT_BLUEPRINT.md` as needed.

---

## Stack (finalized — do not replace without explicit approval)

| Layer | Technology |
|---|---|
| Android app | React Native + **JavaScript**, Expo (dev build / prebuild), latest stable SDK (D11) |
| Android offline DB | SQLite (`expo-sqlite`, WAL) |
| Web | React + plain JavaScript (Vite) |
| Backend | Node.js + Express.js, REST |
| Database | PostgreSQL (Knex for queries + migrations) |
| Architecture | Modular monolith |
| Offline sync | Persistent outbox queue + idempotent API |
| Realtime | Socket.IO (notifications only) |
| Printing | Central print agent (Node, cashier laptop) + Xprinter XP-E200M (ESC/POS) |
| Reports | PDF (HTML→PDF) + Excel |
| Security | RBAC + audit trail + device approval |
| Hosting | Local-first (D21); staging = Hostinger KVM 1 with Dokploy from task 5.11 (D22); production at Phase 13; encrypted offsite backups |

### Hard bans
- ❌ TypeScript ❌ Flutter ❌ Next.js ❌ Microservices ❌ ORMs
- ❌ Bangla/i18n libraries — all UI text lives in `packages/shared/strings` (D13)
- ❌ Floats for money or litres — use integer **poisha** (BDT×100) and integer **millilitres** in JS; `NUMERIC` in PostgreSQL
- ❌ Hard deletes of financial records — corrections are reversal entries
- ❌ Custom native code unless approved; no unnecessary packages
- ❌ Implementing pending items: credit approval, credit limits, offline credit, VAT tax invoice

---

## Working rules (mandatory)

1. Work on **only ONE component** (task ID from `TASK_TRACKER.md`) at a time.
2. **Never start the next component without the owner's approval.**
3. Explain the current task briefly before coding.
4. Read existing code before modifying it.
5. Do not rewrite unrelated files.
6. Do not introduce unnecessary packages; justify every new dependency.
7. Do not change finalized business decisions silently — check `DECISIONS.md`.
8. Keep JavaScript readable and modular (JSDoc on exported functions).
9. Validate on both backend and frontend where relevant (shared zod schemas).
10. Test each component before marking it completed.
11. Report what was implemented, tested, and what remains unresolved.
12. Update `TASK_TRACKER.md` after successful completion (status + date + notes).
13. One clear Git commit per finished component: `[<task-id>] <summary>`.
14. Ask before any significant architectural change.
15. If a requirement is pending or unclear, document it in `DECISIONS.md` and do not assume.

**Never claim a component is tested unless the tests were actually run, and say which command ran them.**

A task is **Done** only when every acceptance criterion in `TASK_TRACKER.md` is met.

---

## Response format to the owner

Explanations in **Bengali with English technical terms**, concise (~10–15 lines per component):

1. **Core Components** — what was proposed/implemented
2. **Cautions** — bugs, security risks, dependencies, business-rule conflicts
3. **Decisions** — recommendation, what is finalized, what needs approval

---

## Repository layout (created in task 1.1)

```
apps/api           Express API (modular monolith: modules/<domain>/)
apps/web           React web (owner, manager, supervisor, cashier)
apps/mobile        React Native attendant app (Expo)
apps/print-agent   Node print agent for the cashier laptop
packages/shared    zod schemas, money/qty utils, constants, UI strings
docs/              (optional) extra design notes
```

## Commands
_To be filled in task 1.1–1.8 (install, lint, test, migrate, dev, deploy-staging)._

---

## Non-negotiable domain rules (summary — full list in DECISIONS.md)

- **D1** A sale posts **revenue only**. Stock and FIFO COGS are posted **only at shift-close approval** from verified meter volume. Never deduct stock at sale time.
- **D7** Quick Sale input is **amount only** (BDT, 2 decimals). Litres = amount ÷ effective price, stored to 3 decimals (ml).
- **D2/D6** Meter readings are stored **per nozzle** (cumulative totalizers). Double-nozzle machines use sticky A/B selection.
- Offline sales are never silently lost or duplicated: client UUID primary key, idempotent sync, per-item ack, `review_required` instead of rejection.
- Price validation uses the price **effective at the estimated sale time**, never the price at sync time.
- Attendants never get credit-approval, void, write-off or approval permissions.
- Tolerance-based rules (cash D18, stock D19, test-pump D17): small differences auto-post; above the setting needs approval (owner for write-offs/stock/test-pump over limit).