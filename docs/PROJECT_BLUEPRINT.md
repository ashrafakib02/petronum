# PROJECT_BLUEPRINT.md — PPMS

Stable business requirements. Where `DECISIONS.md` differs, **DECISIONS.md wins** (references shown as Dn).

## 1. Overview
- **Product:** Petrol Pump Management System (PPMS), project name Petronum
- **Market:** Bangladesh · **Language:** English-only (D13)
- **Architecture:** Single-tenant — one deployment = one pump
- **Fuels:** Petrol, Diesel, Octane
- **Goal:** Reduce manual administrative work by ~50–60% (measured during pilot, task 13.14)
- **Users:** Owner, Manager, Supervisor, Cashier (web); Attendants (Android, personal phones)
- Attendant UI must be extremely simple.

## 2. Scope
Individual fuel sales · fuel purchasing & receiving · tanks & stock · dispenser machines/nozzles · employee duty & flexible shifts · cash collection & expenses · supplier accounting · inventory reconciliation · FIFO costing · P&L · dashboard & printable reports · central receipt printing · offline-capable Android operations · first-run setup wizard (D10).

## 3. Business rules

### 3.1 Dispensers
- One machine = one fuel type; a machine has 1 or 2 nozzles (A/B), each with its own **cumulative totalizer** (D2, D6).
- Number of machines is configurable (pilot pump: 5–6 machines).
- Readings are entered manually; no automatic dispenser integration.
- Meter replacement/reset is recorded as an event so volume is never negative (3.8).

### 3.2 Login & duty
- Attendant logs in with a PIN on an **approved device**; optional biometric.
- After login selects the machine (and nozzle on double-nozzle machines — sticky, D6).
- Selection stays active for subsequent sales; machine can be changed; one-tap logout.
- Authentication, duty session, machine assignment and business shift are **separate entities**.
- Logout never erases unsynchronized transactions.

### 3.3 Sales
- Every vehicle = one sale. Flow: dispense → Quick Sale → active machine/nozzle preselected → **enter amount (BDT)** (D7) → cash default → save.
- Vehicle number optional. Price snapshot preserved; litres computed from effective price.
- Receipt checkbox default OFF; only available online (D20).

### 3.4 Payments
- MVP: **cash only**. Credit rules pending — do not implement.

### 3.5 Shifts
- Variable shifts, midnight crossing, duty end separate from shift close.
- Opening/closing meter readings per nozzle; supervisor reviews and approves shift close.
- Cash handover may occur multiple times per duty (D16); shortages per D18.
- Test-pump volumes recorded by supervisor (D17).

### 3.6 Offline
- Sales and duty events stored locally (SQLite), unique client IDs, persistent queue, safe retry, no duplicates, preserved through logout/restart.
- States: pending, synced, review_required (never silent loss).
- Handles wrong device clocks, late sync, revoked users, conflicting events.
- Connectivity: pump Wi-Fi primary, mobile data optional (D9).

### 3.7 Pricing
- Effective-time price versioning; offline sales validated against the price effective at the (estimated) sale time. Preserve price snapshot, timestamps, validation outcome and approved corrections.

### 3.8 Inventory
- Hybrid reconciliation: sales = provisional dispensing; nozzle meters = verification; dip = tank check.
- Provisional vs verified stock (D1). No double deduction.
- Manual dipstick: litre-marked or cm + chart, per tank (D12). Tank sensors excluded.
- Stock gain/loss per D19; transit shortage per D15.

### 3.9 Costing
- **FIFO** cost layers at fuel-type level (physically mixed fuel is an accounting convention).

### 3.10 Accounting
- Internal double-entry ledger, auto-posted from business events (no manual journals for staff).
- Cashbook, cash handover, expenses, supplier ledger & payments, supplier claims, purchasing, revenue, inventory valuation, FIFO COGS, gross/operating/net profit.
- Customer ledger architecture possible later; credit workflows not implemented.

### 3.11 Central printing
- Sale validated and saved → backend creates authorized print job → Socket.IO notifies agent → agent claims job → prints → acks.
- Persistent queue, duplicate protection, authorized agent, controlled reprints ("COPY"), receipt search, failure recovery, audit.
- Offline behaviour per D20. Printer: Xprinter XP-E200M, 58 mm receipts (D14).

### 3.12 Reports
Owner dashboard · daily / fuel / machine / employee sales · shift closing · meter reconciliation · tank inventory · purchases · supplier ledger · expenses · cash collection · FIFO COGS · P&L · PDF/Excel export.

## 4. Pending (do not implement)
Credit approval rules · credit limits · offline credit & approval authority · VAT tax invoice format.

## 5. Excluded from MVP
See `DECISIONS.md` → Excluded.

## 6. Development strategy
Vertical slices: schema → backend → frontend → validation → tests → verify → commit. One component at a time, each demonstrable. Progress in `TASK_TRACKER.md`.
