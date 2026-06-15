# GroceryAgent Development Plan

This plan details the step-by-step process to initialize, develop, and verify the GroceryAgent application in the workspace root ([d:\AI-Grocery](file:///d:/AI-Grocery)).

---

## Proposed Phases of Development

### Phase 1: Bootstrapping & Project Setup
We will initialize the codebase using the Next.js framework in the current workspace directory.
1. Run `npx create-next-app --help` to verify non-interactive command-line options.
2. Initialize Next.js with the following options: TypeScript, Tailwind CSS, ESLint, `src/` directory, App Router, and custom path aliases.
3. Install the `daisyui` UI component library to provide premium theme selections and component styling.
4. Set up the initial routing structure and CSS theme tokens.

### Phase 2: Price Discovery & API Layer
We will implement the backend utility functions for real-time supermarket data and route handlers.
1. Implement the **Woolworths API search client** to query live data.
2. Implement the **Foodstuffs simulation engine** to generate realistic mock prices for Pak'nSave and New World.
3. Create Next.js API Routes:
   - `POST /api/chat`: Communicates with the AI coordinator.
   - `POST /api/basket/optimize`: Aggregates products and compares total prices across stores.
   - `GET /api/deals`: Serves live/simulated discounts.
   - `GET /api/dashboard/summary`: Serves metrics and saving statistics.

### Phase 3: AI Coordinator Agent (Gemini API)
We will build the intelligent agent logic to handle user conversations.
1. Install the official Google Generative AI SDK (`@google/genai` or `@google/generative-ai`).
2. Implement tool-calling functions (`SearchProducts`, `BuildBasket`) so the LLM can search prices and calculate totals on-demand.
3. Configure the system prompt with user memory context and grocery intelligence heuristics.

### Phase 4: UI Design & User Experience
We will build a high-fidelity web interface using daisyUI components.
1. **Home Screen**: A search bar, example prompts, and quick links.
2. **Chat Screen**: A ChatGPT-style premium chat flow with smooth message streams and formatted product results cards.
3. **Basket View**: Displays items, quantities, price comparisons, and a visual breakdown of the "split basket" savings.
4. **Dashboard**: Charts showing cumulative savings and favorite products.

---

## User Review Required

> [!IMPORTANT]
> To ensure the bootstrapping command runs smoothly in non-interactive mode, we will run `npx create-next-app@latest` targeting the current folder (`./`).
> Since the workspace already contains some files (like `README.md`, `why-realtime-scraping-fails.md`, and the `AI-instruction-files` directory), Next.js might warn us that the directory is not empty. We will handle this gracefully.

---

## Verification Plan

### Automated Verification
- Verify the typescript builds successfully (`npm run build`).
- Verify the application launches locally (`npm run dev`).

### Manual Verification
- Deploy and open the application in a browser to test the chat interface and price comparisons.
