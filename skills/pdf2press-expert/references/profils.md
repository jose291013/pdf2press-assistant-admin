# PDF2Press Core – profile types (French UI, relevé 14/11/2026)

All profiles: **Nom**, often **Unité de mesure** (Millimètres, Centimètres, Pouces, Pieds, Mètres). Buttons Supprimer · Rejeter · Enregistrer.

## Preflight (`/preflight`)
Card « Options » = 14 switches, in this order:
1. Intégrité des documents – form fields (avertir / échouer), annotations or stamps (avertir / échouer). Damaged or password-protected files always fail.
2. Taille du fichier – maximum size; avertir / échouer.
3. Résolution de l'image – « Échec si moins de » N DPI, « Avertir si moins de » N DPI (e.g. 150 / 250).
4. Dimensions des pages – Largeur, Hauteur, Tolérance (unit of the profile); report « Avertir / Erreur si en dehors des tolérances »; aspect ratio report « … si toutes les pages ne sont pas proportionnelles ».
5. Nombre de pages – Minimum, Maximum; avertir / échouer if outside.
6. Multiples de pages – page count must be a multiple of N.
7. Orientation des pages – all pages same orientation; avertir / échouer.
8. Pages vides – which pages: Tous, Première, Dernière, Première et Dernière, Deuxième et avant-dernière; avertir / échouer. Crop marks count as content.
9. Fond perdu – Taille minimale; avertir / échouer if insufficient.
10. Tons directs – list type Autoriser / Interdire (names, joker `*`, e.g. `PMS*`), required colours; avertir / échouer.
11. Polices – Taille minimale des caractères (pt) avertir / échouer; embedding avertir / échouer; allowed / forbidden font names (joker `*`) and font types.
12. Rich Black – Maximum CMJN %, Niveau de noir minimum; avertir / échouer.
13. Surimpression – black text / black vectors not in overprint, allowed / forbidden spot colours in overprint; avertir / échouer.
14. Distance de sécurité du texte – Distance par rapport au bord; avertir / échouer.

Field cards are titled with the option name (e.g. « Dimensions des pages » holds Largeur / Hauteur / Tolérance). Example (envelope C5 landscape): Largeur 229, Hauteur 162, Tolérance 2, « Erreur si en dehors des tolérances »; pages 1–1 échec; résolution échec < 150 / avertir < 250; Rich Black 300 % avertir; police ≥ 3 pt avertir; intégration échec.

## Corriger / fix (`/fix`)
Card « Options » = 10 switches, in this order:
1. Transformation de l'espace couleur – Conversion de profil ICC (list, e.g. CoatedFOGRA39.icc).
2. Redimensionner les pages – width / height, Mise à l'échelle (proportionnelle / en étirant), Distorsion maximale % (1–2 % recommended).
3. Résolution de l'image – amélioration (upscale).
4. Tronquer les pages – supprimer les pages au-delà de N.
5. Ajout de fond perdu – taille; Effet miroir or Répéter la couleur du dernier pixel. Added only if missing or smaller.
6. Ajouter des marques de coupe.
7. Rich Black – convert 100 % black objects to given CMYK; reduce rich black above « Encre maximale » with « Niveau de noir minimum »; apply to Texte / Vecteurs / Texte et vecteurs.
8. Polices – vectoriser (not recommended).
9. Surimpression – black objects, spot colours; apply to Remplissages / Vecteurs / both.
10. Aplatissement des transparences – résolution DPI.
Example « FIX de base » (demo): ICC FOGRA39, Rich Black reduce (max 290, min black 50, text and vectors), overprint black objects (fills and vectors), flattening 300 DPI.

## Imposition (`/impose`)
Nom, Unité, SORTIE L×H, COUPE L×H, FOND PERDU L×H (= trim + 2 × bleed), MARGE DE RELIURE L×H (gutter), MARGE Gauche / Partie inférieure, N-UP Axe X / Axe Y, Méthode (Dupliquer & Répéter, Couper et empiler, Découper, Horizontalement/Verticalement, Verticalement/Horizontalement, Agrafé + Taille de signature multiple of 4), SuperpositionPDF (file, uploaded by the user), Inclure les traits de coupe, Recto-verso, Générer / Rafraîchir l'aperçu. List columns: Nom, Unités, Sortie, Méthode.
Example: 85×55 on 320×450, bleed 91×61, gutter 6×6, margins 26.5 / 14.5, 3 × 7, step & repeat, crop marks, duplex.

## Épreuve / proof (`/proofing`)
Nom, Affichage (Erreurs uniquement / Avertissements et erreurs), card « Rapports » = the 14 preflight checks as switches (which issues are shown on the approval page), example preview.
The **approval behaviour** is set on the workflow step, not here (see flux.md); account default in Paramètres.

## Communication (`/communication`)
Three tabs:
1. Paramètres – Nom; « Affichage » switches: Logo, Titre, Texte de l'en-tête, Aperçu du fichier, the 14 checks, Texte du bas de page, Bouton Voir le BAT, Informations sur l'entreprise. Subject optional.
2. Éditeur – click a section of the e-mail preview: Voir les avertissements / les erreurs / les deux, Titre d'avertissement, Texte d'avertissement (rich text), Titre de l'erreur, Texte d'erreur; variables can be inserted.
3. Personnaliser – colours (page, content, headings, texts, links, buttons, button text), company logo.

## Rétention (`/retention`, menu Gestion des fichiers)
Summary (sessions, files, storage, deletion within 30 days), list « Politique de conservation » (Nom, jours fichier source, jours version finale), list of all files (manual delete: never without explicit request).

## E-mail entrant (`/email`)
Nom, Activé, Protocole, Serveur, Port, Nom d'utilisateur, Mot de passe (**typed by the user only**), type d'authentification / Se connecter (OAuth), Ignorer les avertissements SSL, Tester la connexion, e-mail de l'expéditeur, Source, Types de fichier (PDF, JPEG, PNG, TIFF, DOC…), taille min / max (MB), nombre maximum de pièces jointes, Supprimer le mail après récupération. Save before connecting.

## Stockage cloud (`/cloud-storage`)
Nom, Application cloud (Google Drive / One Drive), Activé, Se connecter (OAuth by the user), Mappage des dossiers (each folder → print buyer: company, first name, last name, e-mail), « Uniquement les dossiers mappés ». Save before connecting.

## Renommer le fichier (`/rename`, single setting)
Templates: Nom du fichier de production, Nom du fichier original, Nom du rapport. Tokens: `%WORKFLOW_NAME%`, `%SESSION_ID%`, `%INJECTION_TYPE%`, `%USER%`, `%VAR_name%` (case-sensitive, e.g. `%VAR_Height%`), `%ORIGINAL_FILENAME%`, `%PRODUCTION_FILENAME%`.

## Paramètres (`/settings`)
Comportement de validation de l'épreuve (default), Unité de mesure par défaut, Culture (langue), Coût estimé de la main-d'œuvre prépresse (par heure), Temps moyen de traitement prépresse (dashboard savings).
