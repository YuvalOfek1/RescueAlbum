# RescueAlbum: Engineering Tasks

- **Estimates** are in dev-days (d) for one developer.
- **Owner:** A = app & capture, B = ML & money, P = product/design.
- **Deps** lists the task IDs that must be done first. Stories link to [USER_STORIES.md](USER_STORIES.md).

## M0 · Quality spike (week 1)

| ID | Task | Owner | Est | Deps | Stories |
|---|---|---|---|---|---|
| T-001 | Collect a 50-photo test set (≥ 15 Japanese family photos, mix of B&W, faded, damaged, glare) with consent | P | 2d | – | – |
| T-002 | Convert/validate Core ML models: Real-ESRGAN x4 + general-x4v3, GFPGAN v1.4 (clean arch), DDColor-Tiny, NAFNet. Record sizes and licenses | B | 3d | – | 4.2–4.4 |
| T-003 | Spike harness app: pick photo, run chain (color fix → denoise → upscale → face blend → colorize), export results + timings + peak memory | B | 3d | T-002 | 4.2 |
| T-004 | Run the same set through Remini. Prepare a blind A/B sheet | P | 1d | T-001 | – |
| T-005 | Blind test with 10 judges ("same person?", preference). Write results and go/no-go | P | 1d | T-003, T-004 | – |
| T-006 | Start legal review: GFPGAN license parts, DDColor, Real-ESRGAN, NAFNet. Create `THIRD_PARTY_NOTICES` | P | 1d | T-002 | – |

## M1 · Foundations (weeks 2–3)

| ID | Task | Owner | Est | Deps | Stories |
|---|---|---|---|---|---|
| T-101 | Xcode project (iOS 26, Swift 6, SwiftUI), modules per SPEC §6.2, SwiftLint/format, CI build (Xcode Cloud or GitHub Actions) | A | 2d | – | – |
| T-102 | SwiftData model: Project, Page, Item. File storage helpers (HEIC). Migration plan | A | 2d | T-101 | 1.2, 1.3 |
| T-103 | Projects list & detail UI (create, rename, delete, counts) | A | 2d | T-102 | 1.2 |
| T-104 | Onboarding (≤ 3 screens, "Scanning is free. Restoring is Pro.") | A/P | 1d | T-101 | 1.1 |
| T-105 | `CaptureKit`: camera session, full-res capture, focus/exposure, permissions flow | A | 3d | T-101 | 2.1 |
| T-106 | Live print detection overlay (Vision rectangles on preview frames, throttled) | A | 2d | T-105 | 2.1 |
| T-107 | `PageSplitter`: multi-rectangle detection on the still, filtering, perspective correction | A | 2d | T-105 | 2.2 |
| T-108 | Corner editor with loupe, add/delete box | A | 3d | T-107 | 2.3 |
| T-109 | Autosave pipeline + crash-safe state restore | A | 1d | T-102 | 1.3 |
| T-110 | Analytics wrapper + funnel events (SPEC §13) | B | 1d | T-101 | – |

## M2 · Scan complete (weeks 3–5)

| ID | Task | Owner | Est | Deps | Stories |
|---|---|---|---|---|---|
| T-201 | Glare detection heuristic (specular highlight ratio) + tilt prompt UX | A | 2d | T-105 | 2.4 |
| T-202 | `GlareFusion`: homographic registration of 2–3 frames, per-pixel non-specular selection (Metal/Core Image), seam blending | A | 4d | T-201 | 2.4 |
| T-203 | PHPicker import → optional split | A | 1d | T-107 | 2.5 |
| T-204 | Capture back flow (per item and batch), link back image to item | A | 2d | T-102 | 3.1 |
| T-205 | `BackReader`: OCR (ja-JP + en-US), editable caption UI | A | 2d | T-204 | 3.2 |
| T-206 | Date parser: NSDataDetector + custom patterns + Japanese eras (昭和/平成/令和/元年) with unit tests | A | 2d | T-205 | 3.3 |
| T-207 | Decade picker (single + multi-select), date precision storage | A | 1d | T-102 | 3.4 |
| T-208 | `PhotosExport`: album create/reuse, order, creationDate, add-only permission | A | 2d | T-102 | 5.1 |
| T-209 | Caption metadata (IPTC/EXIF) on export. Verify what Photos shows | A | 1d | T-208 | 3.2 |
| T-210 | Share sheet: all/selected, original vs share-size | A | 1d | T-208 | 5.3 |

## M3 · Restore in app (weeks 4–6; Dev B starts week 2)

| ID | Task | Owner | Est | Deps | Stories |
|---|---|---|---|---|---|
| T-301 | `RestoreEngine` core: step graph, tiling with overlap, ANE/fp16 config, memory budget < 700 MB | B | 4d | T-003 | 4.2 |
| T-302 | Color/fade correction step (Core Image) + tuning on test set | B | 2d | T-301 | 4.2 |
| T-303 | Denoise + upscale steps; fast preview path (compact model) | B | 2d | T-301 | 4.1, 4.2 |
| T-304 | Face step: detection, alignment, GFPGAN, feathered paste-back, strength = alpha blend | B | 3d | T-301, T-006 | 4.3 |
| T-305 | Colorize step: grayscale detection, DDColor low-res ab + full-res L combine, strength | B | 2d | T-301 | 4.4 |
| T-306 | "What changed" heat map | B | 1d | T-301 | 4.5 |
| T-307 | Restore UI: sliders, swipe compare, hold-for-original, apply-to-all | A | 3d | T-302 | 4.3–4.5 |
| T-308 | Batch restore with progress, pause/resume (foreground) | B | 2d | T-301 | 4.6 |
| T-309 | `ModelStore`: Apple-hosted Background Assets, versioning, compile + pre-warm, resume, progress UI | B | 3d | T-002 | 4.7 |
| T-310 | Save restored image as revertible PhotoKit edit (adjustment data) | A | 2d | T-208, T-301 | 5.2 |
| T-311 | Performance pass on iPhone 12 / 13 / 15 / 17: timings, memory warnings, thermal | B | 2d | T-308 | 4.2 |

## M4 · Monetization & polish (weeks 6–7)

| ID | Task | Owner | Est | Deps | Stories |
|---|---|---|---|---|---|
| T-401 | App Store Connect: subscription group, US weekly/yearly, JP monthly/yearly, trial on yearly, Family Sharing | B | 1d | – | 6.2–6.4 |
| T-402 | StoreKit 2 / RevenueCat integration, entitlement gating, restore purchases | B | 2d | T-401 | 6.1, 6.5 |
| T-403 | Paywall UI at the wow moment, dismissible, plan copy per locale, JP final-confirmation screen | B/P | 2d | T-402, T-307 | 6.1, 6.3 |
| T-404 | Trial reminder local notification | B | 0.5d | T-402 | 6.6 |
| T-405 | Japanese localization (String Catalogs) + native review | P | 2d | T-307 | 7.1 |
| T-406 | Accessibility pass: Dynamic Type, VoiceOver, touch targets | A | 1d | T-307 | 7.2 |
| T-407 | Privacy manifest, privacy label, PhotoKit permission copy (EN/JA) | A | 0.5d | – | – |
| T-408 | App icon, App Store screenshots (real output), preview video, EN/JP ASO keywords | P | 2d | T-307 | – |

## M5 · Beta & launch (weeks 8–9)

| ID | Task | Owner | Est | Deps | Stories |
|---|---|---|---|---|---|
| T-501 | TestFlight beta with 30 testers (15 US / 15 JP), feedback form | P | 1d | M4 | – |
| T-502 | Bug-fix buffer | A/B | 5d | T-501 | – |
| T-503 | Final legal check: licenses, 特定商取引法 screens, terms & privacy policy URLs | P | 1d | T-006 | – |
| T-504 | App Review submission + responses | P | 1d | T-502 | – |

## v1.1 backlog (weeks 10–14)

| ID | Task | Owner | Est | Stories |
|---|---|---|---|---|
| T-601 | CloudKit container, custom zone, record schema (album, item, share-size asset) | A | 2d | 8.1 |
| T-602 | CKShare create/invite via ShareLink/UICloudSharingController; accept flow | A | 3d | 8.1 |
| T-603 | Participant contributions + caption/date edits + sync conflict handling | A | 3d | 8.2 |
| T-604 | Roles (read-only/contributor), remove participant, stop sharing | A | 1d | 8.3 |
| T-605 | Scratch detection (BOPBTL UNet) + inpainting (MI-GAN/LaMa) + editable mask brush | B | 5d | 9.1 |
| T-606 | Background batch via `BGContinuedProcessingTask` + Live Activity | B | 2d | 9.3 |
| T-607 | Deep Restore: backend proxy (keys server-side), App Attest, entitlement check, fair-use queue, consent screen | B | 4d | 9.2 |
| T-608 | Print layouts (L-size, 4×6, 5×7) at 300 dpi | A | 2d | 10.1 |

## Totals

| Milestone | Dev-days |
|---|---|
| M0 | 11 |
| M1 | 19 |
| M2 | 18 |
| M3 | 26 |
| M4 | 11 |
| M5 | 8 |
| **v1 total** | **~93 dev-days**, of which product/design (P) ≈ 12 and engineering (A+B) ≈ 81 dev-days ≈ 16 dev-weeks |
| v1.1 | ~22 dev-days |
