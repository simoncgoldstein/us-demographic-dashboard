# Data documentation

This document describes the data structure, source strategy, and update process for the U.S. Demographic Dashboard.

## Data layers

The dashboard uses two separate data layers.

### 1. Demographic payload

The demographic payload is loaded by `index.html` from the preserved dashboard payload in the `dashboard-data-source` branch.

It contains:

- National long-run demographic composition.
- 2020-2025 current-estimate views.
- Geography options for the United States, Census regions, states, and D.C.
- Age groups for the 2020-2025 current-estimate view.
- Event markers used by the chart.

This split keeps the live `index.html` smaller while preserving the large Census-derived payload.

### 2. Overlay metrics

Overlay data lives in:

```text
data/overlays.json
```

The main page loads this file at runtime from the same GitHub Pages site. This avoids browser-side calls to FRED, FBI, or other external APIs, which may fail because of CORS, rate limits, API availability, or privacy tools.

## Overlay file format

`data/overlays.json` is a JSON object. Each key maps to an array of `[year, value]` pairs.

Example:

```json
{
  "home": [
    [1987, 64],
    [1990, 76],
    [2000, 100],
    [2025, 327]
  ]
}
```

Current keys:

| Key | Meaning | Notes |
|---|---|---|
| `foreign` | Foreign-born share | National percentage anchor series |
| `wage` | Real weekly earnings | National real earnings anchor series |
| `home` | Case-Shiller home price index | National home-price index anchors |
| `afford` | Home price / wage ratio | Derived inside the dashboard from `home` and `wage` |
| `lfpr` | Prime-age male labor-force participation | National percentage anchor series |
| `crime` | Violent crime rate | National violent crime rate anchors |
| `homicide` | Homicide rate | National homicide rate anchors |

Do not add `afford` directly unless the dashboard logic is changed. It is currently calculated from the `home` and `wage` series.

## Category model

The dashboard uses simplified category names for display:

- White
- Black
- Hispanic
- Asian
- Other

The meaning of these categories changes by source period.

### Long-run national view

The long-run view bridges historical Census race categories and modern race/Hispanic-origin categories. This is acceptable for a broad visualization, but it is not a perfectly consistent measurement series.

Important caveats:

- Hispanic origin is not consistently available across the full historical series.
- Historical White is not identical to modern non-Hispanic White.
- The Hispanic line should begin only when reliable source support exists.
- Other is a residual category and can include different groups depending on the period.

### Current-estimate views

The 2020-2025 current-estimate views are more consistent for modern analysis. They support:

- United States
- Census regions
- States and D.C.
- Age groups

These views should not be visually extended backward to 1790.

## Overlay interpretation

Overlay metrics are national contextual series. They are not state-specific and not age-specific in the current implementation.

When a user selects a state or age group, the demographic data updates to that state or age group, but the overlay remains a national context line.

The right-side overlay axis is intentionally separate from the left-side demographic axis. This prevents units such as percent share, home-price index, earnings, and crime rates from being mixed on one scale.

## Recommended overlay display

The default should remain:

```text
Index to first common year
```

This makes series with different units visually comparable without pretending they are measured in the same units.

Other supported displays:

- Raw value
- Percent change

## Adding or updating overlay data

1. Update `data/overlays.json`.
2. Keep the format as an array of `[year, value]` pairs.
3. Use numeric years and numeric values only.
4. Keep data ordered by year when possible.
5. Update the Sources tab in `index.html` if the source or interpretation changes.
6. Update this file if adding a new overlay key.
7. Test the dashboard in line mode with the overlay selected.

## Adding a new overlay metric

To add a new metric:

1. Add a new key to `data/overlays.json`.
2. Add a matching `<option>` in the `Overlay metric` dropdown in `index.html`.
3. Add source notes to the Sources tab.
4. Add a row to the overlay key table above.
5. Verify that the dotted overlay line renders on the right-side scale.

## Quality rules

- Do not fetch external APIs from the browser for normal page operation.
- Do not imply causation from visual correlation.
- Keep the main chart as a time-series view.
- Use a separate analysis module if correlation or regression is reintroduced.
- Keep state/age demographic filters conceptually separate from national overlays.
- Prefer official or durable source-derived anchors over fragile live API calls.