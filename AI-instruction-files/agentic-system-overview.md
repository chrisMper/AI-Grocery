# Agentic System Overview

To keep the platform simple, fast, and cost-effective, we use a **Single Coordinator Agent** pattern. Instead of routing requests through multiple sequential LLM calls (which increases latency and token consumption), a single agent handles user interaction and calls specific backend tools.

---

## Coordinator Agent Responsibilities

1. **User Request Understanding**: Understands natural language queries, detects intent (e.g., shopping list creation, price comparison, budget optimization), and asks clarifying questions if details are missing.
2. **Execution Planning & Tool Calling**: Dynamically decides which backend tools to run (e.g., search database, match products, retrieve prices, build optimized baskets) based on the query.
3. **Savings Recommendation**: Analyzes search and comparison results to suggest the best store options, cheaper substitute products, or deal combinations.
4. **Final Recommendation**: Formulates a clear, human-readable response featuring direct store recommendations, basket breakdowns, and cost summaries.

---

## Agentic Workflow Example

**User Request**: *"I need ingredients for spaghetti bolognese for a family of four under $150."*

1. **Receive & Parse**: The agent parses the request and identifies the goal (meal plan ingredients + budget threshold).
2. **Tool Call - Recipe/Items Discovery**: The agent calls a tool (or uses its internal knowledge) to identify the core ingredients needed for spaghetti bolognese.
3. **Tool Call - Search & Match Products**: The agent invokes product search tools on the Supabase database to find matching grocery items.
4. **Tool Call - Compare Supermarkets**: The agent requests price comparisons across different supermarket chains for these items.
5. **Tool Call - Optimize Basket**: The agent optimizes the basket to stay under $150, prioritizing store-specific discounts and cheaper alternative brands.
6. **Respond**: The agent formats a clear response detailing the items, store recommendations, and final cost breakdowns.

---

## Key Benefits of Single-Agent Architecture

- **Low Latency**: Performs the entire loop in one or two LLM roundtrips, reducing wait time for the user to a few seconds.
- **Cost Efficiency**: Reduces token usage compared to multi-agent message passing systems.
- **Maintainability**: Centralizes reasoning and instructions in a single, well-defined system prompt.
- **Reliable Output**: Simplifies the state model, avoiding complex distributed state management across agents.