# Deployment documentation

This repository is deployed as a static GitHub Pages site.

## Live site

```text
https://simoncgoldstein.github.io/us-demographic-dashboard/
```

## Expected GitHub Pages configuration

Repository:

```text
simoncgoldstein/us-demographic-dashboard
```

Pages settings:

```text
Source: Deploy from a branch
Branch: main
Folder: /root
```

The site entrypoint is:

```text
index.html
```

## Deployment timing

For this repository, GitHub Pages should usually redeploy within a few minutes after a commit to `main`.

If a change is not visible:

1. Wait one to three minutes.
2. Hard refresh the browser.
3. Check GitHub Pages settings.
4. Check the repository Actions/Pages deployment status if available.

## Local testing

Because `index.html` loads JSON and a preserved payload, serving locally is better than opening the file directly.

From the repository root:

```bash
python3 -m http.server 8000
```

Open:

```text
http://localhost:8000/
```

## Files required for production

The live site needs at least:

```text
index.html
data/overlays.json
```

The live `index.html` also depends on the preserved payload in the repository's `dashboard-data-source` branch.

## Safe deployment workflow

Recommended workflow for larger changes:

1. Create or edit the file locally.
2. Test locally with `python3 -m http.server 8000`.
3. Verify these interactions:
   - Page loads.
   - Line chart renders.
   - Stacked area renders.
   - Overlay metric renders as a dotted line.
   - Plus/minus zoom works.
   - Drag-to-pan works after zoom.
   - Mobile vertical scrolling is not blocked.
   - Sources tab opens.
   - CSV export works.
4. Commit to `main`.
5. Wait for GitHub Pages to redeploy.
6. Hard refresh the live page.

## Emergency rollback

If the live page breaks, restore a known-good `index.html` commit or replace `index.html` with a stable loader that points back to the preserved dashboard payload.

The most common breakpoints are:

- JavaScript syntax errors in the single-file `index.html`.
- JSON formatting errors in `data/overlays.json`.
- Changes to the preserved payload branch format.
- Mobile pointer-event behavior that blocks vertical scrolling.

## Static-site constraints

This repository should remain a static site unless there is a strong reason to add a build system.

Avoid introducing:

- Node/package manager dependencies.
- Runtime calls to external APIs for normal page load.
- Framework-specific build steps.
- Secrets or API keys.

## No secrets

This project should not contain secrets. Do not commit:

- API keys.
- Tokens.
- Personal credentials.
- Private data exports.

Overlay data should be source-derived public data or manually curated public anchor points.