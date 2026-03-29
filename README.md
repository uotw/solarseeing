# ☀️ Solar Seeing Forecast

**[solarseeing.com](https://solarseeing.com)**

A free, open-source solar seeing forecast tool for spectroheliograph and solar telescope imaging. Analyzes multi-layer atmospheric wind data to predict seeing conditions up to 7 days out.

![Solar Seeing Forecast Screenshot](screenshot.jpg)

## Why This Exists

Standard astronomical seeing forecasts (Clear Sky Chart, Astrospheric, Meteoblue) are designed for nighttime observing and don't reliably predict daytime seeing conditions. Daytime solar seeing is dominated by ground-layer convection — not the upper-atmosphere turbulence that nighttime tools model.

This tool analyzes wind speed and direction at four atmospheric pressure levels, applies literature-calibrated scoring weights, and produces an hourly seeing quality forecast tuned specifically for solar imaging.

## How It Works

### Multi-Layer Wind Analysis

The tool pulls GFS forecast data from the [Open-Meteo API](https://open-meteo.com/) at four pressure levels:

| Layer | Altitude | Weight | Why It Matters |
|-------|----------|--------|----------------|
| 850 hPa | ~4k ft | 40% | Ground layer — dominates daytime seeing. 55-65% of optical turbulence lives below 500m (Big Bear, La Palma studies) |
| 700 hPa | ~9k ft | 25% | Summit-adjacent — strong predictor per Mauna Kea Weather Center ML models |
| 500 hPa | ~17k ft | 15% | Mid-troposphere — weaker individual predictor, falls in turbulence "gap" |
| 250 hPa | ~32k ft | 20% | Jet stream — Vernin (1986) 20 m/s threshold for sub-arcsecond seeing |

### Scoring Model

Each layer's wind speed is scored independently using thresholds derived from observatory research:

- **850 hPa uses a U-shaped curve** — stagnant air (< 2 m/s) is penalized because thermal plumes build without mixing. The sweet spot is 2-5 m/s.
- **250 hPa is anchored on Vernin's 20 m/s threshold** — the most robust finding in the literature for sub-arcsecond free-atmosphere seeing.
- **Vector wind shear penalty** — computed between adjacent layer pairs. Optical turbulence (Cn²) scales with shear squared, so large speed or direction differences between layers are penalized.
- **Cloud cover multiplier** — tuned for solar imaging where you only need a 2-minute clear window. Partly cloudy conditions (30-50%) barely affect the score since gaps are plentiful.

### Score Scale

| Score | Rating | What It Means |
|-------|--------|---------------|
| 80+ | Excellent | Sub-arcsecond conditions possible. High-resolution day. |
| 65-79 | Good | Great for full-disk SHG work. Fine detail visible. |
| 45-64 | Decent | Workable — spectroheliograph seeing resistance helps. |
| 25-44 | Poor | Expect choppy reconstructions. Coarse detail only. |
| < 25 | Bad | Jet stream overhead or heavy overcast. Skip it. |

## Features

- **7-day forecast** with best score per day at a glance
- **Hourly bar chart** for each day — tap any bar for details
- **Multi-layer wind speed chart** — see all four layers plotted across the day with shear overlay
- **Cloud cover integration** — cloud icons on bars, detailed breakdown in panel
- **Wind shear visualization** — dashed red line showing total vector shear
- **Time filters** — All Hours / Daylight / Morning (sunrise-based)
- **Any location** — enter coordinates for anywhere in the world
- **Click-interactive** — bar chart and wind chart both respond to clicks
- **Zero infrastructure** — runs entirely client-side, no backend needed

## Calibration Sources

The scoring model draws from:

- **Vernin (1986)** — foundational 20 m/s jet stream threshold
- **Sarazin & Tokovinin (2002)** — V₀ = max(V_ground, 0.4 × V₂₀₀) empirical formula used at ESO Paranal
- **Cherubini et al. (2022)** — Mauna Kea Weather Center ML seeing forecast, GFS at 650-200 mb
- **Big Bear Solar Observatory** — daytime Cn² profiles showing 55-65% of turbulence below 500m
- **Swedish Solar Telescope, La Palma** — 67% of daytime turbulence in 0-500m layer
- **Baikal Astrophysical Observatory** — daytime optical turbulence and wind speed distributions
- **Cloudy Nights / SolarChat forums** — community experience with jet stream thresholds, site selection, and daytime seeing patterns

## Tech Stack

- Single static HTML file
- React 18 via CDN
- Babel standalone for JSX transpilation
- Font Awesome 6 icons
- Plus Jakarta Sans + JetBrains Mono fonts
- Open-Meteo GFS API (free, no key required)
- Hosted on Cloudflare Pages

## Development

No build tools needed. Edit `index.html` and push:

```bash
git clone https://github.com/uotw/solarseeing.git
cd solarseeing
# edit index.html
git add .
git commit -m "your changes"
git push
```

Cloudflare Pages auto-deploys on push to `main`.

### Local Development

Just open `index.html` in a browser. The API calls work from `file://` — no local server needed.

## Contributing

This is a first-version scoring model that needs real-world validation. Contributions welcome:

- **Imaging session reports** — compare the forecast score to your actual seeing conditions
- **SSM data correlation** — if you have a Solar Scintillation Monitor, comparing readings to predictions would be invaluable
- **Scoring threshold refinements** — adjust the wind speed breakpoints based on empirical data
- **Additional data sources** — temperature gradients, boundary layer height, humidity
- **UI improvements** — mobile optimization, accessibility, additional visualizations

## License

MIT

## Acknowledgments

Built with data from [Open-Meteo](https://open-meteo.com/) and informed by decades of community knowledge from [Cloudy Nights](https://www.cloudynights.com/), [SolarChat](https://solarchatforum.com/), and the professional observatory seeing research community.
