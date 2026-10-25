# RescueAlbum: User Stories

Format: **ID · As a … I want … so that …**, with acceptance criteria (AC). Release: **v1** or **v1.1**.

---

## E1 · Projects & onboarding (v1)

**US-1.1 · As a new user, I want to understand in one screen what the app does and what is free, so that I trust it before I start.**
- AC: Onboarding is ≤ 3 screens and skippable.
- AC: The first screen states "Scanning is free. Restoring is Pro."
- AC: No account or sign-in is required.

**US-1.2 · As a family archivist, I want to create a project per album or relative ("Mom's albums"), so that scans stay organized.**
- AC: Create, rename and delete a project. Deletion asks for confirmation and states whether Photos copies are kept.
- AC: A project shows a count of pages, photos and restored photos.

**US-1.3 · As a user, I want my work saved automatically, so that I never lose scans if the app closes.**
- AC: Every capture is persisted before the next UI step.
- AC: After a force-quit, the project reopens at the same state.

## E2 · Capture & split (v1)

**US-2.1 · As a user, I want to photograph a whole album page, so that I don't have to shoot each print separately.**
- AC: The camera shows live detection outlines for prints.
- AC: Capture is at full resolution.
- AC: Works for 1–8 prints per page.

**US-2.2 · As a user, I want the app to split the page into separate, straightened photos, so that each print becomes its own image.**
- AC: Each detected print is cropped with perspective correction.
- AC: ≥ 90% of prints on the test set are detected without manual fixing.

**US-2.3 · As a user, I want to fix a bad crop by dragging corners, so that nothing is cut off.**
- AC: The corner editor has a magnifier loupe.
- AC: Users can add a missed print by drawing a box.
- AC: Users can delete a false detection.

**US-2.4 · As a user, I want glare from plastic sleeves removed, so that photos aren't ruined by reflections.**
- AC: The app prompts for 2–3 tilted shots when glare is detected.
- AC: The fused result shows no visible specular highlights on the test set.
- AC: Users can skip the glare pass.

**US-2.5 · As a user, I want to import photos I already took or scanned, so that I can restore them too.**
- AC: PHPicker import of 1–100 images.
- AC: Imported images go through the same split step (optional).

## E3 · Backs, dates & captions (v1)

**US-3.1 · As a user, I want to flip a print and capture its back, so that the writing is kept with the photo.**
- AC: "Capture back" is offered per item and for a batch.
- AC: The back image is stored and linked to its photo.

**US-3.2 · As a user, I want the handwriting read into a caption, so that I don't have to retype it.**
- AC: OCR runs in Japanese + English.
- AC: The recognized text is editable before saving.

**US-3.3 · As a user, I want the date on the back to become the photo's date, so that it sorts into the right year in Photos.**
- AC: Parses common formats ('78, 1978, Aug 1978, 1978年8月).
- AC: Converts Japanese eras: 昭和53年 → 1978, 平成5年 → 1993, 元年 → year 1.
- AC: The date can be confirmed or edited before export.

**US-3.4 · As a user, I want to quickly set a decade when there's no date, so that photos still sort roughly correctly.**
- AC: The decade picker (1920s–2000s) applies to one item or all selected items.
- AC: Precision is stored as "decade".

## E4 · Restore (v1)

**US-4.1 · As a user, I want to see a before/after of my own photo right away, so that I know the app works for me.**
- AC: The first restore preview is shown within 20 s of finishing the first page on iPhone 15.
- AC: The preview is at full quality, with no watermark.

**US-4.2 · As a subscriber, I want one tap to repair fading, color cast, noise and low resolution, so that photos look like they did originally.**
- AC: Pipeline steps 3–5 run (see SPEC §7).
- AC: 12 MP output in < 15 s on iPhone 15.

**US-4.3 · As a subscriber, I want face clean-up to be optional with a strength slider, so that my relatives still look like themselves.**
- AC: Off by default.
- AC: The slider runs 0–100% and previews in < 1 s per face.
- AC: Applies per photo or to all photos.

**US-4.4 · As a subscriber, I want to colorize black-and-white photos optionally, so that I can choose the look.**
- AC: Offered for detected grayscale images only.
- AC: Has a strength slider.
- AC: The original detail is preserved (Lab combine).

**US-4.5 · As a user, I want to compare before and after and see what changed, so that I trust the result.**
- AC: Swipe compare and hold-to-see-original.
- AC: The "What changed" heat map can be toggled.

**US-4.6 · As a user, I want to restore a whole project at once, so that I don't tap 200 times.**
- AC: "Restore all" with progress.
- AC: Can be paused and resumed.
- AC: Continues while the app is in the foreground (background in v1.1).

**US-4.7 · As a user, I want the app to work while the AI models download, so that I can start scanning right away.**
- AC: Scanning works with no models.
- AC: Model download progress is shown on the restore screen.
- AC: The download resumes after interruption.

## E5 · Save & share (v1)

**US-5.1 · As a user, I want everything saved into a Photos album named after my project, in album order, so that my library stays tidy.**
- AC: Album created or reused.
- AC: Order preserved.
- AC: Creation date = photo date (when known).

**US-5.2 · As a user, I want restored photos saved as edits I can revert, so that the original is never lost.**
- AC: The restoration is saved via PhotoKit editing, and "Revert" in Photos returns the original.

**US-5.3 · As a user, I want to share all or selected photos via LINE, AirDrop, Messages or email, so that my family gets copies.**
- AC: System share sheet.
- AC: Multi-select.
- AC: Export size options: Original or Share-size.

## E6 · Subscription (v1)

**US-6.1 · As a user, I want to see clear plans after my first before/after, so that I can decide while I see the value.**
- AC: The paywall shows plans, price per period, trial length and charge date.
- AC: The paywall can be dismissed. The free scanner keeps working.

**US-6.2 · As a US user, I want a weekly "Album Pass" with no trial, so that I can finish one project without a long commitment.**
- AC: Weekly $5.99 and yearly $39.99 with a 7-day trial (yearly is the default).

**US-6.3 · As a Japanese user, I want monthly and yearly plans in yen with no weekly option, so that pricing feels fair.**
- AC: ¥800/month and ¥4,800/year, with 「2ヶ月分お得」 framing.
- AC: The final confirmation screen meets 特定商取引法 requirements.

**US-6.4 · As a subscriber, I want my family to use my yearly plan, so that siblings can help digitize.**
- AC: Family Sharing is enabled on yearly.
- AC: Entitlement is recognized on family devices.

**US-6.5 · As a user who cancels, I want to keep all my photos, so that I don't feel trapped.**
- AC: Saved and restored photos remain in Photos.
- AC: The in-app library stays viewable.
- AC: Only new restores require Pro.

**US-6.6 · As a user, I want a reminder before my trial converts, so that I'm never surprised by a charge.**
- AC: Local notification 24 h before the trial ends (opt-in prompt during the trial start).

## E7 · Localization & accessibility (v1)

**US-7.1 · As a Japanese user, I want the app fully in natural Japanese, so that it feels made for me.**
- AC: 100% of strings localized and reviewed by a native speaker.
- AC: Japanese fonts render correctly.
- AC: IME-safe text fields.

**US-7.2 · As an older user, I want large text and clear controls, so that I can use it comfortably.**
- AC: Dynamic Type up to AX3 without clipping.
- AC: VoiceOver labels on all controls.
- AC: Minimum 44 pt touch targets.

## E8 · Family Album (v1.1)

**US-8.1 · As a subscriber, I want to create a Family Album and invite relatives by link, so that everyone sees the photos in one place.**
- AC: CloudKit share.
- AC: Invite via Messages, LINE or email.
- AC: Participants join with their Apple account.

**US-8.2 · As a relative, I want to add my own scans and fill in names and dates, so that the family archive grows together.**
- AC: Participants can add items and edit captions and dates. Changes sync within 1 minute.

**US-8.3 · As an album owner, I want to control who has access, so that family photos stay private.**
- AC: Owner can remove participants and stop sharing.
- AC: Read-only vs contributor roles.

## E9 · Deeper repair (v1.1)

**US-9.1 · As a subscriber, I want scratches, dust and small tears removed, so that damaged prints look whole.**
- AC: Scratch detection + inpainting.
- AC: The mask is editable (brush to add or remove).

**US-9.2 · As a subscriber, I want an optional cloud "Deep Restore" for badly damaged photos, so that I can rescue the worst ones.**
- AC: An explicit consent screen names the provider.
- AC: Fair-use queue disclosed.
- AC: Never on by default.

**US-9.3 · As a user, I want big batches to keep running when I lock my phone, so that I can restore 500 photos overnight.**
- AC: Background continued processing with a Live Activity progress view.

## E10 · Print (v1.1)

**US-10.1 · As a user, I want print-ready layouts (L-size, 4×6, 5×7), so that I can print at a convenience store or photo shop.**
- AC: Export print-ready JPEG/PDF at 300 dpi with bleed options.
