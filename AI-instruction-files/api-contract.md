# API Contract

These endpoints are implemented as Next.js Route Handlers (e.g., `app/api/chat/route.ts`) and accept/return JSON payloads.

---

### `POST /api/chat`
Communicates with the Single Coordinator Agent.

**Request Payload**:
```json
{
  "message": "Find the cheapest ingredients for spaghetti bolognese.",
  "conversationId": "73c683b5-78be-4c40-b472-35805abfb290" // Optional, for resuming chat
}
```

**Response Payload**:
```json
{
  "response": "I've found the ingredients for spaghetti bolognese. The cheapest option is to buy them at Pak'nSave for a total of $24.80. You can save $3.40 by substituting homebrand pasta.",
  "conversationId": "73c683b5-78be-4c40-b472-35805abfb290",
  "suggestedProducts": [
    { "id": "a1b2c3d4...", "name": "Beef Mince 1kg", "price": 14.50, "store": "Pak'nSave" },
    { "id": "e5f6g7h8...", "name": "Chopped Tomatoes 400g", "price": 1.20, "store": "Pak'nSave" }
  ]
}
```

---

### `POST /api/basket/optimize`
Calculates and compares basket totals across supermarkets.

**Request Payload**:
```json
{
  "items": [
    { "productId": "a1b2c3d4-...", "quantity": 2 },
    { "productId": "e5f6g7h8-...", "quantity": 1 }
  ]
}
```

**Response Payload**:
```json
{
  "totals": {
    "Woolworths": 32.50,
    "Pak'nSave": 28.90,
    "New World": 33.10
  },
  "cheapestSingleStore": "Pak'nSave",
  "savingsOpportunity": 4.20, // Savings compared to most expensive store
  "splitBasket": {
    "total": 26.50,
    "savings": 2.40, // Additional savings if items bought at separate stores
    "items": [
      { "productId": "a1b2c3d4-...", "storeName": "Pak'nSave", "price": 12.00, "quantity": 2 },
      { "productId": "e5f6g7h8-...", "storeName": "Woolworths", "price": 2.50, "quantity": 1 }
    ]
  }
}
```

---

### `GET /api/deals`
Fetches current active discounts and promotions stored in the database.

**Query Parameters**:
- `category` (string, optional): e.g., `Pantry`
- `store` (string, optional): e.g., `Woolworths`

**Response Payload**:
```json
[
  {
    "productId": "d5e6f7g8-...",
    "name": "Anchor Butter 500g",
    "storeName": "New World",
    "price": 5.99,
    "originalPrice": 7.20,
    "description": "Save $1.21"
  }
]
```

---

### `GET /api/dashboard/summary`
Retrieves saving statistics and metrics for the logged-in user.

**Response Payload**:
```json
{
  "totalSaved": 148.75, // Cumulative user savings
  "itemsTracked": 14,
  "preferredStores": ["Woolworths Albany", "Pak'nSave Albany"],
  "recentLists": [
    {
      "id": "c1d2e3f4-...",
      "name": "Weekly Staples",
      "itemCount": 8,
      "totalCost": 64.30,
      "updatedAt": "2026-06-15T02:00:00Z"
    }
  ]
}
```