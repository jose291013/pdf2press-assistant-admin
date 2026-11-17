# Verified JavaScript helpers (PDF2Press app, run in the app tab `{region}.ui.pdf2press.aleyant.com`)

Each `navigate` resets the page: redefine helpers in the same script that uses them.

## Get the id of a list row (edit / duplicate)
```js
// on /<route>?embed=true&lang=fr ; name = exact text of the « Nom » cell
const r = [...document.querySelectorAll('tbody tr')].find(r => r.cells[1]?.textContent.trim() === name);
setTimeout(() => r.querySelector('.ri-edit-box-line').closest('a,button').click(), 50); // then read location.pathname: /undefined/pdf2press/<type>/edit/<id>
// duplicate: r.querySelector('.ri-checkbox-multiple-blank-line').closest('button').click()  → toast « Le profil a été dupliqué avec succès ! », copy = name + 'Copy'
```

## Read / fill a profile (preflight, fix, proof…)
```js
const optCard = () => [...document.querySelectorAll('.v-card')].find(c => c.querySelector('.v-card-title,.v-card-item')?.textContent.trim() === 'Options');
const opt = () => { const card = optCard(); return [...card.querySelectorAll('.v-switch')].map(s => { let p = s, txt = ''; while (p && p !== card) { p = p.parentElement; txt = (p.innerText || '').trim(); if (txt) break; } return { label: txt.split('\n')[0], on: s.querySelector('input').checked, input: s.querySelector('input') }; }); };
const setOpts = async want => { for (const [l, v] of Object.entries(want)) { const o = opt().find(o => o.label === l); if (o.on !== v) { o.input.click(); await new Promise(x => setTimeout(x, 300)); } } };
const fld = (section, label, val) => { // section '' = whole page (Nom)
  const card = [...document.querySelectorAll('.v-card')].find(c => c.querySelector('.v-card-title,.v-card-item')?.textContent.trim() === section);
  const e = [...(card || document).querySelectorAll('.v-input')].find(e => e.querySelector('label')?.textContent.trim() === label);
  const i = e.querySelector('input');
  Object.getOwnPropertyDescriptor(HTMLInputElement.prototype, 'value').set.call(i, String(val));
  i.dispatchEvent(new Event('input', { bubbles: true })); i.dispatchEvent(new Event('change', { bubbles: true }));
};
const save = async () => { [...document.querySelectorAll('button')].find(b => b.innerText.trim() === 'Enregistrer').click(); await new Promise(x => setTimeout(x, 2500)); location.reload(); };
// example: preflight DL 225×110, exactly 1 page
await setOpts({'Intégrité des documents':false,"Résolution de l'image":false,'Polices':false,'Rich Black':false,'Distance de sécurité du texte':false,'Dimensions des pages':true,'Nombre de pages':true,'Orientation des pages':true});
fld('', 'Nom', 'P2P – PF – Enveloppe DL 225×110 1 page');
fld('Dimensions des pages', 'Largeur', 225); fld('Dimensions des pages', 'Hauteur', 110); fld('Dimensions des pages', 'Tolérance', 2);
fld('Nombre de pages', 'Minimum', 1); fld('Nombre de pages', 'Maximum', 1);
await save();
// after reload: read back with fld-like getters and the switch string [...optCard().querySelectorAll('.v-switch input')].map(i=>i.checked?1:0).join('')
```
Selects (Vuetify `v-select`: report « Avertir… / Échec… », unit, ICC profile) need real clicks: click the field, then the item in `.v-overlay--active .v-list-item`. Read: `.v-select__selection-text`.

## Workflow editor – drop a step (HTML5 drag and drop)
```js
// sx, sy = screenshot coordinates (viewport 1400×1000 → screenshot 800 wide)
window.__drop = async (label, sx, sy) => {
  const k = 1400 / 800, x = sx * k, y = sy * k;
  const src = [...document.querySelectorAll('.node-step-source')].find(e => e.innerText.trim() === label); // Injecter, Preflight, Corriger, Imposer, Condition, Épreuve, En attente, Conservation, Routeur, Action, Complet
  const dt = new DataTransfer(), r = src.getBoundingClientRect();
  src.dispatchEvent(new DragEvent('dragstart', { bubbles: true, cancelable: true, dataTransfer: dt, clientX: r.x + 5, clientY: r.y + 5 }));
  const tgt = document.elementFromPoint(x, y);
  for (const t of ['dragenter', 'dragover', 'drop']) tgt.dispatchEvent(new DragEvent(t, { bubbles: true, cancelable: true, dataTransfer: dt, clientX: x, clientY: y }));
  src.dispatchEvent(new DragEvent('dragend', { bubbles: true, cancelable: true, dataTransfer: dt, clientX: x, clientY: y }));
  await new Promise(z => setTimeout(z, 400));
  return document.querySelectorAll('.vue-flow__node').length;
};
```

## Workflow editor – nodes, handles, edges
```js
const name = {}; document.querySelectorAll('.vue-flow__node').forEach(n => name[n.dataset.id] = n.innerText.replace(/\s+/g, ' ').trim().slice(0, 40) + '#' + n.dataset.id.slice(0, 4));
[...document.querySelectorAll('.vue-flow__handle')].map(h => { const r = h.getBoundingClientRect();
  return name[h.getAttribute('data-nodeid')] + ' ' + (h.classList.contains('source') ? 'OUT' : 'IN') + ' ' + h.getAttribute('data-handleid').split('__').pop()
    + ' @' + Math.round((r.x + r.width / 2) * 800 / 1400) + ',' + Math.round((r.y + r.height / 2) * 800 / 1400); }).join('\n');
// edges: [...document.querySelectorAll('.vue-flow__edge')].map(e => e.getAttribute('data-id'))   // edge-<src>-<dst>
```
Then connect with `computer left_click_drag` from an OUT position to an IN position (+1 px down). The Vue internals (`__vueParentComponent`) are not exposed in production: do not rely on them.
