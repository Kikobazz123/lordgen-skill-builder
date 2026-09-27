---
name: lordgen-brand
description: Pointer to LordGen's actual brand identity (colors, type, logo files) and what's still unratified. Read before any skill produces client- or public-facing visual or written output.
---

# Brand identity

Read when: a skill generates anything a client, prospect, or the public will see —
an email, a deck, a landing page, a proposal document.

**Source of truth:** [`newsletter-brand-guideline.md`](https://github.com/Kikobazz123/lordgen-newsletter-pipeline/blob/main/newsletter-brand-guideline.md) in lordgen-newsletter-pipeline — read that file
directly rather than duplicating it here; it will drift out of sync otherwise. Summary of
what it confirms, so you know whether it's worth opening:

## Confirmed

- **Palette:** Ink `#0A0A09` (field), Graphite `#141312` (raised field), Regal Gold `#C9A24B`
  (primary — rules, eyebrows, links, the mark, under 15% of any surface), Leaf `#F0E2BC`
  (headline trim), Brass `#8A6A24` (borders/dividers only — fails body-text contrast), Bone
  `#E8E6E1` (body text), Slate `#8C8A85` (muted/captions). Gold is never a background for body
  copy.
- **Type:** Archivo, falling back to `'Helvetica Neue', Helvetica, Arial, sans-serif`. Flush
  left always — never centred or justified, including headlines.
- **Logo files:** `../../Newsletter Demo/lordgen-ai-logo.svg` (master, web/decks/docs),
  `lordgen-mark-816.png` (raster source), `lordgen-mark-email-96.png` (email-safe raster —
  SVG is stripped by Gmail/Outlook/Yahoo, use PNG in any email context).
- **Layout discipline:** square containers, zero border-radius, everywhere — "the single rule
  that keeps the emails looking like the brand rather than like Mailchimp." Likely worth
  carrying into non-email surfaces too, though that's not yet confirmed.
- Dark-first design; the guideline documents specific handling for forced-light-mode email
  clients if a skill ever touches email again.

## Explicitly NOT yet covered

The guideline itself flags this scope limit: **voice and tone, subject-line style,
positioning, data-viz palette, and UI state colours are not covered** — those belong to the
full consultancy identity, not the newsletter demo, and haven't been ratified yet. A skill
needing brand voice (e.g. a proposal-drafting or website-copy skill) should ask rather than
infer a tone from the visual palette.

Two color values are marked `[new]` in the source guideline (Regal Gold's hex, Bone, Slate) —
sampled/decided during the newsletter build, not printed in the original Identity Standard.
Worth flagging to the user for explicit ratification before they're treated as permanent.

## Changelog
- v1.0 — Initial, pointer built from `Newsletter Demo/newsletter-brand-guideline.md`
