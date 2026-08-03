# Demo video — 2:30 script

**I cannot produce this.** Recording a screen and hosting a video are both things
you have to do. Everything else in `media/` is generated and ready; this is the
one required item that needs your hands and your voice.

Shortest path: QuickTime → **File ▸ New Screen Recording** (⌃⌘N), then upload
unlisted to YouTube and paste the link.

---

## Before you record

The app has to exist. Generate it in native.builder first (prompts 1–3 minimum —
inbox, detail, and push), publish it to its `*.nativelyai.app` URL, and open that
URL on your phone. Record the phone if you can mirror it; otherwise record the
browser at a narrow window so it renders as the mobile layout it is.

Have `npm run export:approvals` ready in a terminal in another window.

---

## 0:00–0:20 — the wall

> "This agent worked out the warehouse stocks out in three days. It drafted the
> purchase order. And then it did what every ops agent does — it sent someone a
> notification, and a human opened a supplier portal and typed in a card number.
>
> The agent did the thinking. The person did the paying."

## 0:20–0:40 — why it stayed unsolved

> "The obvious fix is to give the agent a card. Which is worse — now a model with
> a plausible hallucination has your card.
>
> Finance teams haven't handed spend authority to agents because nobody could
> answer one question: who authorized this, and what stopped it going further?"

## 0:40–1:00 — the two limits

*Slide 4 of the deck on screen, or say it over the app.*

> "There are two limits on an agent's spending, and people conflate them. The
> agent's own threshold is what we let it do without a human — a line in our
> config, which a bug can step over. The mandate cap is what Visa will actually
> authorize — enforced by the network, but blind to whether this particular spend
> makes sense.
>
> A spend is safe to make automatically only when both agree. Everything else
> comes here."

## 1:00–1:50 — the product, on a phone

*This is the part that matters. Slow down.*

> "Three requests are waiting."

*Open the inbox. Let the three cards sit on screen for a beat.*

> "Restock Agent, $3,780 to Acme Supply. The headline isn't the amount — it's
> **why this is my decision**: it's over that agent's $2,500 limit. Not a
> mystery, not a transaction I have to go investigate."

*Tap into the detail.*

> "Here's the agent's reasoning. Here's what already cleared. And here's the part
> that makes this signable — **the clause that authorizes it**, quoted, from
> Procurement Policy section 3.2. Not the agent telling me it's fine. A document.
>
> And the money comes from the Acme mandate, which has $8,000 left."

*Approve. Show the confirm sheet, then the card leaving the inbox.*

> "Two taps. Under ten seconds. Standing up."

## 1:50–2:15 — the second card

> "Now the interesting one. ShipStation, $1,120 — and there's **no standing
> authorization at all**. Approving this doesn't draw on a budget; it mints a
> single-use card, and I confirm with a passkey.
>
> Same queue, completely different decision. The card tells me which one I'm
> making before I have to work it out."

## 2:15–2:30 — close

> "Countersign was built in native.builder — the screens, the database, the auth,
> the push notifications, the ingest endpoint, the deployment. All of it.
>
> Upstream is a governance engine I'd already written that decides which spends
> need a person. It had never had a human surface. This is it.
>
> Your agents can spend now. This is who approved it."

---

## Shot list

- [ ] The inbox with three cards, legible
- [ ] `needsHumanBecause` highlighted — pause on it
- [ ] The policy citation block in detail view
- [ ] An approval going through: confirm sheet → card leaves inbox
- [ ] **A push notification arriving**, ideally on a real phone. This is the
      single most convincing shot available and worth retaking until it lands.
- [ ] The no-mandate card, to show the two decisions are different
- [ ] Brief: `npm run export:approvals` producing the feed, to show it is real data

## Notes

- Lead with the refusal-shaped card, not the purchase. Anyone can demo a purchase.
- Say "authorized", not "verified" — the policy authorizes; nothing is verified.
- Do not claim the app is a native iOS app. It is a mobile web app, installable to
  the home screen. That is what native.builder produces, and saying otherwise is
  the kind of thing a judge checks.
- Don't show a card number, even a test one.
