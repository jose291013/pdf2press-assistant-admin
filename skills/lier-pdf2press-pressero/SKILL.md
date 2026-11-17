---
name: lier-pdf2press-pressero
description: Links PDF2Press Core to Pressero — assigns a PDF2Press workflow to a Pressero product (Upload tab, "use PDF2Press", workflow column), checks the site's PDF2Press settings (proofing at checkout, proof approval), and follows the resulting sessions. Use when the user says "assigne le flux PDF2Press au produit …", "active PDF2Press sur le produit", "le BAT ne s'affiche pas au panier", "asigna el flujo PDF2Press al producto", "assign the PDF2Press workflow to the Pressero product".
---

# Link PDF2Press to Pressero products

Load skill `pdf2press-expert` first, and the Pressero plugin's skill `pressero-expert` (plugin `pressero-assistant-admin`) for the product edit page (one Save button for the whole page, Kendo widgets).

## Assign a workflow to a product
1. Pressero admin → site → Produits → edit the product.
2. Tab **Téléchargement** (upload fields): tick **« utiliser PDF2Press »** (« Use PDF2Press Workflow Proofing »): a **third column** appears → choose the PDF2Press workflow (prefix `P2P – FLUX – …` or `TEST-Claude – FLUX – …`).
3. Save the product page (« Sauvegarder »), reload, check the box and the workflow are kept.
4. Product import / export column: « Use PDF2Press Workflow Proofing » (True / False).
Before saving on a customer site: show the change and get the go-ahead.

## Site settings (Paramètres → tab PDF2Press, `#awe`)
- `ProofingStep` – Autoriser l'épreuvage pendant le paiement.
- `Pdf2PressRequireCheckoutApprove` – Exiger l'approbation de l'épreuve au paiement.
- `Pdf2PressAllowAproveAllProofListPa…` – Autoriser « tout approuver » sur la liste des BAT.
If Pressero loses the connection with PDF2Press it falls back to e-mail proofing.

## Variables sent to the workflow
Pressero sends the product's question answers; workflow variables must carry the **exact** question label (PJM prompt) or PJM attribute key. If a branch never matches, compare names and option texts (accents, spaces, case) and adjust the workflow.

## Follow a test order
- PDF2Press → Sessions de travail (`/workflows/sessions`): file, status (En file d'attente, En traitement, En attente, Complété, Complété avec des avertissements, Échoué, Annulé, Redirigé), injection (Pressero, QuoteOrchestrator, Replaced File), workflow, session id.
- Storefront: the proof page after upload / at checkout; approve or replace the file.

## Status (v0.1.0)
Procedure given by the user (14/11/2026); the assignment screen itself has not been driven by Claude yet: read it first (screenshot + DOM) the first time and update this skill with the exact field ids.
