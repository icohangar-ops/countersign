# native.builder prompts

Paste **Prompt 1** into the builder's input on
[builder.nativelyai.com](https://builder.nativelyai.com) and hit *Start building*.
Attach `contract/sample-feed.json` if the builder accepts file uploads — it does,
per its docs, and giving it the real payload beats describing one.

Then iterate with prompts 2–5, one at a time. The docs recommend extended
conversations over one enormous prompt, and the skeleton needs to be right before
the polish goes on.

Do **not** paste all five at once.

---

## Prompt 1 — the app and its data model

```
Build "Countersign" — a mobile-first web app where a finance approver clears
spending that AI agents have proposed. Optimise every screen for a phone held
one-handed; it will be used standing up, in under ten seconds per decision.

THE DOMAIN

Autonomous agents run an eCommerce operation and propose purchases. Most clear
automatically. Some need a human, and those land here. Each pending item is an
"approval card" with this exact shape:

{
  "id": "restock_PLT-900_2026-08-03",
  "agent": "Restock Agent",
  "kind": "purchase",
  "amountDisplay": "3780.00 USD",
  "amountMinor": 378000,
  "currency": "USD",
  "merchant": { "name": "Acme Supply", "url": "https://acmesupply.com" },
  "why": "PLT-900 is at 5 units in DC-1, below the 60-unit reorder point — 0 days of cover left at 9/day. Ordering 270 units for 30 days of cover.",
  "needsHumanBecause": "3780.00 USD is over the Restock Agent's 2500.00 USD limit",
  "gatesPassed": ["Funds authorized"],
  "reasonCodes": ["mandate_covers_spend", "over_agent_cap"],
  "authorizedBy": [{ "source": "Procurement Policy v4 §3.2", "excerpt": "Replenishment orders with approved suppliers may be issued by automated systems up to $10,000 per order." }],
  "fundingSource": "Acme Supply mandate · 8000.00 USD remaining",
  "queuedAt": "2026-08-03T19:41:53.975Z",
  "decisionRef": "cmVzdG9ja19QTFQtOTAwOm1kdF9hY21l"
}

Create a Supabase table `approvals` matching that shape, plus columns
`decision` (null | 'approved' | 'rejected'), `decided_at`, `decided_by`, and
`note`. Seed it with the three records in the attached JSON.

SCREENS

1. INBOX (home) — a scrollable list of cards where `decision IS NULL`, newest
   first. Each card shows, in this order of visual weight:
     - the amount, largest element on the card
     - merchant name, and the agent that proposed it, smaller
     - `needsHumanBecause` as a single highlighted line — this is the reason
       the person is being asked, and it is the most important text on screen
     - relative age from `queuedAt` ("queued 4 min ago")
   Show a count in the header: "3 waiting". Empty state: "Nothing waiting.
   Agents are operating within their limits."

2. DETAIL — tap a card. Same header, then:
     - `why` — the agent's reasoning, in full
     - `gatesPassed` as small chips
     - `authorizedBy` in a bordered quote block, source name above the excerpt.
       Label it "Authorised by". If the array is empty, show "No policy on file
       authorises this" in the warning colour.
     - `fundingSource` as a plain line. If null, show "No standing
       authorisation — approving mints a single-use card."
     - two actions pinned to the bottom of the viewport, thumb height:
       "Approve" (primary) and "Reject" (secondary, quieter).
   Approving opens a confirm sheet restating merchant and amount, with an
   optional note field. Rejecting requires a note.

3. DECIDED — a history list of everything with a decision, showing who decided,
   when, and any note. Read-only.

BEHAVIOUR

- A decision writes `decision`, `decided_at`, `decided_by`, `note`, then POSTs
  to a configurable webhook URL: { id, decisionRef, decision, note }. Put the
  webhook URL in a settings screen so it can be changed without a rebuild.
- A decision is final. Once set, the card leaves the inbox and cannot be
  re-decided. Show an error if it is attempted.
- Never display `reasonCodes`, `amountMinor`, or `decisionRef` to the user.
  They are for logic and the callback only.

DESIGN

Dark, calm, and serious — this is a finance tool, not a game. Near-black
background, one accent green for approve, amber for the "needs a human" line,
red reserved strictly for reject and for missing policy. Generous spacing,
large type, high contrast. No gradients, no drop shadows, no illustrations.
Money in a monospaced face so digits align down the list.
```

---

## Prompt 2 — the thing that makes it a phone app

```
Add web push notifications. When a new row is inserted into `approvals`, push
"$3,780 from the Restock Agent needs your approval" and deep-link to that
card's detail screen. Ask for permission on first visit, but only after the
user has seen the inbox once — not on cold open.

Make the app installable to the home screen (PWA manifest, icons, standalone
display) so it opens without browser chrome.
```

## Prompt 3 — auth

```
Add Supabase email auth. Only signed-in users can see or decide approvals. Set
`decided_by` from the signed-in user's email. Add row-level security so
approvals are only readable when authenticated.
```

## Prompt 4 — the live feed

```
Add a POST /ingest endpoint that accepts { generatedAt, pending: [...] } in the
attached shape and upserts each card into `approvals` on `id`, leaving already
decided rows untouched. This is how the upstream governor pushes new work in.

Secure it with a bearer token held in Supabase secrets.
```

## Prompt 5 — polish, only if time allows

```
Add to the inbox header a total of everything waiting ("$6,579 across 3
requests"). Add pull-to-refresh. Add a subtle amber left border on any card
queued more than an hour ago.
```

---

## Why it is written this way

**The data model is given, not described.** Handing the builder a real payload
removes the round of guessing where it invents field names and you spend prompts
correcting them. `contract/sample-feed.json` is genuine output from the upstream
governor, not a mock.

**`needsHumanBecause` is the most important string on the screen.** Everything
else is context. An approver's real question is not "what is this?" but "why is
this my decision?" — and the governor already knows the answer, so the card
should lead with it. Most approval UIs bury that and show a transaction instead.

**Reject requires a note; approve does not.** Asymmetric on purpose. A refusal is
information the upstream system needs in order to improve; an approval is just
the expected path.

**Prompt 2 is the one that earns the hackathon.** A list of pending items is a
web page. A push notification that arrives, deep-links to one decision, and
settles it in two taps is the product — and it is the reason this belongs on a
phone rather than in the dashboard it could have been.

**Nothing here asks the builder for a custom backend.** Supabase, an ingest
endpoint, a webhook out, push — all of it is inside what native.builder
advertises. That is deliberate: the submission should demonstrate the tool, not
work around it.
