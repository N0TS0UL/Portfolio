# Weaver's Code — inspiration uniquement

> ⚠️ **Ce style ne doit être appliqué à aucune page.** Ces fichiers sont une
> référence visuelle / d'interaction, pas une feuille de style à importer.
> Ne pas lier `support.js`, `image-slot.js` ni le `.dc.html` depuis le site.

Source : projet Claude Design `e2abc0cb-61f6-47a3-9d2f-b3d4dfe75997`
(`Weaver's Code - Portfolio.dc.html`, importé le 2026-09-28).

## Fichiers

| Fichier | Rôle |
|---|---|
| `Weaver's Code - Portfolio.dc.html` | Maquette complète (template `<x-dc>` + logique `DCLogic`), en classes |
| `weavers-code.scss` | Styles extraits, un bloc BEM par composant — source |
| `weavers-code.css` | Compilé : `npx sass --no-source-map weavers-code.scss weavers-code.css` |
| `Weaver's Code - Portfolio.inline.dc.html` | Original importé, styles inline (sauvegarde) |
| `support.js` | Runtime Claude Design (dc-runtime, React requis) — non réutilisable tel quel |
| `image-slot.js` | Web component `<image-slot>` (placeholder image drag & drop) — spécifique à l'éditeur |

## Concept

Portfolio narratif « chronique oubliée » : un tisseur de code s'éveille.
5 chapitres, chacun débloque une couleur ; à la fin, la palette est « restaurée ».

1. **I · L'Éveil** — hero, boot terminal tapé, portrait ASCII ↔ photo
2. **II · Les Fragments** — projets « scellés » qu'on compile au clic
3. **III · L'Arsenal** — compétences en index `tree`, leaders pointillés
4. **IV · Les Échos** — timeline (réalisations)
5. **V · Le Lien** — contacts, script `connect.sh`

## Idées à retenir

- **Couleur par chapitre** : variable CSS `--acc` posée sur la section quand
  elle devient visible (IntersectionObserver, ratio > .25). Tout le reste
  (`color: var(--acc, fallback)`) transitionne en 1s.
- **Rail de chapitres fixe** à gauche (84px) : chiffres romains + label
  vertical (`writing-mode: vertical-rl`), pastilles de palette en bas.
- **Effet frappe** : `type(el, text, speed)` — boot sequence, code des fragments.
- **Fragment scellé → compilé** : état scellé → code tapé avec spinner →
  fondu vers la carte finale (image, description, tags, liens).
- **Fil de tissage** : SVG vertical dont `stroke-dashoffset` avance à chaque
  fragment ouvert ; le nœud du fragment s'allume.
- **Arsenal** : lignes qui apparaissent une à une, leaders pointillés en
  `scaleX(0 → 1)`, niveaux en mots (« expert », « réflexe »…) plutôt qu'en %.
- **Overlays** : scanlines animées (`flick`) + vignette radiale, `pointer-events: none`.
- **Coins HUD** : 4 équerres 16px en `--acc` autour du portrait.
- **Reveal au scroll** : `opacity 0 + translateY(18px)` → visible, délai via `data-delay`.
- **Toggle ASCII/photo** mémorisé en cookie.

## Tokens relevés

**Polices** : Cinzel (chiffres romains), Cormorant Garamond italique
(titres, prose), JetBrains Mono (UI, code).

**Fonds** : `#040507` (page), `#06090e` / `#070a0f` (cartes).
**Texte** : `#eaeef2` (titres), `#c2c8ce`, `#9aa1a8` (prose), `#6b7178`, `#5b626b`, `#454c54`, `#3a4047` (atténué).
**Bordures** : `rgba(120,150,190,.12–.24)`.

**Palette des chapitres** :

| Chapitre | Couleur |
|---|---|
| I | `#4fa9ff` bleu |
| II | `#6fe0d0` turquoise |
| III | `#a98bff` violet |
| IV | `#ff5d6c` rouge |
| V | `#f5b65a` ambre |

**Échelle typo** : h1 98px · h2 68–72px · h3 26–32px · prose 18–22px · UI 8.5–13px
(labels en capitales, `letter-spacing` .14–.42em).

## Limites à noter si on s'en inspire

- Aucun responsive (grilles fixes 206px / 220px / 400px, marge 84px).
- Tailles UI très petites (8.5–9px) : lisibilité / accessibilité faibles.
- Pas de `prefers-reduced-motion`.
- Contenu (projets, hackathon, contacts) = texte fictif de maquette.
