# Portfolio — Claude Instructions

## Case studies

Before creating, editing, or critiquing any case study page (`*/index.html`), read `CASE-STUDY-GUIDELINES.md` in full. It contains:
- Canonical font stack (Kumbh Sans + Playfair Display — no Caveat, no Inter)
- CSS token names (`--page` not `--white`, `--serif` for Playfair)
- Layout rules (body max-width, section spacing, cover image)
- Every component pattern (hero, section-heading, callout, proto-embed, bullet-list, site-preview, page transition)
- Absolute bans (side-stripe borders, identical card grids, gradient text, em dashes)
- Content rules (hero copy, insights with sources, results as visual peak)
- Impeccable command sequence

The reference implementation is `video-kyc/index.html`. When in doubt, match it exactly.

## Serving locally

```
npx serve -p 8080 /Users/mehuljatiya/Documents/mehulportfolio
```

Case study URLs use trailing slashes: `http://localhost:8080/video-kyc/` not `/video-kyc/index.html`. This is required for `npx serve` to resolve relative asset paths correctly.

## Portfolio index

`index.html` is the portfolio homepage. Case study card hrefs must use trailing-slash paths (`video-kyc/` not `video-kyc/index.html`). The index has a forward push-transition — new case study pages must have the matching reverse transition (see CASE-STUDY-GUIDELINES.md).
