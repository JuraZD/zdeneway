# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Static single-page website (SPA) for a private tour guide in Zadar, Croatia. The entire site lives in a single `index.html` file — all HTML, CSS, and JavaScript are embedded. Designed for deployment on GitHub Pages or Netlify.

## Architecture

**Single-file design** (`index.html`): intentional choice for zero-dependency deployment. Structure within the file:
1. `<style>` block — all CSS with custom properties at the top of `:root`
2. HTML sections: `nav`, `#hero`, `#about`, `#tours`, `#gallery`, `#booking`, `#reviews`, `footer`
3. `<script>` block at the bottom — i18n engine, booking modal, Stripe simulation, scroll/nav behavior

**i18n system**: All translatable text uses `data-i18n="key"` attributes. The `TRANSLATIONS` JS object holds strings for all languages. `setLang('en')` iterates DOM and swaps text. Hero titles have their own `heroTitles` map due to multi-line animated layout.

**Booking flow**: Form submit → `openBookingModal(tourName, price)` → Stripe-like modal → `simulatePayment()` (fake, 2s delay). Not connected to a real payment processor.

## Color Palette

Defined as CSS variables in `:root` (top of `<style>` block). Reference `boja.md` for the project's intended palette:
- Crna (Black): `#000000`
- Bijela (White): `#FFFFFF`
- Bež (Beige): TBD
- Crvena (Red): TBD
- Zlatna (Gold): `#C9A84C`

Current site uses: `--navy: #0b1f35`, `--terra: #c4623a`, `--gold: #c9a84c`, `--sand: #f2ead9`, `--cream: #faf6ef`.

## Key Patterns

**Adding a language**: (1) add `<button class="lang-btn" onclick="setLang('xx')">XX</button>` to nav, (2) add translation keys to `TRANSLATIONS.xx`, (3) add entry to `heroTitles`.

**Gallery layout**: First and fourth items span 2 columns (`grid-column: 1/3` and `3/5`). Captions come from `data-caption` on `.gallery-item`.

**Tour cards**: Each card's book button calls `openBookingModal('Tour Name', PRICE)`. The form's tour dropdown must also include the tour name as an `<option>`.

## Deployment

No build step. Deploy by pushing `index.html` to GitHub and enabling GitHub Pages (Settings → Pages → main branch / root).

Images are currently Unsplash URLs. Replace with local paths or CDN URLs — recommended size ≥ 800×600px, < 200KB each.
