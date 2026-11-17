---
name: creer-profil-pdf2press
description: Creates or edits a PDF2Press Core profile — preflight, correction (fix), imposition, proof (épreuve), communication e-mail, retention policy, inbound e-mail or cloud storage — by duplicating a close profile or from scratch, then saves and verifies it. Use when the user says "crée un profil preflight", "profil de correction FOGRA39", "profil d'imposition 85x55 sur SRA3", "modifie le profil d'épreuve", "crea un perfil de preflight", "PDF2Press fix profile", or when skill creer-flux-pdf2press needs profiles.
---

# Create or edit a PDF2Press profile

Load skill `pdf2press-expert` first (golden rules, access through the iframe jump, map). Field details: `pdf2press-expert/references/profils.md`; helpers: `pdf2press-expert/references/scripts.md`.

## 1. Clarify (short)
- Account (demo or customer) and whether it is a test (`TEST-Claude – …`) or final (`P2P – …`).
- Type and values. Derive values from the product when possible (format, pages, sides, bleed policy, colour standard). Typical defaults to propose:
  - Preflight: integrity on (fail), resolution fail < 150 / warn < 250 DPI, fonts embedded (fail) and ≥ 5 pt (warn), rich black ≤ 300 % (warn), page dimensions = trim (+ 2 × bleed if bleed is required) with tolerance 1–2 mm (fail), page count = sides or pages (fail), bleed ≥ 3 mm (fail) only if the product needs bleed.
  - Fix: ICC FOGRA39 (coated) or FOGRA47 (uncoated) as the shop uses, rich black reduce (max 290, min black 50), black overprint, flattening 300 DPI; add bleed 3 mm (mirror) when the flow should repair missing bleed.
  - Proof: Avertissements et erreurs, all reports on.
- Show the planned values in plain words and wait for the go-ahead (customer account) before saving.

## 2. Find a starting point
- Open the list (`/<route>?embed=true&lang=fr`), search names close to the need. Read a candidate (open it, read fields and switches).
- If one is close: **duplicate** it (icon duplicate) → the copy is « <name>Copy » (reload or search « Copy »), open the copy by its edit icon, take the id from the URL. Several copies at once are allowed (names CopyCopy, CopyCopy2…); rename each right away.
- Otherwise: « Ajouter » → `/<route>/create?embed=true&lang=fr`.

## 3. Fill and save
- Rename first (`Nom`), then switches (card « Options »), then numeric fields by card title and label, then selects with real clicks.
- Save (« Enregistrer »), wait, reload, read everything back. Report a short table of the saved values.
- Inbound e-mail / cloud: Claude fills the non-secret fields and saves; the **user** types passwords and clicks « Se connecter » (OAuth). Never test a connection with credentials Claude typed.

## 4. Pitfalls
- The list does not refresh after a duplicate; the copy keeps the original's creation date sometimes (1/1/1901 for old ones): search by name.
- A switch turned off keeps its values (they are ignored until it is turned on again).
- Units: the profile unit applies to every length of the profile; check it before typing dimensions.
- Page dimensions: enter the format as the file is expected (e.g. landscape envelope 229 × 162). Whether PDF2Press accepts a rotated file (162 × 229) is not verified yet: test with a real file before relying on it.
- Never delete a profile used by a workflow; ask the user before any deletion.

## Status (v0.1.0)
Validated: preflight (duplicate, rename, switches, dimensions, page count), fix (duplicate, rename). Not yet exercised by Claude: imposition creation, communication editor, inbound e-mail, cloud, retention creation.
