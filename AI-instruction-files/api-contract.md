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
    {
      "barcode": "9310072026315",
      "name": "Beef Mince 1kg",
      "brand": "Woolworths",
      "size": "1kg",
      "prices": {
        "Woolworths": 14.50,
        "Pak'nSave": 12.80, // Simulated
        "New World": 14.20  // Simulated
      }
    },
    {
      "barcode": "9400262135682",
      "name": "Chopped Tomatoes 400g",
      "brand": "Essentials",
      "size": "400g",
      "prices": {
        "Woolworths": 1.20,
        "Pak'nSave": 1.05,  // Simulated
        "New World": 1.25   // Simulated
      }
    }
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
    { "barcode": "9310072026315", "quantity": 2, "basePrice": 14.50 },
    { "barcode": "9400262135682", "quantity": 1, "basePrice": 1.20 }
  ]
}
```

**Response Payload**:
```json
{
  "totals": {
    "Woolworths": 30.20,
    "Pak'nSave": 26.65, // Simulated (e.g. 5-15% cheaper)
    "New World": 30.70  // Simulated
  },
  "cheapestSingleStore": "Pak'nSave",
  "savingsOpportunity": 4.05, // Savings compared to New World (most expensive)
  "splitBasket": {
    "total": 26.65,
    "savings": 0.00,
    "items": [
      { "barcode": "9310072026315", "storeName": "Pak'nSave", "price": 12.80, "quantity": 2 },
      { "barcode": "9400262135682", "storeName": "Pak'nSave", "price": 1.05, "quantity": 1 }
    ]
  }
}
```

---

### `GET /api/deals`
Fetches current active promotions from Woolworths (live) and Foodstuffs (simulated).

**Query Parameters**:
- `category` (string, optional): e.g., `Pantry`

**Response Payload**:
```json
[
  {
    "barcode": "9400562134582",
    "name": "Anchor Butter 500g",
    "brand": "Anchor",
    "size": "500g",
    "prices": {
      "Woolworths": 5.99,
      "Pak'nSave": 5.49,
      "New World": 6.10
    },
    "isSpecial": true,
    "promotionDescription": "Woolworths Special: Save $1.21"
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
  "preferredStores": ["Woolworths Grey Lynn", "Pak'nSave Albany"],
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