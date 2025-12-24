---
name: Gemini chat backend
overview: Add a small Node/Express backend (for Render/Railway) that proxies Gemini API calls and sends email notifications via SMTP; update the ChatWidget to call the backend for dynamic AI replies.
todos:
  - id: backend-scaffold
    content: Create Express server, static hosting, and /api/chat endpoint; add env config + rate limiting
    status: pending
  - id: gemini-integration
    content: Integrate Gemini SDK wrapper and prompt format; return assistant reply JSON
    status: pending
    dependencies:
      - backend-scaffold
  - id: email-notify
    content: Add SMTP mailer and send notification on each user message (with IP/user-agent/session)
    status: pending
    dependencies:
      - backend-scaffold
  - id: frontend-wire
    content: Update ChatWidget to call /api/chat with sessionId and show Gemini responses; keep quick question chips
    status: pending
    dependencies:
      - gemini-integration
      - email-notify
---

# Integrate Gemini chat + email notifications

## Key constraint

**Gemini API keys and SMTP credentials cannot be safely placed in `index.html`** (they would be exposed to visitors). We’ll add a small backend for Render/Railway and have the frontend call it.

## Files / modules to add

- `server/server.js` (Express API + static hosting)
- `server/gemini.js` (Gemini client wrapper)
- `server/mailer.js` (SMTP email notifications)
- `server/rateLimit.js` (basic throttling)
- `server/package.json` (dependencies + start script)
- `.env.example` (Gemini + SMTP env vars)

## Files to update

- `index.html` (ChatWidget: call `/api/chat` for replies; keep UI/typing indicator)

## Implementation steps

- Backend (Render/Railway)
- Serve the existing site as static files.
- Implement `POST /api/chat` that accepts `{ sessionId, message, history }`.
- Call Gemini (Google AI Studio key) to generate the reply.
- Send an **email notification on every user message** (per your choice) via SMTP, including:
    - message text (trimmed)
    - timestamp
    - IP/user-agent (basic “who/where” signal)
    - sessionId
- Add basic anti-spam protections:
    - rate-limit per IP/session
    - cap message length
- Frontend
- Generate a persistent `sessionId` (store in `localStorage`).
- Replace the current “templated replies” with a fetch call to `/api/chat`.
- Keep the 2–3 quick question chips (they just send messages, now answered by Gemini).

## Getting the Gemini API key (you selected: need steps)

- Create a key in Google AI Studio, then set it as `GEMINI_API_KEY` in Render/Railway env vars.