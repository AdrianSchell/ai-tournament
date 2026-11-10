# AI Tournament — complete website and Cloudflare Worker (v8.1)

This package combines the latest v8 student/instructor frontend and feedback backend with the original Georgia Tech image assets and a seamless Tech Tower hero fade.

## Update existing deployment

For the visual change only, replace **style.css** in the root of your GitHub ai-tournament repository and commit. Cloudflare does not need updating.

For a full reinstall, upload root index.html, app.js, style.css, config.js and assets/ to GitHub Pages. Do **not** upload secrets, team codes or answer keys. The worker/src/index.js backend is deployed separately to your existing Cloudflare Worker.

The included worker/wrangler.toml is a **template**. Before any Wrangler deployment, replace its KV ID with the ID of your existing namespace, and verify the deadline and Worker name. Do not create a new KV namespace if preserving submissions. Cloudflare secrets remain managed in Cloudflare.

The config.js file contains the public Worker address previously provided. Confirm it still matches your Cloudflare dashboard.

## Verification

Refresh GitHub Pages with Ctrl+Shift+R and check the hero on desktop and mobile. Verify student submission, instructor dashboard, dataset releases and private feedback before student use.


## v8.2 hero image update
The homepage now uses `assets/tech-tower-seamless.png`, extracted from the approved design reference and faded into transparency on the right and bottom. Both `style.css` AND the new PNG must be uploaded to GitHub. The rest of the application is unchanged. Cloudflare does not need redeployment for this visual change.
