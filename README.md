# RescueAlbum

An iOS app that gets old family photo albums onto your iPhone. You get every photo split out, repaired, dated and shared, and faces still look like the real people.

> Status: planning. No code yet.

## Documents

| Doc | What's in it |
|---|---|
| [docs/SPEC.md](docs/SPEC.md) | Product and technical specification: positioning, features, architecture, photo-repair pipeline, sharing, monetization |
| [docs/PLAN.md](docs/PLAN.md) | Milestones, timeline, team split, risks, decisions to make |
| [docs/USER_STORIES.md](docs/USER_STORIES.md) | User stories with acceptance criteria, grouped by epic |
| [docs/TASKS.md](docs/TASKS.md) | Engineering task breakdown with IDs, estimates, owners and dependencies |

## In one paragraph

Families have hundreds of printed photos from before phone cameras. Today, getting them onto a phone means juggling Google PhotoScan (glare removal, abandoned since Nov 2022), Photomyne (splitting album pages, plus a subscription and extra credits), Remini or MyHeritage (restoration that often changes faces), a date-fixing tool, and retyping the notes written on the backs. RescueAlbum does the whole job in one flow, on the device, with honest unlimited pricing. Scanning is free. Restoring is a subscription.

## Tech at a glance

- Swift + SwiftUI, iOS 26+
- All processing runs on-device: Vision, Core Image, Core ML, PhotoKit
- No server in v1. Family album sharing in v1.1 uses CloudKit.
- StoreKit 2 subscriptions: weekly + yearly in the US, monthly + yearly in Japan
- English + Japanese at launch
