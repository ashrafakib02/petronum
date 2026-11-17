# DECISIONS.md — PPMS

Decision log. **Entries here override `PROJECT_BLUEPRINT.md`** where they conflict.
Status: ✅ Approved · ⏳ Tentative · ⚠️ Assumed (confirm with owner) · ⛔ Pending (do not implement)

## Approved decisions

| # | Date | Topic | Decision | Tasks | Status |
|---|---|---|---|---|---|
| D1 | 2026-11-16 | Inventory posting | Sale posts revenue only. Stock movement + FIFO COGS posted at shift-close approval from verified meter volume. If shift close is delayed, show **provisional stock** = last verified + receipts − unverified sales volume | 7.4, 8.11, 9.7 | ✅ |
| D2 | 2026-11-16 | Meter storage | Readings stored per nozzle in backend; attendant UI is machine-level | 0.4, 6.2 | ✅ |
| D3 | 2026-11-16 | Offline re-login | 12 h window, configurable (see D8) | 2.12, 13.6 | ⏳ final at 13.6 |
| D4 | 2026-11-16 | Documentation | `CLAUDE.md` added; roadmap + progress live only in `TASK_TRACKER.md` (no separate roadmap file) | 0.8 | ✅ |
| D5 | 2026-11-16 | Demo order | Keep offline-first order; first sale demo is M2 (7.13); no throwaway online-only demo | — | ✅ |
| D6 | 2026-11-16 | Double-nozzle machines | Nozzles labelled A/B at setup. Single-nozzle: no extra step. Double-nozzle: **sticky selection** — chosen once at duty start, preselected in Quick Sale, 1 tap to switch. *Overrides blueprint §3.2 "no repeated nozzle selection" for double-nozzle machines only* | 6.5, 7.5 | ✅ |
| D7 | 2026-11-16 | Quick Sale entry | **Amount only** (BDT, 2 decimals). Litres computed = amount ÷ effective price, stored 3 decimals. *Overrides blueprint §3.3 "amount or litres"* | 0.3, 7.1, 7.5, 9.4 | ✅ |
| D8 | 2026-11-16 | Settings | Business-day start (default 00:00) and offline-login window (default 12 h) are owner system settings, not blockers | 3.10, 6.1, 12.1 | ✅ |
| D9 | 2026-11-16 | Connectivity | Pump Wi-Fi primary; attendant may use own mobile data; otherwise offline queue auto-syncs on reconnect | 1.13, 5.11 | ✅ |
| D10 | 2026-11-16 | Setup & suppliers | First-run Setup Wizard; supplier search + autofill + inline add | 3.11, 8.1, 8.2 | ✅ |
| D11 | 2026-11-16 | Android support | Latest stable Expo/React Native with that SDK's default minimum Android version (not raised). Device registration records Android version; check approved devices before SDK upgrade / forced update | 1.7, 2.7, 13.7 | ✅ |
| D12 | 2026-11-16 | Dipstick | Per-tank dip mode chosen at setup: litre-marked dipstick (direct entry) or cm/mm + calibration chart. Editable later; past readings unchanged | 3.5 | ✅ |
| D13 | 2026-11-16 | Language | Software is **English-only** (UI, receipts, reports, manual). No Bangla, no i18n library. *Bengali is used only in chat explanations to the owner* | 1.9, 11.6, 12.8, 13.11 | ✅ |
| D14 | 2026-11-16 | Printer | **Xprinter XP-E200M** purchased (80 mm series, 58/80 mm mode switch, auto-cutter, USB + LAN). Receipt designed for 58 mm (32 cols); paper width is a setting | 11.5, 11.6 | ✅ |
| D15 | 2026-11-16 | Transit shortage | Invoice qty − received qty → separate **supplier claim**. Accepted → reduces payable / cash refund; rejected → transit-loss expense. Rule may change later | 8.5, 8.12, 10.3 | ✅ |
| D16 | 2026-11-16 | Cash handover | Attendant may hand over cash multiple times during duty; remainder reconciled at duty end | 9.1 | ✅ |
| D17 | 2026-11-16 | Test-pump | Supervisor records test-pump on web (nozzle, litres). Per-nozzle daily limit setting; above limit only **owner** approves; every entry visible to owner. Fake entries surface in tank reconciliation | 3.10, 9.13, 12.2 | ✅ |
| D18 | 2026-11-16 | Cash short/over | Within tolerance (default ±20 BDT) auto-posts; above, supervisor chooses attendant due or write-off; write-off needs **owner** approval. Cash over → other income. Salary deduction is outside the system | 9.8, 9.14 | ✅ (for now) |
| D19 | 2026-11-16 | Stock gain/loss | Within tolerance auto-posts (loss at FIFO cost, gain as new layer at latest purchase cost); above needs **owner** approval + dashboard alert. Tolerance set in setup, editable; entered as **% or "X L per 100 L"** (both shown, live example) | 3.10, 3.11, 9.9, 9.10 | ✅ |
| D20 | 2026-11-16 | Offline receipt (resolves pending #4) | Offline → receipt checkbox disabled; handwritten receipt if needed (outside the system; cashier & driver responsible). After sync no auto print; one-time "Print Receipt" button per sale (phone + web); afterwards only controlled COPY reprint | 7.6, 11.1, 11.10 | ✅ |
| D21 | 2026-11-17 | Environments | **Local-first**: Phases 1–5 run on the dev PC (phone reaches API over LAN). Staging VPS comes at the end of Phase 5 (5.11–5.13), before real-device network tests (5.14); production at Phase 13. M1 demo runs locally | 1.13, 3.12, 5.11–5.14, 13.1 | ✅ |
| D22 | 2026-11-17 | Server panel | Spare **Hostinger KVM 1** (1 vCPU / 4 GB / 50 GB) wiped and used as staging with **Dokploy** (lightweight, Docker-based). Coolify not installed on it (needs 2 cores) | 5.11, 5.12, 13.1 | ✅ |

## Assumptions (confirm with owner)

| # | Assumption | Tasks |
|---|---|---|
| A2 | Cash handover receiver = the cashier/supervisor logged in on web who records it | 9.1 |
| A3 | Stock tolerance basis = litres **sold** in the period (not tank stock) | 9.9, 9.10 |

## Unchanged by request
- Meter Reset event (3.8) and optional biometric login (2.11) remain as planned.

## Conventions (to be finalized in task 0.3)
- Money: integer poisha in JS, `NUMERIC(14,2)` in DB. Litres: integer ml in JS, `NUMERIC(14,3)` in DB. Rounding: half-up.
- Time: `timestamptz` (UTC) in DB, displayed in `Asia/Dhaka`; separate `business_date` field.

## Pending — do NOT implement
1. Credit approval rules
2. Customer credit limits
3. Offline credit sales and approval authority
4. ~~Offline instant receipt printing~~ → resolved by D20
5. VAT-specific tax invoice format (receipts say "Cash Receipt", never "Tax Invoice")

Attendants must never receive credit-approval permissions.

## Excluded from MVP
Digital payments · multi-tenant · microservices · automatic dispenser integration · IoT tank sensors · HR/payroll · AI forecasting · advanced BI · customer app · loyalty · GPS · individual attendant printers · mandatory vehicle number for cash sales · detailed nozzle-level UI workflows (beyond D6) · offline Force Print (considered, rejected in favour of D20)