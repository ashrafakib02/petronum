# ARCHITECTURE.md — PPMS

Status: **skeleton**. Sections marked _(task x.y)_ are completed by that task.

## 1. System overview

```
 Attendant phones (React Native + SQLite outbox)
        │  HTTPS REST /sync  (pump Wi-Fi or mobile data; offline queue)
        ▼
 ┌──────────────────────────── Linux VPS ────────────────────────────┐
 │ Express API (modular monolith) ── PostgreSQL                       │
 │   modules: auth, devices, config, shifts, duty, sales, sync,       │
 │            inventory, fifo, ledger, cash, purchases, suppliers,    │
 │            printing, reports, settings, audit                      │
 │ Socket.IO (notifications only)                                     │
 └───────────────────────────────────────────────────────────────────┘
        ▲ HTTPS (web)                    ▲ HTTPS + Socket.IO (agent)
 React web (owner/manager/            Print agent (Node service on cashier
 supervisor/cashier)                  Windows laptop) → Xprinter XP-E200M (LAN/USB)
```

Environments (D21, D22): **local** dev PC for Phases 1–5 (phone → API over LAN); **staging** = Hostinger KVM 1 + Dokploy (Docker) from task 5.11; **production** at 13.1 using the same Dokploy setup.

## 2. Repository layout
See `CLAUDE.md`. Shared code (zod schemas, money/qty utils, constants, UI strings) in `packages/shared`.

## 3. Data conventions
- Money: integer poisha in JS; `NUMERIC(14,2)` in DB. Litres: integer ml in JS; `NUMERIC(14,3)` in DB. Rounding half-up _(finalized in 0.3)_.
- Time: `timestamptz` UTC; display `Asia/Dhaka`; `business_date` derived from business-day start setting (D8).
- IDs: client-generated UUID for offline-created records (sales, duty events); DB-generated elsewhere.
- No hard deletes on financial tables; append-only ledgers (journals, stock movements, audit log).

## 4. Data model (ERD)
_(task 0.4: users, devices, auth sessions, duty sessions, machine/nozzle assignments, business shifts, machines, nozzles, tanks, meter readings, meter reset events)_

_(task 0.5: sales, price versions, stock movements, FIFO layers & consumptions, journals, supplier claims, print jobs, settings, audit log)_

Fixed constraints:
- Sale has **no stock FK** (D1). Stock moves only via `verified_dispense`, `receipt`, `opening`, `adjustment`.
- Meter readings keyed by nozzle (D2).
- Auth session ≠ duty session ≠ machine assignment ≠ business shift.

## 5. Offline sync protocol
_(task 0.6: JSON examples for new batch, duplicate replay, partial failure, review_required, outdated app version)_

Fixed design:
- Mobile writes every event to local SQLite **outbox** first (online or offline).
- States: `pending → sending → synced | review_required | failed(retryable)`; never deleted before ack.
- `POST /sync` batch; idempotent by client UUID (unique constraint); per-item ack; replay returns identical ack.
- Min app version gate (426) — items stay pending.
- Clock model: server time offset captured at each sync + monotonic elapsed time → `estimated_server_time`; outside tolerance → `review_required`.
- Revoked users: events before revocation accepted; after → `review_required`.

## 6. Inventory & costing
- Provisional stock (D1, 8.11); verified stock from approved shift close.
- FIFO layers per **fuel type**; consumption inside one DB transaction with `SELECT … FOR UPDATE`.
- Receiving creates layer for received qty; shortage → supplier claim (D15).
- Gain/loss per D19; test-pump excluded from meter volume per D17.

## 7. Accounting
- Double-entry `journal_entries` + `journal_lines`; balanced check; immutable; reversal entries.
- Posting map _(task 0.7)_ — events: sale, void, purchase, receiving, supplier claim accept, supplier claim reject, shift-close COGS, cash handover, expense, supplier payment, bank deposit.

## 8. Printing
- Print job created only for receipt-flagged **online** sales and one-time prints of synced offline sales (D20).
- Agent authenticates with device token; Socket.IO notify + polling fallback; claim/lease → print → ack; idempotent.
- Reprints marked "COPY", permission-gated, audited. Receipt = "Cash Receipt", 58 mm / 32 cols default (D14).

## 9. Security
- RBAC (owner, manager, supervisor, cashier, attendant) with permission-matrix tests.
- Device registration + supervisor approval; Android version recorded (D11).
- PIN: hashed, rate-limited, lockout; tokens in secure storage; refresh rotation.
- Offline login window setting (D3/D8).
- Append-only audit log for all sensitive actions.
- TLS, SSH-key-only VPS, firewall, encrypted offsite backups with restore drills.

## 10. Operations
- Dokploy (Docker) handles containers, reverse proxy, TLS and WebSocket upgrade; pino logs, health endpoint, alerts.
- Pump-side: router and printer on small UPS recommended; cashier laptop must not sleep.