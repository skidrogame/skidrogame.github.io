# WP → GitHub Pages Static Mirror

This repo is a **ready-to-use skeleton** to publish a static mirror of your WordPress site to **GitHub Pages**.

> ⚠️ By default, this setup publishes with `robots.txt` blocking crawlers (`Disallow: /`) to prevent SEO duplicate content. If you **want** the mirror indexed, change that file and remove the meta noindex injection step.

---

## Quick options

### Option A — Manual export on your PC (simplest)
1. Install `wget` (Linux/macOS) or use WSL on Windows.
2. Run:
   ```bash
   export SRC_URL="https://www.reloadedskidrow.com/"
   ./scripts/mirror-wget.sh "$SRC_URL" "./public"
   ```
3. Review the output in `./public` (HTML, images, CSS/JS).
4. Copy the contents of `./public` to repo root and commit:
   ```bash
   rsync -a --delete ./public/ ./
   touch .nojekyll
   git add -A && git commit -m "Manual mirror" && git push
   ```
5. In GitHub → Settings → Pages, set **Source** to **Deploy from a branch** → **main** / **root**.

### Option B — Auto mirror via GitHub Actions (no server needed)
1. Commit & push this repo to GitHub.
2. In GitHub → Settings → Pages: set **Source** to **gh-pages** / **root** (we deploy there).
3. Edit `.github/workflows/mirror.yml` and set:
   ```yaml
   env:
     SRC_URL: "https://www.reloadedskidrow.com/"
     CANONICAL_ORIGIN: "https://www.reloadedskidrow.com"
   ```
4. The workflow:
   - Crawls `SRC_URL` with `wget`
   - Converts links for static usage
   - Injects `<meta name="robots" content="noindex,nofollow">` and a `<link rel="canonical">`
   - Publishes to `gh-pages` automatically (schedule daily + on demand).

> If you really want the mirror **indexable**, remove the noindex injection step and set a proper `robots.txt`. For a safe mirror, leave noindex on.

---

## Notes & gotchas

- Dynamic features (comments, search, admin, login) won't work.
- Limit what you mirror to avoid huge repos (uploads can be massive).
- Use `--exclude-directories` to skip `/wp-admin`, `/wp-login.php`, etc.
- If Cloudflare blocks the crawler, consider whitelisting your GitHub Actions IP ranges or mirror from your own server and push from there.
- Ads/trackers: the static copy will include the script tags if present at crawl-time.
- **Canonical** is injected so SEO signals stay with your primary site.

---

## Unblock indexing (optional)
- Change `robots.txt` to allow, and remove the meta noindex injection step in the workflow.
- Keep `<link rel="canonical">` pointing to your main domain if you don't want duplicate content penalties.

---

## Custom domain (optional)
- Put your domain in the `CNAME` file (single line), then set a CNAME DNS record to `<username>.github.io`.
- In GitHub → Settings → Pages → Custom domain, enter the same domain.

