# U.S. Demographic Dashboard

Interactive static dashboard for exploring U.S. demographic composition from the founding era through the present, with optional contextual overlays such as home prices, real weekly earnings, foreign-born share, crime, homicide, and prime-age male labor-force participation.

Live site:

`https://simoncgoldstein.github.io/us-demographic-dashboard/`

## What it does

- Shows national long-run demographic trends from 1790 through 2025.
- Supports current-estimate views by geography and age group for 2020-2025.
- Uses solid demographic lines on the left axis.
- Uses an optional dotted cyan overlay line on a right-side scale.
- Supports line and stacked-area chart modes.
- Supports percent-share and count views where available.
- Includes plus, minus, left, right, and reset zoom controls directly on the chart.
- Allows horizontal drag-to-pan once zoomed.
- Keeps mobile vertical scrolling usable while permitting horizontal panning after zoom.
- Includes a Sources tab and a selected-data table.

## Repository structure

```text
.
├── index.html              # Main GitHub Pages dashboard
├── data/
│   └── overlays.json       # Versioned overlay metric anchors
└── docs/
    ├── DATA.md             # Data model, source notes, and update guidance
    └── DEPLOYMENT.md       # GitHub Pages deployment notes
```

The dashboard is intentionally static. There is no build step, package manager, server, or external runtime dependency required for the core page.

## Data architecture

The dashboard has two data layers:

1. **Demographic payload**: loaded from the preserved dashboard payload in the repository's `dashboard-data-source` branch. This keeps the live `index.html` lighter while preserving the national long-run and 2020-2025 state/age data.
2. **Overlay metrics**: loaded from `data/overlays.json` on the main branch. These are versioned anchor series so the page does not depend on browser-side requests to FRED, FBI, or other external APIs at runtime.

The current overlay metrics are:

- `foreign`: foreign-born share
- `wage`: real weekly earnings
- `home`: Case-Shiller home price index
- `afford`: derived home-price / wage ratio
- `lfpr`: prime-age male labor-force participation rate
- `crime`: violent crime rate
- `homicide`: homicide rate

## Design principle

The primary display is a time-series comparison. Demographic series are plotted as solid lines. Contextual indicators are plotted as dotted overlay lines with their own right-side scale.

These overlays are meant to show timing, inflection points, and broad directional movement. They should not be read as proof of causation. For more rigorous relationship testing, add a separate correlation-analysis view rather than overloading the main chart.

## Local use

Open `index.html` directly in a browser or serve the directory locally:

```bash
python3 -m http.server 8000
```

Then open:

```text
http://localhost:8000/
```

Serving locally is preferable because `index.html` fetches `data/overlays.json` and the preserved demographic payload.

## Deployment

This repository is designed for GitHub Pages. See [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md).

## Data notes

See [docs/DATA.md](docs/DATA.md) for source notes, category caveats, and overlay update guidance.

## Known caveats

- Long-run historical race categories are not perfectly comparable with modern race/Hispanic-origin categories.
- Hispanic origin is not available as a clean national series from 1790 onward, so the Hispanic line starts only once source support exists.
- State and age filters use 2020-2025 estimates, not the full 1790-2025 historical series.
- Overlays are national contextual series, even when a state or age demographic view is selected.
- `afford` is a derived index, not an official standalone source series.

## Maintenance checklist

When changing the dashboard:

1. Keep `index.html` static and dependency-free.
2. Keep overlay metric data in `data/overlays.json` rather than fetching external APIs in the browser.
3. Preserve the distinction between national long-run data and 2020-2025 state/age estimates.
4. Update the Sources tab and `docs/DATA.md` whenever adding or revising metrics.
5. Test mobile scroll, zoom, and drag behavior before publishing.