# Technical Architecture

The platform uses a clean, cost-free, and simple architecture that minimizes local setup complexity and runs entirely on free-tier services.

## Technology Stack

- **Framework**: [Next.js](https://nextjs.org/) (App Router, React) with Server Actions/API Routes. This serves as a unified Frontend and Backend, eliminating the need for a separate API server.
- **Styling**: Tailwind CSS + [daisyUI](https://daisyui.com/) for pre-built, premium-looking UI components.
- **Database**: [Supabase](https://supabase.com/) (Free Tier PostgreSQL) for hosting relational data, user authentication, and storing scraper results.
- **AI / LLM**: [Gemini API](https://ai.google.dev/) (Gemini 1.5 Flash via Google AI Studio) for reasoning, tool orchestration, and response generation. It has a generous free-tier rate limit and requires no local GPU or setup.
- **Scraper / Background Worker**: A lightweight script using Playwright running on a schedule (e.g., GitHub Actions cron job or a local cron job) to fetch prices and update the Supabase database.