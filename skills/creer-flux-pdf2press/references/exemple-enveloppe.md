# Worked example – envelopes DL / C5, 1 or 2 sides (built 14/11/2026, demo account)

## Source: PJM engine « Envelope FR »
- Q1 Quantité (500–999999, default 500).
- Option « Taille »: « DL sans fenêtre » (material: envelope 225 × 110 no window) / « C5 avec fenêtre » (envelope C5 229 × 162 right window).
- Option « Print »: « Couleur 1 face » / « Couleur 2 faces » (production: colour 1 side / 2 sides).
- Shop choices: one workflow, exact format without bleed (envelopes are already made), pages according to Print, FOGRA39 + black handling, proof « confirmation on warnings or failures ».

## Profiles
| Name | Type | Settings |
|---|---|---|
| TEST-Claude – FIX – Enveloppe FOGRA39 | fix | copy of « FIX de base »: ICC FOGRA39, rich black reduce (290 / 50), black overprint, flattening 300 DPI |
| TEST-Claude – PF – Enveloppe commun | preflight | integrity, resolution fail < 150 / warn < 250, fonts, rich black 300 % |
| TEST-Claude – PF – Enveloppe DL 225×110 1 page | preflight | dimensions 225 × 110 tol. 2 (fail), pages 1–1 (fail), orientation |
| … DL 225×110 2 pages | preflight | same, pages 2–2 |
| … C5 229×162 1 page | preflight | 229 × 162 tol. 2, pages 1–1 |
| … C5 229×162 2 pages | preflight | 229 × 162, pages 2–2 |
Proof « Proof card », communication « FR-Communication », retention « RTPOLICIE1 » were reused.

## Workflow « TEST-Claude – FLUX – Enveloppe DL/C5 1-2 faces »
Variables: `Taille` (String), `Print` (String) = the PJM prompts.
```
Injecter → Corriger (FIX Enveloppe FOGRA39) → Preflight commun
  → Condition {Taille} contient « DL »
      Yes → Condition {Print} contient « 1 face » → Yes: PF DL 1 page / No: PF DL 2 pages
      No  → Condition {Print} contient « 1 face » → Yes: PF C5 1 page / No: PF C5 2 pages
  → (the 4 preflights) → Épreuve (Proof card, confirmation on warnings or failures) → Complet
```
12 nodes, 14 links. Grid used (screenshot coordinates, viewport 1400×1000, palette collapsed, one zoom-out): main column x ≈ 300, conditions at x 200 / 420, four preflights at x 127 / 241 / 367 / 485, rows every ~45–60 px.

## Next (more complex flows)
- Action « Redimensionner les pages » to the ordered size from PJM attributes (e.g. `LARGEUR_FINIE_MM`, `HAUTEUR_FINIE_MM`).
- Condition on « Fix – Bleed Size Added » / preflight bleed result → Action « Ajout de fond perdu » only when missing.
- Imposer with a profile matching the press sheet (from the PJM engine's sheet format and `Nup()`).
