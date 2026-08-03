# The approval card contract

One card is one decision. The shape is a **projection** of MetaboSpend's evidence
packet, not a dump of it — a card carries only what someone needs in order to say
yes or no.

## Fields

| Field | Type | Notes |
|---|---|---|
| `id` | string | Stable. Also the idempotency key upstream. |
| `agent` | string | Which agent proposed it. |
| `kind` | `purchase` \| `renewal` \| `topup` | Drives the icon only. |
| `amountDisplay` | string | Pre-formatted. **The client never does money math.** |
| `amountMinor` | integer | Minor units, for sorting and totals. Never displayed. |
| `currency` | string | ISO 4217. |
| `merchant.name` / `.url` | string | Hostname is what the governor matched on. |
| `why` | string | The agent's reasoning, one sentence. |
| `needsHumanBecause` | string | **The headline.** Why this is a human's decision. |
| `gatesPassed` | string[] | Short chips for what already cleared. |
| `reasonCodes` | string[] | Machine-readable. For filtering; never a label. |
| `authorizedBy` | `{source, excerpt}[]` | The policy clause. Empty means nothing authorizes it. |
| `fundingSource` | string \| null | Mandate context in words. `null` means a one-time card gets minted. |
| `queuedAt` | ISO 8601 | Client renders relative age. |
| `decisionRef` | string | Opaque. Returned with the decision. Never displayed. |

## Deliberately withheld

**Payment credentials.** Network tokens and single-use cryptograms never leave the
server. A phone has no business holding one.

**Raw mandate ids.** A person recognises "the Acme mandate, $8,000 left", not
`mdt_01KYT…`. The id travels inside `decisionRef` for the callback only.

**Reason codes as labels.** `over_agent_cap` is precise and useless to a human.
`needsHumanBecause` is the same fact in a sentence.

## The decision callback

```
POST {webhook}/approvals/{id}/decide
{ "id": "...", "decisionRef": "...", "decision": "approve" | "reject", "note": "..." }
```

A decision is final. Re-deciding is an error, not an update — upstream, the ledger
row is the idempotency key, and a replayed approval must not become a second
charge.
