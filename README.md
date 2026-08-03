# Countersign

> **Agents propose. Mandates authorize. You countersign.**

The human gate for agent spend, built for the
[native.builder "Build Without Limits" hackathon](https://lablab.ai/ai-hackathons/nativebuilder-build-without-limits)
(3–10 August 2026).

Autonomous agents can spend money now. Most of what they propose should clear on
its own. Some of it should not — and the moment it needs a person, that person is
almost never at a desk. Countersign is the phone surface for those decisions: a
push notification, one card, and two taps.

## The card

Each pending item answers three questions in the order an approver actually asks
them:

1. **Why is this my decision?** — `needsHumanBecause`. Over the agent's limit, no
   standing authorization, budget exhausted. This leads the card; everything else
   is context.
2. **What authorized it?** — the policy clause, quoted, with its source. Not the
   agent's own reasoning.
3. **Where does the money come from?** — the mandate and what is left on it, in
   words rather than an id.

Then: approve, or reject with a reason.

## Architecture

```
   MetaboSpend governor                native.builder app            Prava
   (upstream, pre-existing)            (built this week)
   ┌──────────────────────┐            ┌─────────────────┐
   │ six deny-first gates │            │  Inbox          │
   │        │             │  approval  │  Detail         │  decision
   │        ▼             │   cards    │  Decided        │  webhook
   │  approval lane  ─────┼───────────▶│                 │──────────▶ mandate
   │                      │   JSON     │  Supabase       │            charge
   └──────────────────────┘            │  web push       │              or
                                       │  PWA            │           passkey
                                       └─────────────────┘           session
```

The app — UI, database, auth, ingest, notifications, deployment — is built
entirely in native.builder. The governor upstream is a **data connection**, which
is one of the integrations native.builder advertises. Nothing here works around
the builder.

## What is in this repo

| Path | What it is |
|---|---|
| `docs/NATIVE_BUILDER_PROMPT.md` | The five prompts, in order, with the reasoning behind each |
| `contract/sample-feed.json` | Real output from the upstream governor — three pending cards, three different reasons |
| `contract/approval-card.md` | Field-by-field contract, including what is deliberately withheld |

## Prior work disclosure

Countersign is **new**, built during the event window.

Upstream of it sits [**MetaboSpend**](https://github.com/icohangar-ops/metabospend),
which I built 30 July – 2 August 2026 for the Agentic Commerce Hackathon and which
is public and MIT-licensed. MetaboSpend decides *which* spends need a human; it has
never had a human surface. That surface is what this project is.

The one file that crosses over is MetaboSpend's `src/scripts/export-approvals.ts`,
added during this event, which projects its evidence packets into the card shape
below. `contract/sample-feed.json` is its output.

Everything in the app itself — screens, data model, notifications, auth, ingest —
is generated in native.builder this week.

## License

MIT — see [LICENSE](LICENSE). Copyright (c) 2026 Shyam Desigan.

## Submission media

| File | For |
|---|---|
| `media/cover-16x9.png` | Cover image · 1920×1080, exactly 16:9 |
| `media/thumbnail-1x1.png` | Team thumbnail · 1024×1024 |
| `media/pitch-deck.pdf` | Slide presentation · 9 slides, 16:9 |
| `media/VIDEO_SCRIPT.md` | Demo video script — **the video itself has to be recorded by a human** |
| `media/src/` | The HTML each asset was rendered from, so any of it can be regenerated |

Everything except the video is generated and ready to upload.
