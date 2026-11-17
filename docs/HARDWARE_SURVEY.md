# HARDWARE_SURVEY.md — PPMS (v2, lean)

> শুধু development শুরুর আগে **অবশ্যই** জানতে হবে এমন প্রশ্ন রাখা হয়েছে।
> বাকি তথ্য owner **First-run Setup Wizard**-এ নিজে দেবে (Part C)।
> উত্তর না পেলে লিখুন `UNAVAILABLE — কারণ`। অনুমান নয়।

---

## Part A — Mandatory Survey (development-এর আগে)

| # | প্রশ্ন | কেন দরকার | Blocks | উত্তর |
|---|---|---|---|---|
| A1 | Shift শেষে প্রতিটি machine-এর **মোট বিক্রি litre-এর reading (totalizer)** নেওয়া যায় কি? কীভাবে দেখা যায় (সরাসরি / keypad / menu)? 📷 বা ছোট video | Reading না পাওয়া গেলে attendant-ভিত্তিক মিলানো (sales বনাম meter) সম্ভব নয় → design বদলাতে হবে | 0.4, 6.2 | |
| A2 | Reading কি **nozzle-wise** নাকি পুরো **machine-এর একটি**? প্রতিটি machine-এ nozzle কয়টি? | Meter reading table-এর গঠন | 0.4 | |
| A3 | প্রতিটি tank-এর **calibration chart** আছে? 📷 সব পৃষ্ঠা | Dip (cm) → litre রূপান্তর; না থাকলে বিকল্প design লাগবে | 3.5 | |
| A4 | Dipstick-এর মাপ কোন unit-এ (cm / mm / inch)? Chart-এ কত ঘর পর পর মান আছে? | Calibration table format | 3.5 | |
| A5 | Attendant-দের phone-গুলোর মধ্যে **সবচেয়ে পুরোনো Android version** কত? (Settings → About phone) | App-এর minimum Android version ঠিক করা | 1.7 | |

### Round 2 উত্তর (2026-11-16)

| # | অবস্থা | উত্তর |
|---|---|---|
| A1 | ✅ | প্রতিটি meter **cumulative totalizer** দেখায় — কখনো দিনে শূন্য হয় না। দিনের বিক্রি = আজকের reading − আগের reading (যেমন 200 → 450 → 650) |
| A2 | ✅ | মোট **৫–৬টি machine**; single-nozzle ও double-nozzle দুই ধরনই আছে। Double-nozzle-এ **২টি আলাদা meter** → setup হবে **প্রতিটি meter আলাদাভাবে**। ⚠️ একজন attendant একাই দুটো nozzle চালাতে পারে — duty assignment নিয়ে সিদ্ধান্ত দরকার (D6) |
| A3 | 🟡 | **BSTI-অনুমোদিত dipstick** ব্যবহার হয়; calibration chart আছে কিনা এখনো জানা যায়নি |
| A4 | ⏳ | Dipstick-এর দাগ **litre-এ নাকি cm/mm-এ** — জানতে হবে (litre-এ হলে chart লাগবে না) |
| A5 | ⏳ | বাকি |

### ইতিমধ্যে জানা (Round 1)
- ৩টি machine-এর ছবি: ডিজেল, High-Tech (fuel?), অকটেন (Blue Sky RealTech) — সবগুলো electronic, keypad সহ।
- Machine ভেদে litre দশমিক আলাদা (৩ বনাম ২) — D7 (শুধু টাকা entry) নেওয়ায় এটা আর সমস্যা নয়।
- প্রতিটি machine-এর পাশে **Wi-Fi** আছে; attendant-রা Wi-Fi-র উপর নির্ভর করবে।
- Phone: Android, fingerprint নেই ধরে নেওয়া, দক্ষতা মাঝারি, কাজ চালানোর মতো ইংরেজি।
- Cashier desk-এ Windows laptop আছে।

---

## Part B — Business Decisions (owner-এর কাছ থেকে; hardware নয়)

| Code | প্রশ্ন | লাগবে কখন (task) | উত্তর |
|---|---|---|---|
| C5 | Attendant নগদ টাকা কখন, কার কাছে জমা দেয়? | 9.1 | |
| C1 | Cash কম/বেশি হলে কী হয় (attendant দেবে / খরচ / অন্য)? | 9.8 | |
| C3 | Dip-এ stock কম/বেশি হলে কী হয়, কে অনুমোদন দেয়? | 9.9 | |
| C2 | Supplier invoice-এর চেয়ে কম fuel পেলে কী হয়? | 8.5 | |
| C6 | Test-pump (মাপ যাচাইয়ে fuel বের করে ফেরত) হয়? হলে কীভাবে লেখা হয়? | 9.4 | |
| P4 | Internet না থাকলে receipt চাইলে কী হবে (পরে print / হাতে লেখা)? | 11.1 | |

---

## Part C — Setup Wizard-এ নেওয়া হবে (survey নয়)

| ধাপ | Owner যা দেবে |
|---|---|
| 1. Pump | নাম, ঠিকানা, ফোন, receipt header |
| 2. Settings | **Business day শুরুর সময়** (default 00:00, D8), **offline re-login সীমা** (default 12h, D8), variance-এর সীমা |
| 3. Fuel & দাম | ৩টি fuel-এর বর্তমান দাম (effective time সহ) |
| 4. Tanks | নাম, fuel, capacity, calibration chart, শুরুর dip/stock |
| 5. Machines | নাম/label, fuel, কোন tank থেকে, nozzle সংখ্যা (A/B), meter-এর digit সংখ্যা, প্রতি nozzle-এর শুরুর meter reading *(litre দশমিক setting আর লাগবে না — D7: entry শুধু টাকায়)* |
| 6. Shifts | Shift template (নাম, শুরু–শেষ; মধ্যরাত পার হতে পারে) |
| 7. Employees | নাম, role, PIN, device approval |
| 8. Opening balances | হাতে নগদ, প্রতি fuel-এর stock ও ক্রয়মূল্য, supplier-এর বকেয়া |
| 9. Suppliers (ঐচ্ছিক) | পরে purchase-এর সময় search + autofill / নতুন add |
| 10. Printer | Print agent connect ও test print |

---

## Part D — বাদ দেওয়া হয়েছে (system-এর হিসাবের সাথে সম্পর্ক নেই)

Machine brand/model; display-তে টাকা/litre দেখায় কিনা; দাম machine-এ কে সেট করে; analog/electronic; meter/machine বদলানোর ইতিহাস; calibration/BSTI তারিখ; বিদ্যুৎ গেলে machine-এর আচরণ; কাগজের register; দৈনিক গাড়ির সংখ্যা; internet/load-shedding কত ঘন ঘন; printer–machine দূরত্ব; water dip; chart কে বানিয়েছে; dip কতবার নেওয়া হয়; supplier payment terms; phone share করে কিনা; APK install আপত্তি (13.7-এ দেখা হবে)।

---

## Printer — ✅ কেনা হয়েছে: Xprinter XP-E200M (2026-11-16)

Spec মিলেছে: 80 mm series, 58/80 mm printing mode switch, auto-cutter, USB + LAN। ESC/POS ও LAN port নিজের unit-এ নিশ্চিত করতে হবে (11.5)।

### মূল purchase checklist
- [ ] 80 mm-supported printer, যেটাতে **58 mm রোলও লাগানো যায়** (paper guide/spacer আছে কিনা কেনার আগে দেখুন) — receipt 58 mm-এ ফিট করবে (D14)
- [ ] ESC/POS support
- [ ] LAN (Wi-Fi/USB থাকলে ভালো)
- [ ] Auto-cutter
