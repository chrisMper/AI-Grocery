# Database Schema

Since the platform performs live API queries and price simulations in memory, **we do not store product catalogs, store branches, or price feeds in the database.**

The database is simplified to **3 core tables** for managing user state, lists, and chat context. Below are the SQL DDL statements that can be executed directly in the Supabase SQL Editor.

---

### 1. `users`
Stores user profile information, dietary preferences, and budget restrictions.
```sql
CREATE TABLE users (
    id UUID REFERENCES auth.users PRIMARY KEY, -- Linked to Supabase Auth
    email TEXT UNIQUE NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL,
    dietary_restrictions TEXT[], -- e.g., ['gluten-free', 'vegan']
    preferred_stores TEXT[], -- e.g., ['Woolworths Grey Lynn', 'Pak\'nSave Albany']
    budget_limit NUMERIC(10, 2)
);
```

### 2. `shopping_lists`
Stores user-created grocery lists.
```sql
CREATE TABLE shopping_lists (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    name TEXT NOT NULL DEFAULT 'Weekly Groceries',
    -- Stores list of items: [{ "barcode": "12345", "name": "Milk", "quantity": 1, "brand": "Woolworths", "size": "2L" }]
    items JSONB NOT NULL DEFAULT '[]'::jsonb, 
    created_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);
```

### 3. `conversations`
Maintains conversational history for the chatbot and updates user context memory.
```sql
CREATE TABLE conversations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    messages JSONB NOT NULL DEFAULT '[]'::jsonb, -- Array of messages: [{ "role": "user"|"assistant", "content": "text", "timestamp": "ISO-string" }]
    summary TEXT, -- Key user preferences dynamically summarized by the LLM (e.g., "prefers organic Woolworths products")
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);
```