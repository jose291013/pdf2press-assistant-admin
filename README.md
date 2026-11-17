# PDF2Press – Assistant Admin (v0.1.0)

Plugin Cowork qui pilote l'admin **PDF2Press Core** (Aleyant), intégré à l'admin Pressero, via le navigateur de Claude. Répond en français, espagnol ou anglais.

## Compétences
| Compétence | Rôle |
|---|---|
| `pdf2press-expert` | Accès à l'appli PDF2Press depuis Pressero (saut d'iframe, `lang=fr`), carte des écrans et routes, règles de sécurité, nommage, variables Pressero / PJM. Références : profils, éditeur de flux, scripts JavaScript vérifiés. Chargée par toutes les autres. |
| `creer-profil-pdf2press` | Créer / modifier un profil (preflight, correction, imposition, épreuve, communication, rétention, e-mail entrant, cloud) par duplication ou à partir de zéro, puis vérifier. |
| `creer-flux-pdf2press` | Lire un produit Pressero / moteur PJM, proposer les profils et l'enchaînement d'étapes (conditions sur les options, actions, imposition, BAT), créer les profils, construire le flux dans l'éditeur visuel et le vérifier. Exemple complet : enveloppes DL / C5 1-2 faces. |
| `lier-pdf2press-pressero` | Rattacher un flux à un produit Pressero (onglet Téléchargement → « utiliser PDF2Press » → colonne du flux), réglages PDF2Press du site, suivi des sessions. |

## Prérequis
- Navigateur de Claude (ou Claude in Chrome) ; l'utilisateur se connecte lui-même à l'admin Pressero et à PJM.
- Autoriser les sites `{région}.ui.pdf2press.aleyant.com` et l'admin Pressero dans le navigateur.
- Plugins complémentaires : `pressero-assistant-admin` (admin Pressero) et `pjm-atelier-moteurs` (moteurs, attributs PJM).

## Nommage
`P2P – <TYPE> – <descriptif>` (TYPE = FLUX, PF, FIX, IMP, BAT, COM, RET), code client facultatif `[CODE]` après le préfixe ; objets de test : `TEST-Claude – <TYPE> – …`.

## Limites connues (v0.1.0)
- PDF2Press **Core** uniquement (Pro : back-office, API, interfaces de dépôt client – plus tard).
- Pas d'appel à l'API PDF2Press avec le jeton de session : tout passe par l'interface.
- Mots de passe (e-mail entrant), connexions OAuth (Google Drive, OneDrive) et fichiers à déposer (PDF de superposition) : faits par l'utilisateur.
- Pas encore exercés par Claude : étapes Action (redimensionner, ajouter du fond perdu), Imposer, Routeur, création d'imposition / communication / e-mail / cloud, rattachement au produit Pressero, session de bout en bout.

## Versions
- 0.1.0 – 17/11/2026 : première version ; carte de l'admin Core, profils, éditeur de flux automatisé (dépôt d'étapes par script, liaisons, conditions sur variables), exemple enveloppes DL / C5.

## Sécurité
Jamais de saisie de mot de passe ni d'affichage de jeton, objets de test préfixés, validation de l'utilisateur avant d'enregistrer sur un compte client, une modification à la fois avec relecture, aucune suppression sans demande explicite.
