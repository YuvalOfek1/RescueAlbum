# RescueAlbum: Product & Technical Specification

Version 0.1 · 25 Oct 2026 · Status: draft for review

---

## 1. Problem

Most families keep hundreds of printed photos (1950s–2000s) in albums and shoeboxes at a parent's or grandparent's home. These prints are:

- **fading and getting damaged**: yellow casts, dust, scratches
- **one of a kind**: if they're lost, they're gone
- **stuck in one place**: only whoever holds the album can see them

Photographing them with the iPhone camera gives poor results: glare from album sleeves, crooked crops, one print at a time, faded colors, and the date of the shot instead of the year the photo was taken. "AI enhancer" apps often change faces.

### Evidence (from research, Oct 2026)

- Restore/enhance apps: **35% of recent US reviews are 1–2★**. Renew 57%, RetroFix 55%, MyHeritage 52%.
- **8% of US/UK 1–2★ restore reviews say faces changed.** Remini's JP reviews complain of 別人 ("a different person").
- **Google PhotoScan was last updated 21 Nov 2022.** It still has 90.5k US and 51.9k JP ratings, and JP users report bugs on new iPhones.
- Photomyne is praised for splitting album pages but sells restore credits on top of its membership and asks for payment after scanning.
- Reddit (r/Genealogy, r/DataHoarder, r/estoration) describes juggling 4–5 tools, and treats date fixing as essential.
- Market proof: Remini earns ~$93M/yr net (Appfigures estimate, Feb 2026). Remini ranks #14 grossing in JP Photo & Video vs #21 in the US.

## 2. Positioning

> **Every photo from the album: repaired, dated, shared, and still looking like the person.**

- **Album-first, not "AI-first".** The whole workflow, from page to family album, in one app.
- **Faithful by default.** Repairs damage and leaves faces alone unless the user opts in.
- **Honest unlimited.** Free scanning forever. Subscribers get unlimited restoration with no credits.
- **Private.** Photos are processed on the iPhone and are not uploaded.

## 3. Target users

| Persona | Situation | Main need |
|---|---|---|
| **The family archivist** (35–60) | A parent passed away or moved into care, and the siblings are splitting up the albums | Digitize everything quickly and give everyone copies |
| **The event organizer** | A funeral, memorial, wedding or anniversary is coming | Old photos for a slideshow or display, fast |
| **The gift giver** | Grandma's birthday or Mother's Day | One beautifully restored, printed photo |
| **The genealogist** | Building a family history | Accurate dates, names, captions, archive quality |
| **JP: 終活 family** | Clearing a parent's home, often around お盆 or New Year | Album digitization, sharing via LINE, prints |

## 4. Scope

### 4.1 v1 (MVP)

1. **Album page capture**: full-resolution camera, guided framing.
2. **Auto-detect and split** every print on a page, with a manual corner editor.
3. **Glare removal** from 2–3 angled shots.
4. **Back-of-photo capture**: OCR the handwriting into a caption and parse the date (including Japanese era years). Quick decade picker when no date is found.
5. **Restore** (on-device): fade/color correction → denoise → upscale, plus optional face clean-up and optional colorize, each with a strength slider. Includes a before/after compare and a "what changed" heat map.
6. **Save to Photos** in a named album, in order, with the photo's real date. Restored versions are saved as revertible edits.
7. **Share** via the system share sheet (LINE, AirDrop, Messages, email).
8. **Projects**: group scans by album or relative ("Mom's albums").
9. **Subscription** with paywall, trial and Family Sharing.
10. **Localization**: English + Japanese.

### 4.2 v1.1

- Scratch and tear repair (detection + inpainting)
- **Shared family album** (CloudKit sharing)
- Batch restore queue with background processing and a progress Live Activity
- Print layouts (JP L-size, 4×6, 5×7) and print-ready export
- Optional cloud "Deep Restore" for heavy damage (fair-use queue, consent screen)

### 4.3 Later

- Web link gallery for Android relatives (own backend)
- Memorial / anniversary slideshow video
- People tagging, date estimation

### 4.4 Out of scope

- "Animate the face" / talking photos. These conflict with the faithful positioning and carry App Review risk (Guideline 4.3).
- Credits, tokens or consumable packs
- Ads

## 5. User experience

### 5.1 Core flow

```
Open app → New project ("Mom's albums")
  → Capture page → auto-split (adjust corners) → glare pass (tilt prompt)
  → [optional] flip & capture backs → caption + date
  → Wow moment: before/after of the user's own photo (full quality)
  → Paywall ("Scanning is free. Restoring is Pro.")
  → Restore all → review (slider, compare, what-changed)
  → Save to Photos album / Share
```

### 5.2 UX rules (from 70,000 reviews)

1. Say what's free on the first screen. **Never charge at the save step** after work is done.
2. No credits. Unlimited means unlimited.
3. Trial terms on one screen, with the charge date and a reminder before charging.
4. Never remove features that launched in v1.
5. Autosave everything. Never lose a scan.
6. Keep results faithful: strength slider, before/after, and a clear view of what changed.
7. No ads.
8. Do Japanese properly: fonts, IME-safe text fields, Japanese support replies.
9. When users cancel, **they keep everything**. Photos are already in their library.

## 6. Technical architecture

### 6.1 Platform

- Swift 6, SwiftUI, **iOS 26 minimum** (decision D1, see PLAN.md)
- iPhone first. iPad supported (landscape) as a secondary target.
- No backend in v1

### 6.2 Modules

| Module | Responsibility | Key Apple tech |
|---|---|---|
| `CaptureKit` | Camera session, full-res capture, guided framing, tilt prompts | AVFoundation (`AVCapturePhotoOutput`) |
| `PageSplitter` | Detect multiple prints, perspective-correct, manual corners | Vision (`VNDetectRectanglesRequest`), Core Image (`CIPerspectiveCorrection`) |
| `GlareFusion` | Align 2–3 shots, per-pixel glare-free merge | Vision (`VNHomographicImageRegistrationRequest`), Core Image / Metal |
| `BackReader` | OCR the backs, parse dates (incl. 昭和/平成/令和), extract captions | Vision text recognition, `NSDataDetector`, custom era parser |
| `RestoreEngine` | Model pipeline, tiling, memory management, job queue | Core ML, Core Image, Accelerate |
| `ModelStore` | Download, version and compile ML models after install | Background Assets (Apple-hosted), `MLModel.compileModel` |
| `Library` | Projects, items, metadata, edit history | SwiftData; files in app container (HEIC) |
| `PhotosExport` | Albums, creation date, revertible edits | PhotoKit (`PHAssetCreationRequest`, `PHContentEditingOutput`) |
| `Sharing` | v1: share sheet. v1.1: CloudKit family album | `ShareLink`, CloudKit (`CKShare`) |
| `Paywall` | Plans, trial, entitlement, Family Sharing | StoreKit 2 (`SubscriptionStoreView`), optionally RevenueCat |
| `Analytics` | Funnel events, crash reporting | TelemetryDeck or PostHog; Xcode Organizer |

### 6.3 Data model (SwiftData, v1)

```
Project      id, title, createdAt, coverItemID
Page         id, projectID, capturedAt, sourceImageURLs[], glareFused: Bool
Item         id, pageID, cropQuad, originalURL, restoredURL?, backImageURL?,
             caption?, takenDate?, datePrecision (day|month|year|decade),
             restoreSettings (faceStrength, colorize, colorizeStrength),
             photosAssetID?, state (scanned|restoring|restored|exported)
```

## 7. Photo-repair pipeline

Everything runs on-device. Order of operations per item:

| # | Step | Method | License | Notes |
|---|---|---|---|---|
| 1 | Crop & straighten | Vision rectangles + `CIPerspectiveCorrection` | Apple | Manual corner override |
| 2 | Glare removal | Homographic alignment of 2–3 frames, then per-pixel selection of the non-specular sample (low brightness / high saturation) | Apple + ours | PhotoScan-style |
| 3 | Fade & color cast | Core Image auto-adjust, white balance, levels, local contrast | Apple | No ML. Fixes most yellowing. |
| 4 | Denoise / grain | `CINoiseReduction` or NAFNet / Restormer (Core ML) | MIT | Preserve some film grain |
| 5 | Upscale | Real-ESRGAN x4 (Core ML, ~59 MB), 512 px tiles with overlap. Compact `general-x4v3` for preview | BSD-3 | ~5–10 s per 12 MP output |
| 6 | Face clean-up *(opt-in)* | Vision face detect + landmarks → align 512² → GFPGAN v1.4 (Core ML, ~298 MB) → feathered paste-back. **Strength slider = alpha blend original ↔ restored.** | Apache-2.0, **NC parts: legal review** | Test identity on Asian faces |
| 7 | Colorize *(opt-in)* | DDColor-Tiny (Core ML, ~203 MB) predicts ab on a low-res copy, combined with full-res L channel (Lab) | Apache-2.0 | Keeps original detail |
| 8 | What changed | Abs-difference heat map before vs after | ours | Trust feature |
| v1.1 | Scratches & tears | BOPBTL scratch-detection UNet → mask → MI-GAN or LaMa inpainting | MIT / Apache-2.0 | False positives risk |
| v1.1 | Deep Restore *(cloud, opt-in)* | Image-edit API (e.g. Gemini image) at 1K, then on-device upscale | vendor ToS | $0.034/image list price; consent screen per Guideline 5.1.2(i) |

**Do not use:** CodeFormer (S-Lab license, non-commercial), Ultralytics YOLO (AGPL), CC-BY-NC weights.

**Performance & memory**
- Peak memory target < 700 MB. Process in tiles, run models sequentially, release between steps.
- Prefer the Neural Engine (fp16). Pre-warm/compile models off the critical path. First compile can be slow.
- Batch work runs in a background job (`BGContinuedProcessingTask`, iOS 26) with Live Activity progress.

**Model delivery**
- Models (~0.6–1 GB total) are downloaded after install via Apple-hosted Background Assets. The App Store binary stays small.
- The app works for scanning before models finish downloading. Restore shows download progress.

## 8. Dates and captions

- Text recognition on the back image with `recognitionLanguages = ["ja-JP", "en-US"]`, accurate level.
- Date parsing order: `NSDataDetector` → custom patterns (`'78`, `Summer 1978`, `1978年8月`) → **Japanese eras**:
  - 昭和 N → 1925 + N (昭和53年 = 1978)
  - 平成 N → 1988 + N
  - 令和 N → 2018 + N
  - 元年 = year 1
- `datePrecision` is stored. The Photos creation date is set to the start of the period.
- Photos has no public API for captions. The caption is stored in the app and written to IPTC/EXIF description metadata of exported files. **Verify** what Photos displays.
- **To test:** Vision accuracy on Japanese handwriting.

## 9. Saving to Photos

- Request add-only or full access as needed (prefer limited/add-only for v1).
- Create or reuse a `PHAssetCollection` named after the project.
- Save the original as a new asset with `creationDate = takenDate`. Apply the restoration as a **revertible edit** (`PHContentEditingOutput` + adjustment data) so users can tap *Revert* in Photos.
- Keep Photos order the same as the album order.

## 10. Sharing

### v1: share sheet
- "Share all" / "Share selected" through `ShareLink` (LINE, AirDrop, Messages, Mail).
- Apps cannot create or add to iCloud Shared Albums, so users can forward from Photos.

### v1.1: Family Album (CloudKit)
- The owner creates a Family Album in the app. Records live in a custom zone in the owner's **private database**, shared via `CKShare`.
- Invite by link (Messages, LINE, email) via `ShareLink` / `UICloudSharingController`.
- Participants (iCloud account + app installed) can view, download, add scans, and edit names and dates.
- Store **share-size HEIC copies** (~1–2 MB). Full originals stay in each person's Photos library.
- **Cost to us: $0.** Storage counts against the owner's iCloud quota.
- Limitation: Apple devices only. Japan has roughly a third Android, hence the later web-link option.

### Later: web link for non-Apple relatives
- A small backend (e.g. Supabase Storage or Cloudflare R2 + static viewer) serves a private, expiring gallery link.
- It requires a privacy policy update, deletion controls and abuse handling.

## 11. Monetization

**Rule:** scanning is free and unlimited. Restoring is a subscription and unlimited.

| | Free | Pro (subscription) |
|---|---|---|
| Capture, split, glare removal | ✓ | ✓ |
| Back-of-photo dates & captions | ✓ | ✓ |
| Save originals to Photos | ✓ | ✓ |
| Full-quality restore preview | ✓ | ✓ |
| Restore, face clean-up, colorize, save restored | | ✓ unlimited |
| Full-resolution export, batch restore | | ✓ |
| Family Album (v1.1) | view only | create & share |

| Market | Plans | Notes |
|---|---|---|
| **US** | Weekly "Album Pass" **$5.99** (no trial). Yearly **$39.99** (7-day trial, default) | Competitors: $9.99/wk, $49.99/yr |
| **Japan** | Monthly **¥800**. Yearly **¥4,800** (「2ヶ月分お得」) | No weekly plan in JP |

- **Paywall moment:** right after the first before/after of the user's own photo ("wow" moment), with "Scanning is free. Restoring is Pro." stated beforehand. Users can dismiss and keep scanning.
- **Family Sharing** enabled on yearly.
- Store fees: ~15% (US Small Business Program; JP 10% + 5% IAP for small developers).
- Cost per restore: $0 (on-device). Deep Restore (v1.1) uses a disclosed fair-use queue.
- After cancel, users keep all saved photos. Restored items remain in Photos.
- Prices are starting points for A/B tests (see PLAN.md).

## 12. Privacy & compliance

- On-device processing, no uploads in v1. Privacy nutrition label: no data collected except anonymous analytics.
- PhotoKit access: request only what's needed. Use PHPicker for imports.
- Privacy manifest (required reason APIs).
- Guideline 5.1.2(i): explicit consent before any cloud AI step (v1.1 Deep Restore). Name the provider.
- Guideline 2.3: App Store screenshots must show real output.
- Guideline 3.1.2: clear subscription terms. JP 特定商取引法 final-confirmation screen requirements for subscriptions (legal check).
- Model licenses reviewed and recorded in `THIRD_PARTY_NOTICES`.

## 13. Analytics (funnel)

`app_open → project_created → page_captured → split_confirmed → first_restore_preview (wow) → paywall_shown → trial_started / purchased → restore_completed → saved_to_photos → shared`

Also: restore duration, memory warnings, crash-free rate, slider usage (face strength), revert rate, cancel reasons.

## 14. Success metrics (first 90 days)

- Restore quality: ≥ 70% of testers say "same person" vs ≤ 50% for Remini in blind tests (pre-launch gate)
- Install → first restore preview: ≥ 60%
- Trial-to-paid ≥ 25% (Photo & Video median 22.2%, RevenueCat 2026)
- Crash-free sessions ≥ 99.5%
- App Store rating ≥ 4.6 with < 10% of reviews mentioning paywall/billing
