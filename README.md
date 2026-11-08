# AI Tournament — Georgia Tech-style interface

A GitHub Pages frontend and Cloudflare Worker backend for two rounds of clothing classification competition.

## Features

- Georgia Tech-inspired navy/gold homepage, datasets, student submission wizard, public leaderboard, and private instructor dashboard.
- Round 1: 100 predictions. Round 2: 200 predictions.
- Independent answer keys, submissions, scoring, confusion matrices, and published leaderboards per round.
- Round 2 training plan: 700 Fashion-MNIST + 80 Georgia Tech originals + 320 augmented variants = 1,100 images.
- Instructor-only answer-key uploads; separate team codes; CSV exports; deadline enforcement.

## Deploy

1. Put `index.html`, `style.css`, `app.js`, `config.js` at the root of a **public GitHub repository** and enable GitHub Pages (Settings → Pages → Deploy from main/root).
2. Install Node.js on your computer. Open a terminal in `worker/`. Run `npm install -g wrangler` and `wrangler login`.
3. Run `wrangler kv namespace create TOURNAMENT_KV`. Copy the returned ID into `worker/wrangler.toml`.
4. Edit `ALLOWED_ORIGIN` in `wrangler.toml` to match your GitHub Pages **origin** (e.g. `https://USERNAME.github.io`, without the repository path).
5. Configure `ROUND1_DEADLINE_UTC` and `ROUND2_DEADLINE_UTC` in `[vars]` as UTC ISO timestamps. If a deadline is omitted, submissions remain open for that round. `DEADLINE_UTC` is a backward-compatible fallback for Round 1.
6. From `worker/`, run `wrangler secret put ADMIN_TOKEN`, then `wrangler secret put TEAM_CODES_JSON`. Example team codes JSON: `{"Team Alpha":"unique-random-code-1","Team Beta":"unique-random-code-2"}`. Never commit these secrets.
7. Run `wrangler deploy`. Copy the Worker URL into `config.js` as `window.TOURNAMENT_API_URL` and commit that update to GitHub.
8. In the instructor dashboard, upload separate answer keys for each round with columns `image_id,true_label`. Use 100 data rows for Round 1, 200 for Round 2.
9. Test with a known CSV before sharing the link. Confirm that unauthorized requests cannot access admin routes, incorrect team codes are rejected, and published leaderboards never contain image-level labels.

## Student CSV

Two columns: `image_id,predicted_label`. All labels are integers 0–9. Round 1 requires 100 rows; Round 2 requires 200 rows. Team name must exactly match a key in `TEAM_CODES_JSON`.

## Security and operations notes

- Never put answer keys or student submissions in the GitHub repository.
- This is a starter implementation. It is **not independently security audited** or live-deployment tested. Review and pilot before graded use.
- The public leaderboard is private until an instructor explicitly publishes it for that round.
- Cloudflare KV is eventually consistent; recent submissions may take time to appear across locations. For high-stakes simultaneous deadlines, consider a transactional database (D1) and stronger identity verification.
- A shared team code authenticates the team, not individual students. Treat it accordingly.
- The interface uses a text-based GT monogram rather than an official trademarked logo or licensed campus photograph. Replace the hero placeholder with an appropriately licensed campus image if desired.
- Team CSVs are validated on the server; browser checks are only for convenience.

## Google Drive dataset release controls

The Datasets page now obtains release status from the Cloudflare Worker. The instructor manages the three Drive URLs and Locked/Released status in **Instructor → Dataset Info**. Sign in with your Cloudflare `ADMIN_TOKEN`, click **Dataset Info**, edit URLs/status, and click **Save dataset release settings**.

The three URLs supplied by the instructor are assigned **in the order provided**: (1) Round 1 competition, (2) Round 2 training and validation, (3) Round 2 competition. Confirm these assignments before release. All three start **Locked**. Links are not sent by the public API while locked. The private admin API returns all three links.

**Critical:** Website locks do not protect files if their Google Drive URLs are known. Keep each Drive file **Restricted** until release; then change its Drive permissions to allow the intended students to download. The app cannot change Google Drive permissions and does not schedule automatic releases. After release, links can be copied and cannot be revoked simply by relocking the app. For true access revocation, change Drive permissions.

After deploying the updated Worker with `wrangler deploy`, deploy the updated frontend files to GitHub Pages. Test `/api/datasets` before and after release and verify that the Drive file is inaccessible to an unauthorized account while restricted. Existing Cloudflare KV storage and `ADMIN_TOKEN` remain unchanged when upgrading the same Worker.

## Visual assets

The homepage uses cropped visual assets from the instructor-provided design reference (`assets/gt-logo-reference.png` and `assets/tech-tower-reference.jpg`). The four statistic icons are embedded SVG line illustrations matching the reference icon shapes. Confirm permission to publish the Georgia Tech marks and building imagery before public deployment. The reference-derived tower crop is low resolution; replace it with an authorized high-resolution photograph if available.
