# Database Schema

To support our simplified Single-Agent architecture on Supabase (PostgreSQL), we consolidate the database into **7 core tables**. Below are the SQL DDL statements for this schema, which can be executed directly in the Supabase SQL Editor.

---

### 1. `users`
Stores user profile information, dietary preferences, and budget restrictions.
```sql
CREATE TABLE users (
    id UUID REFERENCES auth.users PRIMARY KEY, -- Linked to Supabase Auth
    email TEXT UNIQUE NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL,
    dietary_restrictions TEXT[], -- e.g., ['gluten-free', 'vegan']
    preferred_stores UUID[], -- List of store IDs preferred by the user
    budget_limit NUMERIC(10, 2)
);
```

### 2. `stores`
Stores supermarket chains and their specific branches.
```sql
CREATE TABLE stores (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name TEXT NOT NULL, -- e.g., 'Woolworths', 'Pak''nSave', 'New World'
    branch_name TEXT, -- e.g., 'Grey Lynn', 'Albany'
    region TEXT NOT NULL, -- e.g., 'Auckland', 'Wellington'
    address TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);
```

### 3. `products`
The main catalog of grocery products.
```sql
CREATE TABLE products (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name TEXT NOT NULL,
    brand TEXT,
    size TEXT, -- e.g., '1L', '500g'
    category TEXT, -- e.g., 'Pantry', 'Dairy', 'Produce'
    barcode TEXT UNIQUE, -- Barcode/EAN for global matching across stores
    image_url TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);
```

### 4. `store_products`
Maps products to stores with current prices, stocks, and active promotions.
```sql
CREATE TABLE store_products (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    store_id UUID REFERENCES stores(id) ON DELETE CASCADE,
    product_id UUID REFERENCES products(id) ON DELETE CASCADE,
    price NUMERIC(10, 2) NOT NULL, -- Current price
    original_price NUMERIC(10, 2), -- Price before promotion
    is_on_promotion BOOLEAN DEFAULT FALSE,
    promotion_description TEXT, -- e.g., '2 for $5', 'Save $1.20'
    in_stock BOOLEAN DEFAULT TRUE,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL,
    UNIQUE(store_id, product_id)
);
```

### 5. `price_history`
Tracks pricing trends over time to support historical analysis and deal recommendations.
```sql
CREATE TABLE price_history (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    store_id UUID REFERENCES stores(id) ON DELETE CASCADE,
    product_id UUID REFERENCES products(id) ON DELETE CASCADE,
    price NUMERIC(10, 2) NOT NULL,
    recorded_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);
```

### 6. `shopping_lists`
Stores user-created shopping baskets.
```sql
CREATE TABLE shopping_lists (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    name TEXT NOT NULL DEFAULT 'Weekly Groceries',
    items JSONB NOT NULL DEFAULT '[]'::jsonb, -- Array of items: [{ "product_id": "uuid", "quantity": 1 }]
    created_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);
```

### 7. `conversations`
Maintains conversational history for the chatbot and updates user context memory.
```sql
CREATE TABLE conversations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    messages JSONB NOT NULL DEFAULT '[]'::jsonb, -- Array of messages: [{ "role": "user"|"assistant", "content": "text", "timestamp": "ISO-string" }]
    summary TEXT, -- Key user preferences dynamically summarized by the LLM (e.g. "prefers organic Woolworths products")
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);
```