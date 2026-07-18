# The Guardian 🛡️

A voice AI agent that owns your entire trip lifecycle — planning, booking, protecting, recovering. Not a one-time booking bot.

Built at the Sabre × Vocal Bridge hackathon (July 2026).

## The four phases

1. **Listen** — parses screenshots/posters and casual chat mentions (LandingAI) into a draft itinerary, no explicit command needed.
2. **Win** — negotiates each trip leg across vendors by live voice call (Vocal Bridge), then pays via PayPal sandbox.
3. **Guard** — monitors bookings; proposes better options and acts only after a one-word voice confirm. *(roadmap)*
4. **Recover** — a disruption on one leg cascades fixes across the whole remaining day, then the agent briefs you by voice: "here's your new day."

## Repo layout — two people, zero merge conflicts

| Folder | Owner | What lives here |
|---|---|---|
| `backend/` | Khushi | Node/Express "trip brain": shared trip-state API, Vocal Bridge calls, bidding engine, Sabre booking, PayPal payment, disruption cascade |
| `frontend/` | Teammate | Next.js dashboard: live trip board, event feed, LandingAI screenshot parsing, demo controls |
| `docs/CONTRACT.md` | **FROZEN** | The shared schema + REST API both halves build against. Change it only by talking to each other first. |

## Rules of engagement

- Never edit the other person's folder.
- `docs/CONTRACT.md` is the only coupling point. Read it first, build to it exactly.
- Everything works in MOCK mode first; real API keys swap in behind flags without changing shapes.
- PayPal is **sandbox only**, always.
- No real hotels/airlines/restaurants get called — vendor calls are Vocal Bridge ↔ Vocal Bridge or mocked.

## Getting started

```bash
# Khushi
cd backend && cp .env.example .env   # fill keys as they arrive from the hackathon Discord

# Teammate
cd frontend && cp .env.example .env.local
```
