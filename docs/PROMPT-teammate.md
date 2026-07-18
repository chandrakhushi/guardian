# Teammate's prompt — paste into Claude Code inside `frontend/`

```
Build the frontend for "The Guardian," a voice AI travel agent, as a Next.js (App Router) app in this frontend/ folder. I own the ITINERARY DASHBOARD + VISUAL PARSING half. My teammate Khushi owns the backend trip brain (voice calls, bidding, Sabre booking, PayPal) in ../backend — do NOT touch that folder. The frozen shared interface is ../docs/CONTRACT.md: read it first and consume it exactly. Do not change any route or JSON shape.

TECH STACK: Next.js + React + Tailwind, no heavy state library. The backend runs at NEXT_PUBLIC_BACKEND_URL (see .env.example), port 4000. This is a 5-hour hackathon: optimize for a gorgeous, legible, projector-friendly live demo over architecture. Do not use HTML <form> tags; use onClick/onChange handlers.

CRITICAL FIRST MOVE: create lib/fixtures.ts with a complete fake trip object matching CONTRACT.md exactly (flight JFK→SFO, hotel, 8pm dinner, car pickup, a few events), and build the entire UI against it behind a USE_FIXTURES flag. The backend may not exist for the first hour — I must never be blocked. Flip to real fetches at the end of each step.

BUILD ORDER — checkpoint after each step:

STEP 1 (hour 1-2): The live trip dashboard. This is the projector screen for the whole demo.
- Poll GET /trip/:id every 1.5s (SWR or a simple interval).
- Four leg cards (flight, hotel, dinner, transport) rendering details and price, with big status badges color-coded by state: draft (gray), bidding (amber, pulsing), booked (green), disrupted (red, attention-grabbing). Status transitions should animate — judges must SEE the state flip from across the room.
- A live event feed panel: the append-only events array, newest on top, auto-scrolling, timestamped, badge per phase (listen/win/guard/recover). During the bidding war this feed shows each round land in real time; make it feel alive.

STEP 2 (hour 2-3): Phase 1 "Listen" — visual parsing intake (this is our LandingAI award).
- A drop zone for a screenshot/poster/confirmation-email image. On drop, send it to a Next.js API route /api/extract which calls LandingAI ADE server-side (LANDINGAI_API_KEY stays server-only, never in the browser). I will paste the real LandingAI docs when I have them — until then return a clearly-labeled MOCK_EXTRACT result with the same shape: { traveler, draft_legs } per CONTRACT.md's details keys.
- Show the extracted fields ("what Guardian heard") for a beat, then POST /trip with the draft legs and route to the dashboard showing the new trip in draft state. The demo arc: messy screenshot in → structured trip out, no typing.

STEP 3 (hour 3-4): Demo controls + phase choreography.
- A control strip (subtle, bottom of dashboard): "Start bidding war" → POST /trip/:id/bid/hotel; "Simulate flight delay" → POST /trip/:id/disrupt with {"leg":"flight","reason":"delayed","delay_minutes":120}. Both just fire-and-forget (202) — all resulting drama arrives through polling as leg updates and events.
- During recovery, make the cascade legible: the disrupted flight card goes red, then dependent cards visibly update (dinner time shifts, pickup shifts) one by one as PATCHes land, then settle green. A "Here's your new day" banner when a phase-"recover" event announces completion.
- An "on a live call" indicator that lights up whenever recent events mention a voice call, so the audience connects the phone audio to the screen.

STEP 4 (hour 4-5): Polish + rehearsal. Big fonts and high contrast (projector!), empty/loading states that never look broken, kill all console errors. Run the full arc 3+ times against Khushi's real backend: screenshot → trip → bidding war → booked+paid → disruption → recovered day.

Start with the fixtures file and STEP 1 right now.
```
