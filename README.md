# VERTO — Mid-Season Sale (HTML Email)

A production-grade responsive HTML email for a fictional DTC brand, built to demonstrate
cross-client robustness, accessibility, and a deliberate dark-mode strategy.

**Live preview:** rendered via GitHub Pages → `https://pj-web.github.io/verto/`

---

## Engineering approach

- **Hybrid / fluid layout** — fixed `600px` container for desktop, fluid down to mobile via a single
  `max-width:600px` breakpoint. Two-column product grid and the trust strip stack cleanly on narrow viewports
  using `display:block` column classes (no media-query reliance for the core stack where avoidable).
- **Bulletproof buttons** — padding-based CTAs with `mso-padding-alt` for vertical centering in Word-engine
  Outlook, plus an MSO-only fixed-width wrapper so the hero button centers correctly inside the VML textbox.
- **Outlook (Word engine)** — hero background rendered with a VML `<v:rect>` image fill behind the CSS
  gradient; `<o:OfficeDocumentSettings>` pins PPI to keep dimensions stable.
- **Bulletproof logo** — `<img>` plus a styled `ALT` fallback that surfaces the brand name when images are
  blocked (e.g. Outlook's default image-blocking).
- **Accessibility** — `role="article"`, `aria-label`, semantic heading, real text (not images) for copy,
  preheader text for the inbox preview, and decorative icons marked `alt=""`.

---

## Dark-mode strategy

Dark mode in email is not one feature — clients fall into three tiers, and each gets the treatment it can
actually honor. The guiding principle: **control the clients that allow control; design defensively for the
ones that don't.**

| Tier | Clients | Mechanism | Result |
|------|---------|-----------|--------|
| **1. Full control** | Apple Mail (macOS/iOS), iOS Mail, Samsung Mail, Outlook 2019+ (desktop) | `@media (prefers-color-scheme: dark)` | Exact, designed dark palette — pixel-level control |
| **2. Attribute-targeted** | Outlook.com, Outlook mobile apps | `[data-ogsc]` selectors | Reliable for display swaps (logo); color overrides are best-effort |
| **3. Forced auto-inversion** | Gmail apps, Outlook web (forced dark) | Client's own algorithm — **not author-controllable** | Defensive palette ensures the inversion lands clean |

### Tier 1 — designed palette
A dedicated cool, near-black palette (`#0f1115` scaffold, `#1a1d24` surfaces) plus a logo swap, an off-white
hero pill (`#fffffe` dodges Apple Mail's pure-`#ffffff` clamp), and a warm-toned review plate so it still lifts
off the surfaces in dark. The `color-scheme` / `supported-color-schemes` meta tags opt the email in and
suppress some aggressive auto-inversion.

### Tier 3 — the honest part (and the key design decision)
On a **light-first** email you cannot force a specific dark background in Gmail or in Outlook web's forced dark
mode: Gmail ignores `prefers-color-scheme` entirely, and Outlook web overrides author colors with its own
luminance inversion. Gmail inverts this design to a clean dark on its own, so it needs nothing extra.

**Outlook web** was the real problem to solve. Its partial inversion skews **cool** near-blacks toward a
**warm/brown** tone — which is exactly why a `#13151a` (blue-tinted) button rendered muddy brown and nearly
invisible. The fix is at the source, not in overrides Outlook ignores:

- **Neutralized the near-black backgrounds** (footer, secondary CTA) from cool `#13151a` to neutral `#1a1a1a`.
  A neutral source inverts to neutral gray, not brown.
- **Neutralized the light scaffold** (`#eceef2` → `#eeeeee`) and hairline divider so their auto-inversion
  produces a clean neutral dark instead of a tinted, muddy one.

This is indirect influence — the only real lever for a client that won't take direct overrides — so the result
is a *cleaner* forced-dark, not a pixel-perfect one. That distinction is intentional.

### What was deliberately *not* shipped
`mix-blend-mode` text hacks and Outlook display-swap blocks can brute-force more, but they're brittle
(version-dependent, prone to silent breakage on client updates). They're left out of the production template
on purpose: graceful degradation over fragile control.

---

## Compatibility & testing

- Tested intent across Apple Mail (iOS/macOS), Gmail (web + apps), Outlook (web, Windows, mobile), Samsung Mail.
- **Always test in real clients** (Litmus / Email on Acid). A browser's dark-mode toggle does **not** reproduce
  client rendering — especially Outlook and Gmail inversion.

---

## Before sending (production checklist)

- [ ] Swap relative/portfolio asset paths to **absolute `https://` URLs** on a reliable CDN/host.
- [ ] Replace `https://example.com/*` placeholders with real destination + UTM links.
- [ ] Confirm `Unsubscribe` / `Update preferences` point to working endpoints (legal requirement).
- [ ] Verify the preheader text and inbox-preview truncation.
- [ ] Run a seed/inbox-placement + rendering test before the broadcast.

---

## Tech notes

- No external CSS, no web fonts (system font stack for reliability).
- No `<form>` elements; all CTAs are links.
- Single-file, inline-friendly; the `<style>` block holds only responsive + dark-mode rules that must live in
  the head.

*VERTO is a fictional brand created for portfolio purposes.*
