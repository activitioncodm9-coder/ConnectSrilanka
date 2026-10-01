# Connect Srilanka

A lightweight, zero-build starter for building fast static experiences with vanilla HTML, CSS, and JavaScript, served by a Hono backend on Cloudflare Workers.

[cloudflarebutton]

## Description

This project is a minimal boilerplate for shipping a pure frontend site on Cloudflare's global network. The UI lives in `public/` as static HTML, CSS, and JS with no framework or build step, while `worker/index.js` uses [Hono](https://hono.dev/) to serve static assets at the edge.

Ideal for landing pages, prototypes, demos, and learning Cloudflare Workers and Hono.

## Key Features

- **Zero Framework Frontend** — Pure HTML + CSS + JS, no bundler required
- **Edge Serving** — Static files served via Hono + Cloudflare Workers
- **Instant HMR-Style Dev** — Edit `public/` and see changes on save with the local dev script
- **Dark Modern UI** — Responsive gradient layout with glassmorphism card out of the box
- **Single-Page Fallback** — SPA routing handled via Wrangler assets config
- **Production Ready** — Deploy globally in seconds with Wrangler

## Technology Stack

- **Frontend:** HTML5, CSS3, Vanilla JavaScript (ES Modules)
- **Backend:** [Hono v4](https://hono.dev/) (`hono`, `hono/cloudflare-workers`)
- **Runtime / Hosting:** Cloudflare Workers + Workers Assets
- **Tooling:**
  - `wrangler` v4 — build, dev, and deploy
  - `bun` — package management and dev scripts
  - `typescript` v5, `@cloudflare/workers-types` v4 — type support
  - `eslint`, `typescript-eslint`, `globals` — linting

## Project Structure

```text
├── public/
│   ├── index.html      # App entry, UI markup
│   ├── styles.css      # Global styles and theme
│   └── app.js          # Client-side interactivity (counter example)
├── worker/
│   └── index.js        # Hono app, serves static assets
├── scripts/
│   └── dev.ts          # Local development server
├── wrangler.jsonc      # Workers + assets configuration
├── package.json        # Dependencies and scripts
└── README.md
```

## Prerequisites

- [Bun](https://bun.sh/) >= 1.0
- [Node.js](https://nodejs.org/) >= 18
- A [Cloudflare account](https://dash.cloudflare.com/sign-up) for deployment

## Setup / Installation

1. Clone the repository:

   ```bash
   git clone <your-repo-url>
   cd <your-repo-name>
   ```

2. Install dependencies with Bun:

   ```bash
   bun install
   ```

3. Run the bootstrap if needed (runs automatically on `bun install`):

   ```bash
   bun .bootstrap.js
   ```

## Development

Start the local development server:

```bash
bun run dev
```

This runs `bun scripts/dev.ts`, which watches `public/` and `worker/` for changes. Edit files to iterate:

- `public/index.html` — update markup. The example includes a `#counter` button and `#count` span.
- `public/styles.css` — adjust theme, layout, and components.
- `public/app.js` — add client logic. Example:

  ```js
  let count = 0;
  const button = document.getElementById('counter');
  const countSpan = document.getElementById('count');

  button.addEventListener('click', () => {
    count++;
    countSpan.textContent = count;
  });
  ```

- `worker/index.js` — extend the API. Current worker serves static assets:

  ```js
  import { Hono } from 'hono'
  import { serveStatic } from 'hono/cloudflare-workers'

  const app = new Hono()

  // Serve static files from public/
  app.use('/*', serveStatic({ root: './public' }))

  export default app
  ```

### Useful Scripts

```bash
bun run dev     # Start local dev server
bun run build   # Build with Wrangler (typecheck / bundle validation)
bun run lint    # Run ESLint
bun run deploy  # Deploy to Cloudflare Workers
```

### Linting

```bash
bun run lint
```

## Usage Examples

### Adding a New Page Section

Edit `public/index.html`:

```html
<div class="card">
  <h2>Hello from the edge</h2>
  <p class="subtitle">Deployed globally with Cloudflare Workers.</p>
</div>
```

### Adding an API Route

Edit `worker/index.js` before the static handler:

```js
app.get('/api/hello', (c) => {
  return c.json({ message: 'Hello from Hono on Workers!' })
})
```

Then fetch it from `public/app.js`:

```js
fetch('/api/hello')
  .then((res) => res.json())
  .then((data) => console.log(data.message));
```

### Adding Styles

All global styles live in `public/styles.css`. Add utility classes or component styles and link additional stylesheets in `index.html` as needed — no build step required.

## Configuration

`wrangler.jsonc` controls the Worker and asset serving:

```jsonc
{
  "name": "connect-wave-1vhglpgbq79kj6tl-vqno",
  "main": "worker/index.js",
  "compatibility_date": "2025-04-24",
  "assets": {
    "not_found_handling": "single-page-application",
    "directory": "./public"
  }
}
```

- `main`: Hono entry point
- `assets.directory`: Static directory served at the edge
- `not_found_handling: single-page-application`: Falls back to `index.html` for SPA routing

## Deployment

### Deploy to Cloudflare Workers

[cloudflarebutton]

1. Login to Cloudflare:

   ```bash
   bunx wrangler login
   ```

2. Deploy:

   ```bash
   bun run deploy
   ```

   This runs `wrangler deploy` and publishes `worker/index.js` with the contents of `./public` as Workers Assets.

3. Your app will be live at:

   ```text
   https://<your-worker-name>.<your-subdomain>.workers.dev
   ```

### Custom Domain

1. Go to Cloudflare Dashboard > Workers & Pages > your Worker > Settings > Domains & Routes
2. Click Add > Custom Domain and follow the prompts

### Environment Management

For preview vs production, use Wrangler environments in `wrangler.jsonc` or pass `--env` flag:

```bash
bunx wrangler deploy --env staging
bunx wrangler deploy --env production
```

## How It Works

1. Request hits Cloudflare edge
2. `worker/index.js` (Hono) intercepts via `serveStatic`
3. Static file from `public/` is returned with edge caching
4. If no file matches, falls back to `index.html` per `not_found_handling`
5. Client `app.js` hydrates interactivity in the browser

## Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feat/my-feature`
3. Make changes in `public/` or `worker/`
4. Run lint: `bun run lint`
5. Test locally: `bun run dev`
6. Open a Pull Request

## License

This project is open source. Add your preferred license (e.g., MIT) in a `LICENSE` file.

---

Powered by Vanilla JavaScript + Hono + Cloudflare Workers.
