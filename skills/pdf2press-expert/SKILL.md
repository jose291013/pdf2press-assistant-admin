---
name: pdf2press-expert
description: Expertise on the PDF2Press Core admin (Aleyant prepress / preflight / proofing, embedded in the Pressero admin) driven through Claude's browser. Use when the user asks how PDF2Press works, where a setting lives, or wants to read or change PDF2Press profiles or workflows ("PDF2Press", "preflight", "profil de correction", "flux PDF2Press", "BAT PDF2Press", "perfil de preflight", "flujo PDF2Press", "PDF2Press workflow"). Also loaded by every other skill of this plugin before it touches PDF2Press.
---

# PDF2Press Core – expert knowledge

Answer in the user's language (French, Spanish or English). Use plain words with the user; keep ids, routes and selectors for yourself.

## Golden rules (always)
1. **Never type a password**, never read or show tokens. If a login page appears (Pressero admin, PJM), ask the user to sign in in Claude's browser tab, then continue.
2. **Never call the PDF2Press REST API with the session token** (`ams.api.pdf2press.aleyant.com`): Claude's safety checks refuse it. Work through the user interface only.
3. **Test objects first**: on a demo account, create objects named `TEST-Claude – <TYPE> – …`, test them, then ask before deleting. On a customer account, show the plan and get a go-ahead before each save.
4. **Naming** (validated): `P2P – <TYPE> – <description>` with TYPE = FLUX, PF (preflight), FIX, IMP (imposition), BAT (épreuve), COM (communication), RET (rétention); optional customer code `[CODE]` right after the prefix. The search box of each list then finds everything the plugin created.
5. **One object at a time, then verify**: after each save, reload the page and read the values back.
6. **Never click** Supprimer / Eliminar / Delete (profile, workflow) unless the user explicitly asks for that exact object. Leaving a page without saving = navigate away (use `force` if a leave-page warning blocks).
7. Prefer **duplicating** an existing profile or workflow close to the need; build from scratch only when nothing fits.

## How PDF2Press Core is reached
- Core lives inside the Pressero admin (`https://admin.{region}.v6.pressero.com`, e.g. `admin.ams.v6.pressero.com`), menu **PDF2Press**. Each Pressero page `/pdf2press/<page>` only holds an **iframe** to the PDF2Press app (Vue 3 + Vuetify): `https://{region}.ui.pdf2press.aleyant.com/<route>?embed=true&lang=es&token=…`.
- The iframe is cross-origin (not readable from the Pressero page). Technique: open one Pressero PDF2Press page, run `location.href = document.querySelector('iframe').src` (the app stores its session in its own `localStorage`), then navigate directly to `https://{region}.ui.pdf2press.aleyant.com/<route>?embed=true&lang=fr` (`lang=fr|es|en` = interface language). Allow that site in the browser the first time.
- In embed mode, list buttons « éditer » / « Ajouter » jump to `/undefined/pdf2press/<type>/edit/<id>` (404): read the id from the URL, then open `/<route>/edit/<id>?embed=true&lang=…` (creation: `/<route>/create?…`).
- Session expired → open a Pressero PDF2Press page again and repeat the iframe jump.

## Map (routes)
| Pressero menu (ES) | Pressero page | App route |
|---|---|---|
| Dashboard | /pdf2press/extdashboard | /dashboard |
| Sesiones del flujo de trabajo | /pdf2press/workflowsessions | /workflows/sessions |
| Flujos de trabajo | /pdf2press/extworkflows | /workflows · /workflows/edit/{id} · /workflows/create |
| Notificaciones del sistema | /pdf2press/extnotifications | /notification |
| Perfiles › Preflight | /pdf2press/preflight | /preflight |
| Perfiles › Corregir | /pdf2press/fix | /fix |
| Perfiles › Imposición | /pdf2press/impose | /impose |
| Perfiles › Prueba | /pdf2press/proofing | /proofing |
| Perfiles › Email entrante | /pdf2press/inboundEmailProfile | /email |
| Perfiles › Almacenamiento en la nube | /pdf2press/cloudStorage | /cloud-storage |
| Perfiles › Comunicado | /pdf2press/communication | /communication |
| Gestión de archivos › Retención | /pdf2press/retention | /retention |
| Gestión de archivos › Renombrar archivo | /pdf2press/rename | /rename |
| Configuración | /pdf2press/extsettings | /settings |

Every profile page: buttons **Supprimer** (danger) · **Rejeter** · **Enregistrer**. Lists: **Ajouter**, edit icon (`.ri-edit-box-line`), duplicate icon (`.ri-checkbox-multiple-blank-line` → « <name>Copy », then « …CopyCopy2 »…), search box, 20 rows per page. The list does not refresh after a duplicate: reload or search « Copy ».

## What each object does (details: `references/profils.md`)
- **Preflight**: 14 checks (integrity, file size, image resolution, page dimensions, page count, page multiples, orientation, empty pages, bleed, spot colours, fonts, rich black, overprint, text safety distance), each « avertir » (warning) or « échouer » (failure).
- **Corriger (fix)**: 10 automatic corrections (ICC colour conversion, resize pages, image upscale, truncate pages, add bleed, crop marks, rich black, outline fonts, overprint, transparency flattening).
- **Imposition**: output sheet, trim, bleed, gutter, margins, N-up X/Y, method (step & repeat, cut & stack, die cut, saddle stitch…), overlay PDF, crop marks, duplex.
- **Épreuve (proof)**: which issues the customer sees on the approval page.
- **Communication**: the proof e-mail (sections, warning / error texts, colours, logo).
- **Rétention**: days to keep the source file and the final version.
- **E-mail entrant / Cloud**: injection sources (the user connects accounts and types passwords himself).
- **Renommer**: file name templates with tokens `%WORKFLOW_NAME%`, `%SESSION_ID%`, `%INJECTION_TYPE%`, `%USER%`, `%VAR_name%`, `%ORIGINAL_FILENAME%`, `%PRODUCTION_FILENAME%`.
- **Flux de travail**: visual editor (Vue Flow) chaining steps; see skill `creer-flux-pdf2press` and `references/flux.md`.

## Variables coming from Pressero / PJM (key rule)
- A workflow variable can take the value of a **Pressero product question** (for a PJM-priced product: the PJM component prompt, e.g. « Taille », « Print ») or of a **PJM attribute** returned with the price.
- The variable **name must match exactly** (case, accents, spaces) the question label or the attribute key. Its value is the chosen option text (e.g. « DL sans fenêtre »).
- For computed values (final width / height in mm, bleed…), use a PJM attribute (global key in PJM → Paramètres → Attributs, NCalc value on the component or choice; plugin `pjm-atelier-moteurs`, `references/attributs.md`) and a workflow variable of the same name, type Chiffres.

## Working with the app (techniques that work)
- Text / number fields: native setter + events: `Object.getOwnPropertyDescriptor(HTMLInputElement.prototype,'value').set.call(i, v); i.dispatchEvent(new Event('input',{bubbles:true})); i.dispatchEvent(new Event('change',{bubbles:true}))`.
- Switches: `input.click()` on `.v-switch input` (label = first text line of the switch's row). Selects (Vuetify): real clicks, then pick the item in `.v-overlay--active .v-list-item`.
- Save: click the `button` whose text is « Enregistrer », wait ~2 s, `location.reload()`, read back.
- Visual workflow editor: see `references/flux.md` (HTML5 drag and drop by script, connections with mouse drags, viewport 1400×1000).
- Labels of every screen: public files `assets/ui-*.js` of the app (keys like `preflight_…`, `workflow_…`; French and Spanish complete). File names change with each release: find them in `performance.getEntriesByType('resource')`.

## Pressero link (summary)
- Product → tab **Téléchargement** (upload) → tick « utiliser PDF2Press » → a third column appears → choose the PDF2Press workflow. Skill `lier-pdf2press-pressero`.
- Site → Paramètres → tab PDF2Press (`#awe`): proofing during checkout, require proof approval, « approve all » on the proof list. If Pressero loses the PDF2Press connection it falls back to e-mail proofing.

## References
- `references/profils.md` – every profile type: fields, options, defaults, examples.
- `references/flux.md` – workflow editor: steps, variables, conditions, actions, rules, automated build procedure.
- `references/scripts.md` – verified JavaScript helpers (read / fill a profile, duplicate, drop steps, read handles and edges).
- Skills: `creer-profil-pdf2press`, `creer-flux-pdf2press`, `lier-pdf2press-pressero`. PJM engines and attributes: plugin `pjm-atelier-moteurs`. Pressero admin: plugin `pressero-assistant-admin`.
