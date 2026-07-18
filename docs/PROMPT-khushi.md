# Khushi's prompt — paste into Claude Code inside `backend/`

```
Build the backend for "The Guardian," a voice AI travel agent, in this backend/ folder. I own the LIVE VOICE + BOOKING + PAYMENT half. My teammate owns the dashboard UI and visual parsing in ../frontend — do NOT touch that folder. The frozen shared interface is ../docs/CONTRACT.md: read it first and implement it exactly. Do not change any route or JSON shape.

TECH STACK: Node.js + Express, plain REST, in-memory trip store (a dict keyed by trip_id is fine; SQLite only if trivial). Port 4000. Read config from .env (see .env.example). Optimize for "works reliably live on stage in 5 hours" over architectural purity.

APIS: Vocal Bridge (voice), Sabre (flight/hotel search + booking), PayPal (payment, SANDBOX ONLY). I will paste real docs/SDK snippets from the hackathon Discord as I get them — when I do, adapt to the actual method signatures instead of guessing. Until then, build against clearly-labeled mocks (MOCK_SABRE_BOOK, MOCK_PAYPAL_CHARGE, MOCK_VB_CALL) behind the MOCK=1 env flag, each with a TODO marking the swap-in point, so nothing ever blocks on missing credentials.

BUILD ORDER — checkpoint after each step, and after each step give me a curl command to verify it:

STEP 0 (first 30 min): The shared trip-state API from CONTRACT.md — POST /trip, GET /trip/:id, PATCH /trip/:id/leg/:legName, POST /trip/:id/events, plus 202 stubs for /bid and /disrupt. My teammate's dashboard consumes this immediately, so it ships first and must be contract-perfect, with realistic seeded demo data (a JFK→SFO flight, a hotel, an 8pm dinner, a car pickup).

STEP 1 (hour 1-2): Bidding War Engine for the hotel leg, behind POST /trip/:id/bid/hotel.
- 2-3 mock vendor agents (local functions), each with a name, base price, perks, and a counter-offer function that drops price when told a competitor's offer.
- Bidding loop: get vendor A's price, tell vendor B "another property offered $X with breakfast, can you match or beat it?", collect counters, max 2-3 rounds, pick the winner.
- Voice it through Vocal Bridge so the negotiation audibly happens (agent-to-mock-vendor-agent, or agent narrating each round). Never call real businesses — only DEMO_VENDOR_PHONE or VB-to-VB.
- Set leg status "bidding" at start; append one event per round (phase "win") so the dashboard shows the war live; on winner, PATCH the leg to "booked" with final price and details.

STEP 2 (hour 2-3): Sabre booking + PayPal payment, chained after a bid wins.
- Winner → Sabre booking call (or MOCK_SABRE_BOOK) → on success, PayPal sandbox charge for the agreed price (or MOCK_PAYPAL_CHARGE) → on success, write confirmation_id into details and append a "payment_success" event.
- Ordering guarantees: if Sabre fails, never charge. If payment fails, log the event and do NOT mark booked. Never a real charge outside sandbox.

STEP 3 (hour 3-4): Disruption Recovery behind POST /trip/:id/disrupt {"leg":"flight","reason":"delayed","delay_minutes":120}.
- Mark the leg "disrupted", re-derive every dependent booked leg with simple rules: flight delayed by X minutes → push dinner time and transport pickup by X, flag the hotel if arrival passes the check-in window.
- Re-run a mini bid to "rebook" the disrupted leg via mock vendors.
- GUARDRAIL before committing anything: validate the new plan (new arrival before hotel check-in, no overlapping times, rebook price delta under a sane cap). If a check fails, commit nothing, append an event naming the failed check. Log every check.
- On pass: apply all PATCHes, then a Vocal Bridge call voices the briefing: "Your flight was delayed, I've rebooked you and moved dinner and your pickup — here's your new day," reading back the updated details. Append everything as phase "recover" events.

STEP 4 (hour 4-5): Reliability. Run the two live-call moments (bid winner, recovery briefing) 3+ times back to back; fix flakiness. Clean logging so a live failure is diagnosable in seconds. Verify with my teammate that the dashboard reflects every state change.

Start with STEP 0 right now.
```
