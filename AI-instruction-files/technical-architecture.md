# Technical Architecture

The platform uses a clean, cost-free, and simple architecture that minimizes setup complexity and runs entirely on free-tier services.

## Technology Stack

- **Framework**: [Next.js](https://nextjs.org/) (App Router, React) with Server Actions/API Routes. Next.js serves as a unified Frontend and Backend, eliminating the need for a separate API server.
- **Styling**: Tailwind CSS + [daisyUI](https://daisyui.com/) for pre-built, premium-looking UI components.
- **Database**: [Supabase](https://supabase.com/) (Free Tier PostgreSQL) for user authentication, conversations, and shopping list storage. (No background scraper tables are needed).
- **AI / LLM**: [Gemini API](https://ai.google.dev/) (Gemini 1.5 Flash via Google AI Studio) for reasoning, intent parsing, tool orchestration, and response generation. It has a generous free tier and requires no local GPU setup.
- **Price Discovery Engine**:
  - **Woolworths (Live)**: Fetches real-time prices using direct, unauthenticated HTTP requests to the Woolworths search API from the Next.js API Routes.
  - **Pak'nSave & New World (Simulated)**: A server utility function that matches Woolworths products and generates realistic mock prices (e.g., Pak'nSave is simulated at a random discount of 5-15% relative to Woolworths, and New World is simulated at ±5%). This eliminates the need for complex scraper maintenance and Cloudflare-bypass setups.