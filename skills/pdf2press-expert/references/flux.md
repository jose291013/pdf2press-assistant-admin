# PDF2Press Core – workflow editor (validated 14/11/2026)

## Workflow settings (tab « Flux de travail »)
- Nom du flux de travail, Profils de communication, Politique de conservation.
- **Gestion des variables**: rows Nom + Type (String, Chiffres, Oui/Non, Sélectionner with options `Label&Value,Label2&Value2` or `Option1, Option2`), default, required, public. « Ajouter une variable », then « Enregistrer » in the dialog.
- Variable names must match **exactly** the Pressero question label (PJM prompt) or the PJM attribute key (see SKILL.md). They appear first in the « Champ » list of conditions and as `{Nom}` in value fields.

## Steps (palette « Étapes »)
| Step | Configuration (tab « Étape ») |
|---|---|
| Injecter | inbound e-mail profiles + cloud profiles (none needed for Pressero / QuoteOrchestrator injection) |
| Preflight | Profil de preflight (required) |
| Corriger | Profil de correction (required) |
| Imposer | Profil d'imposition |
| Condition | « Configurer l'étape conditionnelle »: SI rows (Et / Ou, Champ, Opérateur, Valeur), units; outputs **Yes / No** |
| Épreuve | Profil d'épreuve + Comportement de validation: Toujours autoriser l'approbation · Désactiver l'approbation en cas d'échec · Demander la confirmation en cas d'échecs · Demander la confirmation en cas d'avertissements ou d'échecs |
| En attente | public hold message |
| Conservation | retention policy |
| Routeur | Si / Sinon si / Sinon → rediriger vers un flux, écraser des variables, changer le profil de communication, continuer, annuler |
| Action | « Configuration de l'étape d'action »: Appliquer l'action (same list as Corriger) with values that may be variables, e.g. Redimensionner les pages Largeur `{Petit côté}`, Hauteur `{Grand côté}`, en étirant, distorsion 2 %, units |
| Complet | — |

Condition fields: variables, then system fields — Cloud Injector (type, company, contact, folder, profile), Document (has annotation, has form field, password protected, pages same orientation), Email Injector (from email / name), Empty Pages (first, last, second, next to last), File Info (file name, size MB, first page width / height, trimbox width / height, all pages same dimension, page count), Fix results (bleed size added, colour spaces, flattening, resize pages success…), Preflight results.
Text operators: Égal à, Différent de, Contient des mots, Ne contient pas de mots, Correspond à n'importe lequel, Ne correspond à aucun. Numeric: =, ≠, >, ≥, <, ≤.

## Editor rules (save is refused otherwise)
- Exactly one Injecter and one Complet; every step connected; one output per step (two for Condition: Yes / No); a step may receive several inputs; no connection back to the previous step.
- A profile is required on every Preflight and Corriger step.
- A Preflight step must exist before any Condition or Routeur; Conditions and Routers need rules.

## Examples
- Simple: Injecter → Corriger → Preflight → Épreuve → Complet.
- Banners (demo): Injecter → Corriger → Preflight → Action (resize to `{Petit côté}` × `{Grand côté}`) → Condition `{Révision de vos fichiers}` = « Simplifié… » → Yes: Complet / No: Épreuve → Complet.
- Envelope DL / C5, 1 or 2 sides (built by Claude, `references/../../creer-flux-pdf2press/references/exemple-enveloppe.md`).

## Automated build (validated)
1. Open `/workflows/create?embed=true&lang=fr`; set the viewport to 1400×1000 (`resize_window`), screenshots are then 800×571 (factor 1.75 between screenshot and CSS pixels); reset to `desktop` at the end.
2. Fill name, communication, retention (clicks in the right panel), variables (dialog).
3. **Drop steps by script** (mouse drags do NOT work: HTML5 drag and drop) – helper `drop()` in `scripts.md`. Plan a grid: nodes are ~210 CSS px wide (≈120 px in the screenshot after one zoom-out). Collapse the palette (chevron) and zoom out once (« − ») to gain room.
4. Move nodes with mouse drags (`left_click_drag` on the node body) if they overlap.
5. Select each node (click) → tab « Étape » → open the profile list → type `TEST-Claude` (or the prefix) to filter → click the item. Read back: `.vue-flow__node` innerText.
6. **Connect with mouse drags** from the source handle (`.vue-flow__handle.source`: `<node>__handle-bottom`, or `condition-yes` / `condition-no`) to the target handle (`<node>__handle-top`). Get handle positions by script (`handles()` in `scripts.md`). Check `.vue-flow__edge` ids `edge-<src>-<dst>`.
7. Conditions: select the node → « Configurer l'étape conditionnelle » → Champ (list), Opérateur (list), Valeur (combobox: type, Enter, click again in the field, Tab to close the suggestions) → Appliquer. Prefer « Contient des mots » for option texts unless an exact match is required.
8. « Enregistrer »: the URL becomes `/workflows/edit/{id}`. Reload and verify node count, edge count, no warning icon, profiles, and one condition rule.
