# Tool & Scraper Framework

To ensure fast response times and avoid anti-bot blocks from New Zealand supermarkets (Woolworths, Pak'nSave, New World), **the Coordinator Agent does not scrape websites in real-time.** 

Instead, data is queried from the Supabase database, which is updated asynchronously by a background scraper.

---

## 1. Background Scraper Architecture

The background scraper is a lightweight script executed on a cron schedule (e.g., once daily using GitHub Actions or a local task scheduler).

- **Technology**: Node.js + Playwright to handle client-side rendering and bypass basic Cloudflare checks.
- **Task**: 
  1. Navigate to NZ supermarket storefronts (Woolworths, Pak'nSave, New World).
  2. Query popular products, staples, and categories.
  3. Parse product name, brand, package size, barcode, current price, promotion details, and image URLs.
  4. Upsert parsed records into the Supabase database (`products` and `store_products` tables).
  5. Insert the latest prices into the `price_history` table for historical tracking.

---

## 2. Agent Tools (Database Queries)

The Coordinator Agent is equipped with the following tool functions to query the compiled price data:

### `SearchProducts`
Searches the database for matching products.
- **Input Parameters**:
  - `query` (string): The search query (e.g., 'milk', 'organic bread').
  - `category` (optional string): Category filter.
- **Output**: Array of matching products from the `products` table (including ID, name, brand, size, category).

### `ComparePrices`
Compares the pricing of a specific product across all available stores.
- **Input Parameters**:
  - `productId` (string: UUID): The target product ID.
- **Output**: Detailed price list showing store name, branch, current price, promotional description (e.g. "Save $1.50"), and in-stock status.

### `GetDeals`
Retrieves products that are currently discounted or on promotion.
- **Input Parameters**:
  - `category` (optional string): Filter by product category.
  - `storeName` (optional string): Filter by store chain (e.g., 'Woolworths').
- **Output**: Array of products currently having `is_on_promotion = true`.

### `BuildBasket`
Calculates and compares the total cost of a shopping list across multiple stores.
- **Input Parameters**:
  - `items` (array of objects): `[{ "productId": "UUID", "quantity": number }]`
- **Output**: Comparison totals:
  - Total cost if bought entirely at Woolworths.
  - Total cost if bought entirely at Pak'nSave.
  - Total cost if bought entirely at New World.
  - The optimized "split basket" option (buying each item at the cheapest store) and the total potential savings.

### `UpdatePreferences`
Saves user preferences (dietary restrictions, preferred stores, budget limits) to their profile.
- **Input Parameters**:
  - `dietaryRestrictions` (optional array of strings): e.g. `["vegan", "gluten-free"]`.
  - `preferredStores` (optional array of strings: UUIDs): List of preferred store IDs.
  - `budgetLimit` (optional number): Budget cap.
- **Output**: Success status confirming update in the `users` table.