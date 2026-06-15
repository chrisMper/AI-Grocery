# Why Real-Time Web Scraping Fails

When building a grocery price comparison application, it is tempting to scrape supermarket websites (like Woolworths, Pak'nSave, and New World) in real-time when a user asks a question. However, this approach introduces severe engineering challenges that usually cause the project to fail.

Here is a detailed breakdown of these challenges and why a simplified architecture (direct API queries + price simulation) was chosen instead.

---

## 1. Severe Latency & Poor UX

Real-time web scraping with tools like Playwright or Puppeteer is extremely slow:
1. **Browser Startup**: Launching a headless browser instance takes 1–3 seconds.
2. **Page Navigation**: Loading three different supermarket websites sequentially takes 5–15 seconds depending on network speeds and page sizes.
3. **Interacting & Extracting**: Typing into search inputs, waiting for JavaScript to render, and parsing the DOM takes another 3–5 seconds.

> [!WARNING]
> A single user chat message requesting a price comparison would take **20 to 30+ seconds** to respond. This is unacceptable for a modern chat interface.

---

## 2. Cloudflare & Anti-Bot Protections

New Zealand supermarkets (Woolworths and Foodstuffs) handle massive traffic and employ strict web application firewalls (WAF) like **Cloudflare**:
- Headless browsers running from cloud servers (like Vercel, Supabase, or AWS) are instantly flagged as bots.
- The website will serve a **CAPTCHA** challenge (like Cloudflare Turnstile).
- Since Playwright is automated, it cannot bypass these CAPTCHAs, resulting in scrape errors, blank pages, or IP bans.

---

## 3. Serverless Execution & Hosting Limits

To keep this project **100% free and simple**, we target hosting platforms like Vercel (Free tier):
- **Timeout Limits**: Vercel Serverless Functions on the free tier have a strict maximum execution time of **10 seconds**. An on-demand scraper will consistently hit this timeout and crash.
- **Resource Constraints**: Running Playwright requires downloading a heavy Chromium binary (~150MB) and spinning up a browser process, which exceeds the memory allocations of free-tier serverless environments.

---

## 4. Maintenance Overhead (Fragile Selectors)

Supermarkets frequently update their website layouts.
- If a supermarket changes a CSS class name, a class selector, or structural HTML element, the scraper's CSS selector breaks.
- This requires constant code updates and monitoring to ensure the app doesn't break unexpectedly.

---

## The Solution: Option B (Direct API + Price Simulation)

To resolve all of these issues while maintaining a 100% free and zero-maintenance profile:
- **Woolworths (Live)**: We fetch prices via direct, lightweight HTTP requests to Woolworths' public, unauthenticated search API. This takes less than 1 second and is highly reliable.
- **Foodstuffs (Simulated)**: We simulate Pak'nSave and New World prices in-memory using statistical models (e.g., Pak'nSave is 5–15% cheaper). This gives realistic comparison data with zero latency, zero scraper code, and zero hosting limitations.
