# API Contract

POST /api/agent/chat

Purpose:

Interact with AI assistant

Request:

{
  "message":"Find the cheapest basket for these items"
}

Response:

{
  "response":"..."
}

---

POST /api/agent/basket

Purpose:

Optimize grocery basket

---

POST /api/agent/compare

Purpose:

Compare stores

---

POST /api/agent/monitor

Purpose:

Create monitoring task

---

GET /api/deals

Returns:

Current promotions

---

GET /api/dashboard

Returns:

User savings summary