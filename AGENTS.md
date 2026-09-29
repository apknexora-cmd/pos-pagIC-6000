# NEXORA static site

Single-file static site: the whole app is `index.html` (Persian RTL landing page with a client-side Gemini chatbot, services catalog, packages, cost estimator, and cart). No build step, no backend, no database.

## Running locally
- `docker compose -f docker-compose.base44.yml up -d` — nginx serves `index.html` on port 3000.
- To run without Docker: open `index.html` directly in a browser, or serve the directory with any static server.

## Gemini chatbot / API key
- The chatbot calls the Gemini API **from the browser** (`callGeminiAPI` in `index.html`). There is no server-side key handling.
- A **shared key for all visitors** is injected at container startup: `docker-compose.base44.yml` reads the platform secret file (`/run/base44/app.env`, mounted read-only) and writes `/usr/share/nginx/html/app-config.js` containing `window.NEXORA_API_KEY`. `index.html` loads that script and `getApiKey()` falls back to it — so no setup modal opens for visitors. The key is never committed to the repo.
- Visitors may still enter their own key via the setup modal (stored in `localStorage` `nexora_api_key`); it takes precedence over the shared key.
- Model name and endpoint are the `GEMINI_MODEL` / `GEMINI_API_URL` constants in `index.html`. If the chatbot returns "model not found", update `GEMINI_MODEL` to a currently available Gemini model.

## Editing
- All content lives in `index.html`: `servicesData`, `packagesData`, `SYSTEM_PROMPT`, and contact info are plain JS arrays/strings near the bottom of the file.
- The page depends on CDNs (Tailwind, Vazirmatn font, Font Awesome, marked.js) — needs internet access to render fully.

## Verify it works
- `curl -s http://localhost:3000/ | grep NEXORA` should return the page title.
- Open the preview: hero, service cards, packages, and the estimator should render; the chatbot activates after entering a Gemini key in the modal.
