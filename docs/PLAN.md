# RescueAlbum: Delivery Plan

Version 0.1 · 25 Oct 2026

## Team

| Role | Owns |
|---|---|
| **Dev A** (app & capture) | Camera, splitting, glare, back reader, Photos export, sharing, UI shell |
| **Dev B** (ML & money) | Restore engine, model conversion & performance, model delivery, paywall & StoreKit, analytics |
| **Product / design** (part-time) | Flows, paywall copy, App Store assets, JP localization review, user tests |

Estimates are in dev-weeks for one developer. Calendar time ≈ dev-weeks ÷ 2 × 1.2.

## Milestones

| # | Milestone | Goal | Dev-weeks | Calendar (2 devs) | Exit criteria |
|---|---|---|---|---|---|
| **M0** | Quality spike | Prove the restore chain beats Remini on "same person" | 2 | Week 1 | Blind test on 50 real photos (≥ 15 Japanese): ≥ 70% "same person"; GFPGAN legal review started |
| **M1** | Foundations | Project skeleton, data model, capture, split | 3 | Weeks 2–3 | Capture a page and get split, straightened prints saved to the library |
| **M2** | Scan complete | Glare fusion, back reader + dates, Photos export | 3 | Weeks 3–5 | A full album page goes from capture to a Photos album with correct dates |
| **M3** | Restore in app | Restore engine integrated, sliders, compare, what-changed, model download | 3 | Weeks 4–6 | 12 MP restore < 15 s on iPhone 15; no memory crash on 50-photo batch |
| **M4** | Monetization & polish | Paywall, trial, Family Sharing, analytics, EN/JA, onboarding | 2 | Weeks 6–7 | Purchase flow works in sandbox; funnel events visible |
| **M5** | Beta & launch | TestFlight with 30 users (15 US / 15 JP), fixes, App Store submission | 1 + fixes | Weeks 8–9 | Crash-free ≥ 99.5%; App Review approved |
| **v1.1** | Family & depth | CloudKit Family Album, scratch repair, batch queue + Live Activity, print layouts, Deep Restore | 6–7 | Weeks 10–14 | See USER_STORIES epics E8–E10 |

**v1 total: ~16 engineering dev-weeks (see TASKS.md), about 9–10 calendar weeks with 2 developers.** This is above the earlier 12-week estimate because it adds the M0 spike, glare fusion, the beta and a bug-fix buffer.

## Timeline (calendar weeks, 2 devs)

```
Week        1    2    3    4    5    6    7    8    9   10–14
M0 spike   ████
M1 found.       █████████
M2 scan              ██████████
M3 restore      (B)  ██████████████
M4 money                       █████████
M5 beta                                  █████████
v1.1                                               ██████████
```

Dev B starts M3 work (model conversion, tiling) in week 2, in parallel with Dev A's M1–M2.

## Launch timing

- A ~9-week build lands around **early January 2027**.
- Good seasonal windows: **Mother's Day (May, US & JP)**, **Father's Day (June)**, **お盆 (mid-August, JP)**, US holidays (Thanksgiving to Christmas 2027).
- Plan the first paid acquisition push for the Mother's Day window, after rating and paywall data from Jan–Apr.

## Decisions to make

| ID | Decision | Options | Recommendation | Needed by |
|---|---|---|---|---|
| D1 | Minimum iOS | iOS 26 vs iOS 18 | **iOS 26** (Background Assets, `BGContinuedProcessingTask`, newer Vision) | M1 start |
| D2 | Face model | GFPGAN (clean parts) vs face step deferred | GFPGAN **after legal review**; ship without face step if review fails | End of M0 |
| D3 | Paywall style | Paywall at "wow" moment with free scanner vs hard paywall | Start with **wow-moment + free scanner**, A/B test hard paywall after launch | M4 |
| D4 | Sharing backend | CloudKit only vs CloudKit + web link | **CloudKit only** in v1.1; web link if users ask | v1.1 start |
| D5 | Analytics | TelemetryDeck vs PostHog | Either. Must be privacy-friendly and anonymous. | M1 |
| D6 | Subscriptions SDK | StoreKit 2 only vs RevenueCat | **RevenueCat** for paywall experiments (free to start) | M4 |
| D7 | Branding | App name (EN/JP) | "RescueAlbum" EN. JP name e.g. おもいでアルバム (check trademark/ASO) | M4 |

## Risks

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Restore quality not clearly better than Remini | Med | High | M0 gate before building the app; tune blend defaults; faithful mode as default |
| GFPGAN license / identity drift on Asian faces | Med | High | Legal review in M0; test JP faces; ship without face step if needed |
| Memory crashes on older iPhones | Med | Med | Tiling, sequential models, < 700 MB peak, device floor (iPhone 12+) for restore |
| Japanese handwriting OCR weak | High | Low | Decade picker and manual date entry; don't depend on OCR |
| App Review rejection (4.3 "AI enhancer" spam) | Low–Med | Med | Lead with album workflow, real screenshots, no gimmick features |
| Low repeat use, so cancellations | High | Med | Weekly pass for projects; Family Sharing; Family Album (v1.1); seasonal prompts |
| Apple or Google ship album splitting / restore | Low–Med | High | Move fast; differentiate on faithful repair, dates, family album |

## Validation before and after launch

**Before (M0/M5)**
1. Blind restore test (50 photos, 10 judges): RescueAlbum vs Remini on "same person" and preference.
2. App Store search popularity: "photo scanner", "restore old photos", "PhotoScan alternative", 写真 スキャン, 古い写真 復元, アルバム デジタル化.
3. 10 interviews (5 US, 5 JP) with people who digitized prints in the last year.

**After launch (A/B tests, in this order)**
1. Paywall style: wow-moment vs hard paywall
2. US weekly price: $5.99 vs $7.99
3. JP: monthly + yearly vs yearly only
