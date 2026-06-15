# Tool & Price Discovery Framework

To ensure fast response times, 100% free operation, and to avoid anti-bot blocks, the platform operates **completely without background scrapers or Playwright/browser automation.**

Instead, the Coordinator Agent uses backend helper functions (tools) that perform a mix of **live API fetching** and **price simulation**.

---

## 1. Price Discovery Logic

- **Woolworths (Live API Fetch)**:
  - We execute a direct HTTP `fetch` to Woolworths' public search API endpoint.
  - Endpoint: `https://www.woolworths.co.nz/api/v1/products?searchTerm={query}&page=1&size=20`
  - This request takes `<1` second, returns standard JSON, requires no authentication, and is not blocked by standard firewalls.
- **Pak'nSave & New World (Simulation)**:
  - Since Foodstuffs supermarkets block direct API fetches and web scrapers, their prices are simulated on-the-fly relative to Woolworths live prices.
  - **Pak'nSave Simulation**: Generates a simulated price between **5% to 15% cheaper** than the Woolworths price (reflecting Pak'nSave's low-price position).
  - **New World Simulation**: Generates a simulated price between **-5% to +5%** of the Woolworths price.
  - Both simulated prices are formatted into realistic currency values (e.g. ending in `.00`, `.49`, or `.99`).

---

## 2. Agent Tools (Backend Functions)

The Coordinator Agent is equipped with the following tool functions to find and compare prices:

### `SearchProducts`
Searches Woolworths' live API for matching products, and simulates prices for Pak'nSave and New World.
- **Input Parameters**:
  - `query` (string): The search term (e.g. "milk", "butter", "bananas").
- **Execution**:
  1. Calls the Woolworths search API.
  2. Parses results to get product name, brand, package size, barcode, image URL, and Woolworths price.
  3. For each product, generates simulated prices for Pak'nSave and New World.
- **Output**: Array of products:
  ```json
  [
    {
      "productId": "woolworths-barcode-12345",
      "name": "Standard Milk 2L",
      "brand": "Woolworths",
      "size": "2L",
      "barcode": "12345",
      "imageUrl": "...",
      "prices": {
        "Woolworths": 3.20,
        "Pak'nSave": 2.85, // Simulated
        "New World": 3.30  // Simulated
      }
    }
  ]
  ```

### `GetDeals`
Returns current Woolworths promotions (live) and mock Foodstuffs deals.
- **Input Parameters**:
  - `category` (optional string): e.g. "Produce", "Bakery".
- **Execution**:
  1. Queries Woolworths API with specific promo filter parameters (e.g. `isSpecial=true`).
  2. Applies a simulator to label random items in Pak'nSave and New World as "Special/On Promotion" with a discount label (e.g., "Save $1.20").
- **Output**: Array of promotional items.

### `BuildBasket`
Calculates and compares the total cost of a shopping list across stores.
- **Input Parameters**:
  - `items` (array of objects): `[{ "productId": "barcode", "quantity": number, "price": number }]`
- **Output**: Comparison totals:
  - Total cost if bought entirely at Woolworths.
  - Total cost if bought entirely at Pak'nSave (simulated).
  - Total cost if bought entirely at New World (simulated).
  - The optimized "split basket" option (buying each item at the cheapest store) and the total potential savings.