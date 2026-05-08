# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Buy vs Rent financial calculator for the Quebec real estate market. Compares net worth trajectories of buying a property versus renting and investing the difference, with optional plex (multi-unit) rental income modeling.

**Live repo:** https://github.com/francoisdavid/Renting-VS-Buying

## Running the App

No build step. Open `index.html` directly in a browser — the entire app is a single self-contained file.

The only external dependency is Chart.js v4.4.1 loaded via CDN, so an internet connection is required for the charts to render.

## Architecture

Everything lives in `index.html` (~830 lines), split into three logical sections:

1. **CSS (lines 8–404)** — design tokens via CSS custom properties in `:root`, with a full dark-mode override via `@media(prefers-color-scheme:dark)`. Color palette uses semantic names (`--blue`, `--green`, `--red`, `--amber`) each with `-bg` and `-text` variants for accessible contrast.

2. **HTML (lines ~1–404)** — input panels, the breakeven hero card, invest comparison boxes, two Chart.js canvases, and a detailed financial table. No templating — the DOM is static and updated via `innerHTML`/`textContent` in JS.

3. **JavaScript (lines ~406–829)** — a single `calc()` function drives all computation. It reads slider/input values, runs the full amortization and investment model, then writes results directly to the DOM and re-renders both charts.

### Key calculation concepts

- **Buyer path:** Tracks equity build-up (principal paydown + appreciation), deducts mortgage interest, property tax, school tax (0.16 % in Quebec), maintenance, and transaction costs (welcome tax on purchase, 5 % realtor fees on sale).
- **Renter path:** Invests the down payment + monthly savings vs. buying into a portfolio with a configurable nominal return; the "monthly savings" is the difference between the equivalent rent and the buyer's monthly all-in cost.
- **Breakeven year:** First year where buyer net worth ≥ renter net worth.
- **Plex mode:** When enabled, rental income from additional units offsets the buyer's monthly carrying cost and shifts the breakeven point earlier.

### i18n

FR/EN strings are stored in a plain JS object (`T = { fr: {...}, en: {...} }`). `setLang(lang)` walks every `[data-i18n]` element and sets its `textContent`, then calls `calc()` to reformat currency labels.

### Slider pattern

Each range input is paired with a display `<span>` updated via an `oninput` handler that also calls `calc()`. Preset "pill" buttons set the slider value and fire `calc()` directly.
