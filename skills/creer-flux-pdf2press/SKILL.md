---
name: creer-flux-pdf2press
description: Designs and builds a PDF2Press Core workflow (flux de travail) for a Pressero product or a PrintJobManager engine — reads the product / PJM engine, proposes the set of profiles and the step chain (inject, fix, preflight, conditions on product options, actions, imposition, proof, complete), creates the profiles, builds the workflow in the visual editor and verifies it. Use when the user says "crée le flux PDF2Press pour le produit …", "flux pour le moteur PJM …", "workflow preflight + BAT", "ajoute une condition / une imposition au flux", "crea el flujo PDF2Press", "build the PDF2Press workflow".
---

# Build a PDF2Press workflow from a product / PJM engine

Load skill `pdf2press-expert` first. Editor details and rules: `pdf2press-expert/references/flux.md`; helpers: `pdf2press-expert/references/scripts.md`; profiles: skill `creer-profil-pdf2press`. Worked example: `references/exemple-enveloppe.md`.

## 1. Read the product (read only)
- PJM engine (plugin `pjm-atelier-moteurs`, « Reading an engine »): components, prompts, choices, materials (formats in their names), production lines (sides), attributes. Note the **exact** prompt labels and choice texts: they become workflow variables and condition values.
- Or the Pressero product page / form: question labels and options.
- Ask the user what the shop wants when the engine does not say it: bleed required or not, colour standard, proof behaviour, repair (resize / add bleed) or reject, imposition or not.

## 2. Propose the design (wait for the go-ahead)
- One workflow per Pressero product (Pressero assigns one workflow per product): use **Conditions on variables** to branch per format / sides instead of several workflows.
- Typical chain: Injecter → Corriger (colour, rich black, overprint, flattening) → Preflight commun (integrity, resolution, fonts, rich black) → Condition(s) on `{Format}` / `{Faces}` → per-branch Preflight (dimensions, page count) [or Action resize / add bleed, then Preflight] → [Imposer] → Épreuve → Complet.
- A Preflight must come before any Condition. Branches may join again on the same step (e.g. Épreuve).
- Variables: names = exact question labels or PJM attribute keys. Use a PJM attribute (type Chiffres) for computed sizes (final width / height in mm) used by Actions; if it does not exist yet, propose creating it in PJM (plugin `pjm-atelier-moteurs`, attributes reference) and wait.
- Condition values: « Contient des mots » with a distinctive word of the option (« DL », « 1 face ») is more tolerant; « Égal à » requires the exact text.
- Reuse existing proof / communication / retention profiles when suitable; create the rest.
- Show: list of profiles to create (names with the prefix), the step chain as a short diagram, variables, condition rules, proof behaviour.

## 3. Build
1. Create / duplicate the profiles (skill `creer-profil-pdf2press`), note their exact names.
2. Workflow: either duplicate a close workflow (list → duplicate icon, then edit) or `/workflows/create?embed=true&lang=fr`.
3. Follow the automated procedure of `flux.md`: viewport 1400×1000, settings + variables, drop steps by script on a planned grid, move overlapping nodes with mouse drags, set each node's profile, connect handles with mouse drags (Condition: `condition-yes` / `condition-no`), configure conditions / actions, Enregistrer.
4. Verify after reload: node and edge counts, no warning icon, each node's profile (node text), one condition rule re-opened (Annuler to close), workflow settings. Reset the viewport (`desktop`).

## 4. Test and hand over
- Tell the user how to test: assign the workflow to a product (skill `lier-pdf2press-pressero`), order with a PDF of each case (good file, wrong format, wrong page count), check the sessions list (`/workflows/sessions`: status, injection, workflow) and the proof page.
- If variables do not arrive (conditions always take the « No » branch), compare the variable names with the product's question labels / attribute keys and fix them.
- Record the objects created (names, ids) for the user.

## Status (v0.1.0)
Validated on the demo account (14/11/2026): workflow from scratch with 12 steps, 14 links, 3 conditions on variables, profiles on every step. Not yet exercised: Action steps (resize, add bleed), Imposer, Routeur, En attente, duplication of a workflow, end-to-end session from Pressero.
