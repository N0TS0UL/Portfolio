# Oryzo — inspiration technique

Source : https://oryzo.ai/ (analysé le 2026-10-03 : HTML, CSS `_astro/index.*.css`, bundle `_astro/hoisted.*.js`).
Ce qui est retenu : **comment** le scroll pilote les animations et la 3D, pas le style visuel.

## Ce que le site utilise

| Techno | Preuve dans le code | Pourquoi ils l'utilisent |
|---|---|---|
| Astro | dossier `/_astro/` | Site statique : HTML pré-généré → chargement rapide, JS seulement où il faut. |
| `position: sticky` | `#sustainability-hero { height: 500vh }` + `-inner { position: sticky; top: 0; height: 100vh }` | Bloquer l'écran pendant que le scroll avance. Le navigateur gère le blocage, le JS ne calcule qu'une progression 0→1. |
| Three.js | `WebGLRenderer`, `window.__THREE__` | Un `<canvas>` 3D fixe derrière le HTML, piloté par le scroll. |
| Textures KTX2 / Basis | `Basis`, `ktx2` | Textures compressées **sur le GPU** : moins de mémoire, chargement plus rapide qu'un PNG. |
| GSAP + SplitText | `gsap`, `SplitText` | Découper les titres en lignes/mots pour les animer un par un. |
| Scroll lissé maison | `scrollPixel`, `targetScrollPixel`, `velocityPixel` | Lisser le scroll → animations fluides même avec une molette à crans. |
| Vidéo pilotée par le scroll | classe `RafVideo`, `.webp` → `.mp4` | Rejouer un rendu pré-calculé au lieu de calculer la 3D en direct. |

## Le mécanisme « bloqué → scroll normal »

```
section (400vh)          ← sa hauteur = durée de l'animation
└─ inner sticky (100vh)  ← reste collé en haut pendant 300vh
section suivante         ← scroll normal reprend
```

```css
.pin        { height: 400vh; position: relative; }
.pin__inner { position: sticky; top: 0; height: 100vh; overflow: hidden; }
```

```js
const r = pin.getBoundingClientRect();
const p = Math.min(Math.max(-r.top / (r.height - innerHeight), 0), 1); // 0 → 1
```

`p` pilote tout : image de séquence, `currentTime` vidéo, caméra 3D, opacité.

## Technique Apple : séquence d'images au scroll

Utilisée par Apple sur les pages produit (AirPods Pro, MacBook…). Pas de 3D dans le navigateur : on **rejoue un rendu 3D pré-calculé**, image par image, selon le scroll.

**Pourquoi** :

- Qualité de rendu maximale (Cycles, ombres, reflets) impossible à calculer en direct sur mobile.
- Coût GPU ≈ 0 : le navigateur dessine juste une image dans un canvas.
- Aller-retour parfait : chaque position de scroll = une image précise (une vidéo saccade en arrière).
- Fiable partout, iOS compris.

**Limites** : poids (2–8 Mo), caméra figée (pas d'interaction souris), résolution fixe.

### Pipeline

1. **Blender** : animer l'objet/caméra sur ~60–150 frames, rendu PNG.
2. **Conversion** : WebP ou AVIF, 1280–1920 px max, qualité ~75.
   `for f in *.png; do cwebp -q 75 -resize 1600 0 "$f" -o "${f%.png}.webp"; done`
3. **Option** : 2 jeux (mobile 800 px / desktop 1600 px) choisis via `matchMedia`.
4. **Section sticky** (voir plus haut) + canvas plein écran dans l'inner.

### Code

```js
const N = 120;
const canvas = document.querySelector('.pin canvas');
const ctx = canvas.getContext('2d');
const imgs = [];

// Préchargement : avant que la section n'arrive à l'écran
for (let i = 0; i < N; i++) {
  const im = new Image();
  im.src = `/seq/${String(i).padStart(4, '0')}.webp`;
  imgs.push(im);
}

let last = -1;
function draw() {
  const r = pin.getBoundingClientRect();
  const p = Math.min(Math.max(-r.top / (r.height - innerHeight), 0), 1);
  const f = Math.round(p * (N - 1));
  if (f !== last && imgs[f].complete) {          // ne redessine que si l'image change
    ctx.drawImage(imgs[f], 0, 0, canvas.width, canvas.height);
    last = f;
  }
  requestAnimationFrame(draw);
}
imgs[0].onload = () => draw();
```

Avec GSAP : `gsap.to(state, { frame: N - 1, snap: 'frame', ease: 'none', scrollTrigger: { trigger: '.pin', start: 'top top', end: 'bottom bottom', scrub: true }, onUpdate: render })`.

**Pour le portfolio** : intro de l'éclat d'âme (A : explosion → galaxie, B : éclat qui se vide). Le texte/code du sortilège reste en HTML par-dessus le canvas → net et accessible.

## Technos utilisables pour le portfolio

| Techno | Pourquoi | Pour quelle idée (`idea.md`) | Coût |
|---|---|---|---|
| **`position: sticky`** | Zéro JS pour le blocage, pas de saut au chargement, marche partout. | Toutes les scènes « bloquées » (intro éclat, Mémoires, Liens). | Aucun |
| **GSAP + ScrollTrigger** | `scrub` lie une timeline au scroll en une ligne ; gère resize et mobile mieux qu'un calcul maison. Gratuit. | Fil SVG, typewriter, fragments scellés → compilés. | ~70 kB |
| **Lenis** | Scroll lissé prêt à l'emploi (remplace leur scroll maison), compatible ScrollTrigger. | Fluidité globale. | ~3 kB |
| **Séquence d'images WebP/AVIF** (canvas 2D) | Qualité de rendu Blender, coût GPU ≈ 0, aller-retour parfait au scroll, fiable sur iOS. | Intro A (éclat qui explose), intro B (éclat qui se vide). | 2–8 Mo |
| **Vidéo scrubbée** (`currentTime = p * duration`) | 5 à 10× plus léger qu'une séquence d'images. Encoder en `-g 1` sinon le retour arrière saccade. | Même usage si la séquence est trop lourde. | ~1–3 Mo |
| **Three.js** | Seul choix si interaction (souris, sélection) ou si le fond doit vivre en continu. | Galaxie / ciel étoilé en fond, Mémoires flottantes cliquables. | ~150 kB + modèles |
| **Éclairage précalculé (bake Blender)** + `MeshBasicMaterial` | Ombres et lumière déjà dans la texture → aucun calcul de lumière en direct. | Éclat d'âme en 3D temps réel. | Temps de bake |
| **GLB + Draco/Meshopt + KTX2** | Modèle et textures 3–10× plus légers ; c'est ce que fait Oryzo. | Tout modèle Three.js. | Export Blender |
| **`AnimationMixer.setTime(p * duration)`** | Une animation faite dans Blender devient pilotée par le scroll, sans la recoder. | Éclat qui explose, fils qui se tissent. | Faible |
| **Image + carte de profondeur** (shader) | Fausse 3D à partir d'une seule image : parallaxe au scroll/souris. | Portrait, ambiances des Échos. | Faible |
| **CSS 3D** (`translateZ`, `rotateY`) | 3D sans WebGL : texte net, accessible, cliquable. | Mémoires autour d'un centre. | Aucun |
| **Vite** | Nécessaire pour importer GSAP / Three.js proprement (npm, modules). | Dès l'étape Three.js. | Outil dev |

### Inutile ici

- **Astro** : un seul `index.html`, pas de pages multiples → Vite suffit.
- **SplitText** : un typewriter maison couvre le besoin (style terminal, pas de découpage en lignes).
- **Scroll maison** : Lenis fait la même chose, testé.

## Choix conseillé

1. `sticky` + ScrollTrigger (`scrub`) + Lenis → base de toutes les scènes.
2. Intro éclat = **séquence d'images** rendue dans Blender → effet « Apple », coût GPU nul.
3. Fond galaxie = **Three.js** (Points), un seul canvas fixe, rendu seulement si `p` change, `setPixelRatio(min(dpr, 1.5))`.
4. 3D temps réel uniquement là où on interagit (Mémoires), avec éclairage précalculé.

## Pièges

- `100vh` sur mobile bouge avec la barre d'adresse → utiliser `100svh` (Oryzo calcule une variable `--vh` en JS pour la même raison).
- Une section sticky ne marche pas si un parent a `overflow: hidden`.
- Prévoir `prefers-reduced-motion` : afficher l'image finale au lieu de l'animation.
- Précharger la séquence d'images **avant** que la section n'arrive à l'écran, sinon images vides au scroll rapide.
