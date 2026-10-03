# Idées — Portfolio 2026-2027

Fusion des notes de `idea.md` (Idées + Refonte) et des commentaires de `index.html`.
Évaluation de faisabilité en fin de fichier.

---

## Fil rouge (concept global)

- Un **fil** tisse l'histoire : le portfolio se déroule dans la trame du destin.
- Le fil est contrôlé par du **code** qui représente le Weaver, caché dans l'ombre, qui manipule le destin.
- Thème et couleurs tirés du Weaver's Code (voir `../inspiration/weavers-code/NOTES.md` : une couleur par chapitre, polices Cinzel / Cormorant / JetBrains Mono).
- Apparition des images réelles / icônes : le fil tourne en rond comme un serpent qui se referme, puis forme l'image petit à petit.
- Le **fil du visiteur** : un nouveau fil apparaît (au début, ou après la première animation). Il est révélé au moment du Lien, où le visiteur tisse un nouveau nœud dans la trame.

### Refonte : interface semi-3D

Passer d'une page 2D à une interface **semi-3D** : contenu lisible au premier plan, profondeur (ciel étoilé, galaxie, fils, fragments flottants) en arrière-plan.

---

## I · L'Éveil

#### Texte / terminal

- Le texte est écrit dans le style d'une fenêtre créée par le Sortilège du Cauchemar.
- Le texte s'écrit au fur et à mesure, comme tissé dans la trame de la réalité (boot `./awaken.sh`).
- Le code de boot est à revoir avec le contexte global ; la liste « BUT Informatique / France » pourrait disparaître et être remplacée par le code.

#### Portrait

- Mon visage en ASCII, écrit au fur et à mesure.
- Pouvoir sélectionner le **Réel**, qui déchire la réalité comme une Porte des Rêves → la vraie photo apparaît.

#### Carte : animation de la bordure en dégradé (JS)

1. Départ en haut à gauche : la bordure blanche se remplit progressivement jusqu'en bas à droite.
2. Ensuite, un dégradé noir s'agrandit depuis le haut à droite et le bas à gauche, en augmentant son pourcentage.

Pistes :

- Piloter les pourcentages via des variables CSS (`--start`, `--end`…) mises à jour en JS (`requestAnimationFrame` ou GSAP).
- Éventuellement `@property` pour animer les variables en CSS pur.
- Base déjà en place : `.card::before` dans `style.scss` (les `15%` à animer).

#### Intro « éclat d'âme » (refonte, 2 variantes)

- **A — Explosion** : l'éclat d'âme explose en étincelles ; derrière, une galaxie avec plusieurs morceaux de code qui représentent le sortilège et les statistiques / ma présentation.
- **B — Épuisement** : l'éclat se vide petit à petit de sa lumière, des gouttes tombent ; dans la flaque d'essence de l'âme, le reflet du ciel étoilé, avec des morceaux de code qui s'écrivent çà et là (le sortilège, la fenêtre de stats).

---

## II · Fragments (projets)

- L'idée des fragments reste la même : des modules en plusieurs fragments, recousus ensemble par le fil rouge.
- Chaque module contient un projet différent (texte, image, lien GitHub).
- Inspiration Weaver's Code : fragment « scellé » → code tapé avec spinner → fondu vers la carte finale ; le fil avance et le nœud du fragment s'allume.

---

## III · Mémoires (compétences / Arsenal)

- Représente les skills.
- La reconstruction des fragments du chapitre précédent rend ce chapitre « complet », sur le même principe que Sunny devient complet après avoir rassemblé la lignée du Démon du Destin. Le fil relie donc les chapitres I et III en absorbant le II : les projets entraînent des skills.
- Chaque skill peut être agrandi pour voir quel fragment lui est lié (lien / réapparition du fragment).
- **Refonte** : comme les souvenirs qui flottent dans l'âme d'un rêveur, les mémoires flottent autour d'un centre et sont sélectionnables.

---

## IV · Échos (timeline / destin)

- Réalisations majeures : diplôme, projet réalisé, etc.
- Timeline classique, mais chaque point suit le fil qui tisse mon histoire.
- Comme je suis encore en vie, le fil n'a pas de fin ; et comme le destin a été défait, le fil pend dans le vide en attendant la suite.
- **Refonte** : un « rêve » / cauchemar par projet ; à chaque changement, un tissage entre les rêves fait naître le rêve suivant.

---

## V · Liens (contacts / épilogue)

- Épilogue : révéler la réalité tout en servant le but premier, rediriger vers mes pages (GitHub, LinkedIn, mail, CV).
- Les liens sont affichés en premier.
- Ensuite, une citation du Weaver qui nous appelle son épigone : en suivant le portfolio, on n'a fait que suivre ses pas (à revoir).
- Le fil du visiteur se lie au fil du Weaver (moi), coupé au chapitre précédent ; le Weaver se rend compte d'une nouvelle présence / d'un nouveau lien.
- **Refonte** : en arrière-plan, un entremêlement de fils avec le nouveau fil du visiteur ; au premier plan, du code qui se compile et représente l'attention du sortilège sur le nouveau venu.

---

## Faisabilité

Contexte : peu d'expérience web pour le moment. Stack actuelle : HTML + SCSS, pas de JS ni de bundler.

### Verdict global

**Faisable, à condition de découper en couches** et de ne pas tout faire en 3D.
Le risque principal n'est pas technique mais le **périmètre** : chaque chapitre a une idée « signature », et la moitié d'entre elles sont des projets à part entière. Il faut viser un site complet et lisible en 2D d'abord, puis ajouter la 3D par-dessus.

Architecture conseillée pour le « semi-3D » :
- **Un seul `<canvas>` WebGL fixe en fond** (ciel étoilé / galaxie / fils), dont la caméra bouge avec le scroll.
- **Le contenu reste en HTML** au-dessus (texte net, accessible, référencé, facile à modifier).
- Une scène, pas une par chapitre : on change couleur, brouillard, particules selon le chapitre actif.

### Outils

| Outil | Rôle | Difficulté | Avis |
|---|---|---|---|
| **GSAP + ScrollTrigger** | Timelines, animations au scroll, typewriter, SVG | Facile | Indispensable. Gratuit (plugins compris). Premier outil à apprendre. |
| **SVG + `stroke-dashoffset`** | Fils 2D qui se dessinent | Facile | Couvre 80 % des effets « fil » sans 3D. |
| **Vite** | Serveur de dev + imports npm | Facile | Nécessaire dès qu'on utilise Three.js / GSAP proprement. |
| **Three.js** | Moteur 3D (particules, galaxie, fils en tube) | Moyen | La référence. Beaucoup de tutos (galaxie, particules). |
| **Spline** | Éditeur 3D visuel, export web | Facile | Bon pour un objet (l'éclat d'âme) sans coder. Limité pour particules / logique fine, fichiers lourds. |
| **Blender** | Modéliser l'éclat, export `.glb` | Moyen | Seulement si Spline ne suffit pas. Courbe d'apprentissage à part. |
| React Three Fiber + drei | Three.js en React | Moyen+ | À éviter pour l'instant : ajoute React à apprendre en plus. |
| Lenis | Scroll fluide | Facile | Optionnel, s'intègre avec GSAP. |
| Shaders GLSL | Reflets, dissolution, eau | Difficile | Réserver aux effets finaux. |

Ressource recommandée : cours **Three.js Journey** (Bruno Simon), il couvre exactement galaxie, particules, scroll-based animation.

### Idée par idée

| Idée | Technique | Difficulté | Remarque |
|---|---|---|---|
| Couleur par chapitre | Variable `--acc` + IntersectionObserver | 🟢 Facile | Déjà préparé dans le SCSS. |
| Bordure en dégradé animée | `@property` ou GSAP sur variables CSS | 🟢 Facile | Bon premier exercice JS. |
| Texte / code tapé | Typewriter JS ou GSAP TextPlugin | 🟢 Facile | Réutilisable partout (Éveil, Fragments, Liens). |
| Fragment scellé → compilé | DOM + classes + GSAP | 🟢 Facile | |
| Skill → fragment lié | DOM, `<details>` ou modale | 🟢 Facile | |
| Fil qui relie les chapitres | SVG path + ScrollTrigger | 🟢/🟡 | En 3D (TubeGeometry) : moyen. |
| Fil qui pend dans le vide | SVG courbe animée | 🟢 Facile | Physique de corde (Verlet) : moyen, optionnel. |
| Portrait ASCII écrit progressivement | ASCII généré hors-ligne + typewriter | 🟢 Facile | Convertisseur en ligne / `ascii-image-converter`. |
| Déchirure « Porte des Rêves » vers la photo | `clip-path` / masque animé | 🟡 Moyen | Version simple : fondu + bord déchiré en SVG. |
| Mémoires flottant autour d'un centre | CSS 3D (`rotateY` + `translateZ`) ou Three.js + raycast | 🟡 Moyen | **Faisable sans WebGL** en CSS 3D : bon compromis. |
| Fil qui s'enroule et forme l'image | Particules qui prennent la forme d'une image (échantillonnage de pixels) | 🟡/🔴 | Particules → image : moyen (tutos). Un vrai fil unique : difficile. |
| **Intro A : éclat qui explose + galaxie** | Three.js (Points) + code en HTML par-dessus | 🟡 Moyen | **Recommandée.** Galaxie = tuto classique. |
| Intro B : éclat qui se vide, gouttes, flaque qui reflète | Reflector / shader d'ondes + particules | 🔴 Difficile | Beaucoup de shader. À garder pour plus tard. |
| Liens : entremêlement de fils + code qui compile | Lignes animées (bruit) canvas/SVG + typewriter | 🟡 Moyen | |
| Échos : un rêve / cauchemar par projet | Une ambiance par écho (couleur, brouillard, particules) + transition | 🔴 Difficile | Coûteux en contenu. Réduire : même scène, ambiance qui change. |

### Points d'attention

- **Recruteurs pressés** : nom, formation, projets et contact doivent être accessibles en quelques secondes. Intro courte + bouton « passer ».
- **Mobile / performance** : limiter le nombre de particules, baisser la qualité sur petit écran, prévoir un repli si WebGL indisponible.
- **Accessibilité** : respecter `prefers-reduced-motion` (animations coupées ou réduites), texte réel en HTML (pas dans le canvas), tailles de police lisibles (le Weaver's Code descend à 8,5 px).
- **Responsive** : la maquette d'inspiration n'en a aucun, à prévoir dès le début.

### Plan conseillé

1. **Bases** : site complet en HTML/SCSS, 5 chapitres avec le vrai contenu, responsive. Aucune animation. → le site est déjà utilisable.
2. **JS simple** : couleur par chapitre, bordure animée, typewriter, fragments scellés → compilés, reveal au scroll.
3. **GSAP + SVG** : le fil qui relie les chapitres, le fil qui pend, les mémoires en CSS 3D.
4. **Vite + Three.js** : canvas de fond unique (ciel étoilé / galaxie), caméra pilotée par le scroll, intro A.
5. **Bonus** : fil → image, déchirure Porte des Rêves, ambiances des Échos, puis éventuellement intro B.

Chaque étape donne un site publiable : on peut s'arrêter à n'importe laquelle.
