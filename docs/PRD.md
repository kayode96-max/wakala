# Wakala — Product Requirements Document

**Status:** Draft for the W3Node Node Engine hackathon  
**Dates:** 12–14 November 2026, Cape Town  
**Track:** Mobile Money & Peer-to-Peer (sponsored by Tether: USD₮ and WDK)  
**Author:** Usman Olukayode  
**Name:** Wakala is a **provisional** working name. It is Swahili for “agent”, is likely used elsewhere, and has not been cleared for trademark or domain conflicts.

This document specifies what Wakala should do during the hackathon and immediately afterwards. It is the source of truth for scope. Protocol design is elaborated in [WHITEPAPER.md](WHITEPAPER.md).

**Judging note:** A numeric rubric was published for the Merchant & Machine Payments track (Product design 33%, Technical UX 34%, Business / market fit 33%). This document **assumes** Mobile Money & Peer-to-Peer is judged similarly until the organisers confirm otherwise. Official Node Engine language emphasises real-world problems, a builder mindset and technical proficiency.

---

## 1. Overview

Wakala is a self-custodial USD₮ wallet and agent marketplace that swaps USD₮ for mobile money (and the reverse) through nearby agents or peers. USD₮ is locked in an escrow smart contract. It is released only when a mobile-money provider confirms the fiat leg, or when the recipient confirms in-app within a timeout and dispute window. Screenshots and unofficial SMS alerts are not valid proofs. Agents post a USD₮ bond; reputation and limits follow their record.

The first vertical slice is **cash-out** (USD₮ → M-Pesa). Cash-in, send-to-phone-number and USSD are lower priority.

The product is intended to work worldwide wherever agents and instant payment rails exist, and is designed first for East, West and Southern Africa.

## 2. Goals and non-goals

### Goals

- Let a person holding USD₮ **obtain local currency in minutes** through an agent they can evaluate (price, bond, rating, speed).
- Replace “I have been paid — please release” with **escrow that listens to the payment network**.
- Use **Tether WDK** and USD₮ as the default wallet and asset, in line with the track sponsor.
- Give agents a way to **earn a spread on idle float** without taking uncollateralised credit risk on the crypto leg.
- Ship a **publicly deployed** demo in three days: lock, mobile-money payment, automatic release, and a visible failure when a screenshot is offered as “proof”.

### Non-goals (hackathon)

- Becoming a licensed exchange, broker or money-transmitter, or holding user fiat.
- Integrating every African mobile-money operator. One Daraja sandbox path is enough; MoMo is a should-have.
- A fully automated dispute tribunal or legal collections process.
- Production KYC/AML, travel-rule tooling, or sanctions screening (the roadmap should name them; the demo may use lightweight attestations only).
- Replicating Machankura’s Bitcoin-over-USSD wallet as the core product (USSD is a claim channel only, and a could-have).
- Cross-chain bridging as a primary feature.
- Building a general-purpose DEX or order book for arbitrary tokens.

## 3. Personas

### Amina — freelancer paid in USD₮ (primary user)

Lives in Nairobi or Lagos. Receives USD₮ for remote work. Needs shillings or naira the same day for rent and airtime. Has been asked to release crypto against a payment screenshot on informal P2P desks. Wants a clear quote, a short wait, and certainty.

### Joseph — mobile-money agent (primary agent)

Runs an M-Pesa agency. Some days he has surplus e-float; some days he is short. He already handles cash-in and cash-out for the public. He will move USD₮ only if the crypto is locked before he sends shillings, and if a dishonest counterparty can cost him no more than a defined process allows.

### Kwame — peer liquidity provider (secondary)

Not a registered agent, but will fill small cash-out requests. He has a lower limit and a smaller bond. The product must not pretend he is a licensed operator.

### Grace — recipient without a wallet (secondary)

Someone sends her USD₮ to her phone number. She may have only a feature phone. She needs a claim path (SMS link now; USSD later) and an optional cash-out at an agent.

### Platform operator (secondary, later)

A marketplace that pays many freelancers and wants bulk off-ramps. Out of scope for the event except as a business-model slide.

## 4. User stories

1. As Amina, I can create a self-custodial USD₮ wallet (WDK) and see my balance without holding a gas token.
2. As Amina, I can request a cash-out, see quotes from agents (rate, fee, bond, rating, estimated speed), and accept one.
3. As Amina, I can see my USD₮ lock in escrow and track the order: locked, waiting for payment, released or refunded.
4. As Joseph, I can list a quote, show my bond, and accept an order only when escrow is confirmed locked.
5. As Joseph, I can send mobile money to the number on the order and have the escrow release when the provider confirms, not when I upload a screenshot.
6. As Amina, if I am paid by a small agent without a provider API, I can confirm receipt in-app, or wait for a timeout refund if they never pay.
7. As either party, I can open a dispute within the window if the fiat leg is missing or wrong.
8. As Amina, I cannot be forced to accept a screenshot as proof.
9. As Joseph, I understand that a lost dispute may slash my bond.
10. As Grace, I can be told by SMS that funds are waiting and claim them (should / could).
11. As a judge, I can watch a sandbox M-Pesa payment trigger an on-chain release, and a fake screenshot change nothing.

## 5. Functional requirements (MoSCoW)

### Must have

| ID | Requirement |
| --- | --- |
| M1 | User can create or import a WDK wallet and receive and send USD₮ (gasless if the paymaster is available). |
| M2 | Agent can register a profile (display name, rail, payout number, bond amount once M8 exists). |
| M3 | Quote list or simple order book for USD₮ → local-currency cash-out (at least one corridor, e.g. USD₮ → KES via M-Pesa). |
| M4 | Escrow contract: lock, release, and refund-after-timeout, with tests. |
| M5 | Cash-out happy path: user locks USD₮; agent is instructed to pay; **Daraja sandbox callback** is accepted by a backend attester; escrow releases to the agent. |
| M6 | Callback handler is authenticated and **idempotent**. Duplicate callbacks do not double-release. |
| M7 | Realtime order timeline in the app (locked → paid / pending → released or refunded). |
| M8 | Agent bond: a USD₮ amount locked in a bond contract or module, visible on the profile (even if slashing is incomplete). |
| M9 | A screenshot or manual “I swear I paid” control, if shown at all, **does not** release escrow. The demo must make this obvious. |
| M10 | Public deployment of the web application (Vercel), plus a documented testnet (or Anvil-for-video plus one public testnet) contract address. |

### Should have

| ID | Requirement |
| --- | --- |
| S1 | Cash-in path (local currency → USD₮): agent pre-locks USD₮; user pays the agent’s till; callback releases USD₮ to the user. |
| S2 | Recipient-confirm path for small agents, with a clear timeout and refund. |
| S3 | Dispute window and a simple resolver (even a demo admin key, clearly labelled as centralised for the hackathon). |
| S4 | Bond slashing on a lost dispute. |
| S5 | Send-to-phone-number: lock, SMS claim link (Africa’s Talking), then wallet or cash-out. |
| S6 | Agent map or distance-sorted list. |
| S7 | MTN MoMo request-to-pay / callback as a second rail. |

### Could have

| ID | Requirement |
| --- | --- |
| C1 | USSD claim and balance check for feature phones. |
| C2 | On-chain reputation scores and automatic limit increases. |
| C3 | Multi-corridor FX routing and inventory management for super-agents. |
| C4 | Bulk payouts for platforms. |
| C5 | A large-text, low-data mode and additional languages. |

### Won’t have (this event)

| ID | Requirement |
| --- | --- |
| W1 | Custodial holding of user USD₮ or fiat by Wakala as a principal (the protocol may use a narrow attester key; it must not take custody of user balances). |
| W2 | Release on user-uploaded screenshots, PDFs or raw SMS text as sole proof. |
| W3 | Production KYC vendor integration and travel-rule messaging. |
| W4 | A Bitcoin or Lightning wallet (out of scope; Machankura already occupies Bitcoin-over-USSD). |
| W5 | Unbounded peer trading with no limits, bonds or timeouts. |

## 6. Non-functional requirements

### Performance

- Quote list and order status should remain usable on a mid-range Android phone over 4G. Aim for a warm first interaction in under two seconds; this is a design target, not a measured baseline.
- Escrow lock and release should use account abstraction so the user is not blocked on acquiring gas.
- Realtime status should update without a full page reload.

### Reliability

- The contract, not the UI, is the source of truth for funds.
- Attestors and callbacks fail closed: no callback, no release (unless the recipient-confirm path is explicitly used).
- Timeouts must be tested. A stuck “pending” state is a defect.
- Node, paymaster or sandbox outages need a recorded backup demo.

### Accessibility

- Large typography and tap targets on cash-out and confirm screens.
- Status is never colour-only; use labels (“Locked”, “Released”, “Refunded”).
- A large-text, low-data mode is a should/could; if not shipped, typography should already be generous.
- Confirmations that move money require an explicit, labelled control — no hidden gestures.

### Security

- Self-custodial user keys stay on the client (WDK). The server never receives seed phrases.
- The attester key can only trigger release or refund according to contract rules, and only for orders it is authorised to sign. Compromise of the attester is a known risk and must be documented (see the white paper).
- Webhooks are verified (provider signatures or allow-lists and shared secrets).
- Idempotent release; re-entrancy and double-withdraw tests in Foundry.
- Row Level Security so users see their orders and agents see theirs.
- Rate-limit order creation. Enforce minimum and maximum amounts in the contract.
- No private keys in git, client bundles or screenshots used in the pitch deck.

## 7. UX flows and screens

### 7.1 Wallet onboarding

`Welcome` (one sentence: “Cash out USD₮ through bonded agents”) → create or import WDK wallet → PIN / device lock reminder → `Home` (balance, Cash out, later Cash in and Send).

Avoid seed-phrase lectures on the first screen; still show a backup reminder before any large inbound transfer if time allows.

### 7.2 Cash-out (must-have)

`Cash out` → enter USD₮ amount and destination phone number / rail → `Quotes` (rate, total local currency, fee, bond, rating) → `Confirm` (summary, timeout displayed) → `Locked` (explorer link) → `Waiting for Joseph to pay` → `Released` (receipts both sides) **or** `Refunded`.

### 7.3 Screenshot contrast (must show in the demo)

From `Waiting`, a control such as “Upload a payment screenshot” either does not exist or, if shown for education, explicitly **fails** with “Screenshots cannot release funds. Waiting for M-Pesa.”

### 7.4 Agent workspace

`Inbox` of open orders → accept → pay instructions → waiting for callback → released. Profile shows bond, completed trades and limits.

### 7.5 Recipient confirm (should-have)

`Waiting` shows “Confirm I received M-Pesa” for un-integrated agents, plus the countdown to refund.

### 7.6 Cash-in (should-have)

Symmetric: user sees pay-to-till instructions or an STK Push; release of USD₮ follows the callback.

### 7.7 Send to number (should-have)

`Send` → phone number → lock → “SMS sent” → recipient claim page → funds in wallet or cash-out CTA.

### Visual tone

Calm, high trust, more like a bank transfer than a trading terminal. Use plain language (“Your dollars are locked until Amina is paid”). Dark mode optional. Avoid memecoin aesthetics.

## 8. Success metrics

Hackathon success is demo-based. **No conversion baselines are claimed.**

| Metric | Target for the event |
| --- | --- |
| Happy path | Live or recorded-plus-live: lock, sandbox mobile-money payment, automatic release, balances updated |
| Safety path | A screenshot or false alert does not release funds |
| Deployment | Public HTTPS app; contract addresses documented |
| Agent clarity | A judge can explain the bond in one sentence after the demo |
| After the event (directional) | Time-to-release; spread versus RPW corridor averages; dispute rate; callback versus recipient-confirm mix — to be instrumented later |

## 9. Dependencies and integrations

| Dependency | Role | Notes |
| --- | --- | --- |
| Vercel | Hosting | Must-have |
| Supabase | Auth, orders, Realtime, Edge Functions for webhooks | Must-have |
| Tether WDK | Self-custodial, gasless USD₮ wallet | Track-sponsor tech; spike browser support on day one |
| Foundry and an EVM testnet | Escrow and bond | Must-have |
| USD₮ (or mock USD₮ on testnet) | Settlement asset | Use official mock or faucet; a small mainnet transaction is optional credibility, not required |
| Safaricom Daraja sandbox | Provider confirmation | Public callback URL required |
| MTN MoMo sandbox | Second rail | Should-have |
| Africa’s Talking | SMS claim | Should-have |
| Paymaster / bundler | Gasless transactions | Required for a smooth demo |
| Licensed ramps (Yellow Card, Kotani Pay, Fonbnk) | Optional extra liquidity, not the core trust model | Do not build a wrapper and call it the product |

## 10. Judging rubric mapping

Assuming the 33 / 34 / 33 split also applies to this track:

| Pillar | Weight | How Wakala is designed to score |
| --- | --- | --- |
| **Product design** | 33% | Uber-style quotes, a calm order timeline, large-text confirmations, an explicit anti-screenshot moment, and an agent profile that makes the bond visible. Design effort should not be sacrificed for protocol features. |
| **Technical UX** | 34% | WDK integration, a tested escrow and bond state machine, authenticated idempotent callbacks, Realtime status, account abstraction, and a public deployment. Architecture should be readable in the repo. |
| **Business / market fit** | 33% | Uses the 17 million-agent network, the 8.46% remittance-cost benchmark, stablecoin usage in the region, the Binance NGN withdrawal, and screenshot fraud. Integrates M-Pesa / MoMo. Positions licensed ramps as partners. Honest about regulation. |

If the official P2P rubric differs, map the same evidence to the published criteria at kick-off and do not invent a different product.

## 11. Risks and mitigations

| Risk | Impact | Mitigation |
| --- | --- | --- |
| Regulatory: P2P crypto-fiat exchange | Pitch or partner concern | Frame as software; agents and ramps are the regulated principals where required; name KYC as a roadmap item; do not claim a licence |
| Attester-key centralisation | Honest security review fails | Document the trust model; keep the key’s power narrow; mention multi-sig or provider-native attestations as next work |
| Small agents lack APIs | Callback-only design excludes them | Recipient-confirm plus timeout and dispute (should-have); do not block the must-have callback demo |
| Daraja sandbox fragility | Demo failure | Backup video; also script an Anvil-only release with a mocked callback signature for emergency use, labelled as such |
| WDK browser support | Lost day | Morning spike; React Native fallback only if filmed separately — prefer a working web wallet |
| Scope creep (USSD, maps, multi-rail, disputes) | Unfinished cash-out | Cash-out first; freeze must-haves end of day one |
| Naming (“Wakala”) already used | Brand confusion | Keep the working-name disclaimer in the README and pitch |
| Liquidity in a demo | Empty quote list | Seed at least two scripted agents with inventory |
| Screenshot fraud is the bug we claim to fix, but a UI shortcut sneaks in | Credibility loss | Code review the release path; automated test that unauthenticated proof cannot release |

## 12. Milestones (Days 1–3)

### Day 1 — Thursday 12 November: protocol and one callback

- Scaffold Next.js, Supabase (users, agents, orders), Foundry project.
- Implement and test escrow: lock, release (attester), refund-after-timeout.
- WDK wallet: create, show balance, send/receive on the chosen chain (check sponsorship quests).
- Stand up a public Daraja callback endpoint (even if the UI is ugly).
- End of day: a scripted lock + mocked or sandbox callback + release works.

### Day 2 — Friday 13 November: marketplace UX

- Quotes, accept, order timeline, agent profile with bond.
- Wire the sandbox payment so a human can complete the path on a phone.
- Build the “screenshot does not release” demonstration.
- Start should-haves only if the happy path is stable (recipient confirm or SMS claim, not both if behind).
- Record backup video.
- End of day: a judge-ready walk-through on deployed preview.

### Day 3 — Saturday 14 November: polish, deploy, pitch

- Copy, empty states, explorer links, README commands updated only where they now work.
- Final deploy; fill contract addresses.
- Pitch: problem with sourced numbers, live escrow release, anti-screenshot moment, business model, regulatory humility.
- Freeze features at least two hours before judging.

## 13. Open questions

1. Does Tether WDK run in a Next.js browser app, or must the team use the React Native quickstart?
2. Which chain, USD₮ contract and paymaster will the sponsorship quests prefer?
3. Who holds the attester key in the demo, and what is the minimum viable path to decentralising it after the event?
4. Will Safaricom Daraja sandbox credentials be obtainable before Friday, and will callbacks reach Vercel without IP allow-list issues?
5. What FX rate source is acceptable for quotes — a manual rate, a public API, or a gap the agent simply sets?
6. What maximum trade size is responsible for a demo with a simple attester?
7. How should Nigerian cash-out work without Binance NGN and without assuming a particular bank API — a later ramp partner?
8. What lightweight identity, if any, should agents show in the demo without pretending it is regulated KYC?
9. Is “Wakala” too generic or already taken for a public GitHub name?

---

*End of PRD. Related: [README](../README.md), [white paper](WHITEPAPER.md).*
