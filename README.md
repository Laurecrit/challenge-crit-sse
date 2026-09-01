# Challenge Smart Sourcing · Indeed × crit. — Mini-site classement

Site de classement hebdomadaire du challenge CVthèque Indeed, à la charte crit.
Il affiche **2 classements** (Agences national + Users Top 3/région) et le **barème**,
avec recherche, filtre par région, KPIs et rafraîchissement automatique.

## Fichiers
- `index.html` — la page (tout le code JS est dedans)
- `style.css` — le style (charte crit.)
- `data.json` — les données (mode démo, ou données de secours)
- `crit-logo-blanc.svg`, `crit-embleme.svg` — logos

---

## 1) Mise en ligne sur GitHub Pages (comme AURA)

1. Créer un dépôt GitHub (ex. `challenge-crit-sse`), **Public**.
2. Y déposer tous les fichiers de ce dossier (glisser-déposer via « Add file > Upload files »).
3. Onglet **Settings > Pages** : Source = branche `main`, dossier `/root`, Save.
4. Au bout d'1 min, l'URL s'affiche : `https://<ton-compte>.github.io/challenge-crit-sse/`

Le site marche immédiatement avec les données de démo de `data.json`.

---

## 2) Brancher le Google Sheet EN DIRECT (mise à jour auto)

Le site peut lire ton Sheet automatiquement, sans que tu touches au code chaque semaine.

**a. Publier les onglets en CSV**
Dans le Google Sheet : **Fichier > Partager > Publier sur le web**.
- Onglet « Agences » → format **CSV** → Publier → copier l'URL.
- Onglet « Individuel » → format **CSV** → Publier → copier l'URL.

**b. Coller les 2 URL dans `index.html`**
Tout en haut du `<script>`, bloc `CONFIG` :
```js
const CONFIG = {
  SHEET_AGENCES_CSV:    "https://docs.google.com/.../pub?gid=...&single=true&output=csv",
  SHEET_USERS_CSV: "https://docs.google.com/.../pub?gid=...&single=true&output=csv",
  REFRESH_MS: 60000
};
```
Enregistrer / re-uploader `index.html`. Le pastille passe à « Données à jour »
et le classement se recalcule tout seul (tri par points + rang).

**c. Structure attendue (celle de ton Excel V3)**
Le site lit les onglets **par position de colonne**, exactement comme ton fichier
`Challenge_CVtheque_Indeed_CRIT_V3.xlsx` :

`Rang · Nom · Région · CV · CT · RP · Score (réf. juin) · CV · CT · RP · Score (challenge) · Δ Score`

Il repère tout seul la ligne d'entête (celle qui contient « Nom » et « Région »),
ignore les lignes de titre du haut, et convertit les codes région
(EST, BPDL, ARCMED, NDIE, EC, SO, CVDL…) en libellés lisibles.

**Ce que le site calcule tout seul — rien à trier dans le Sheet :**
- le **classement national des agences** sur la progression (Δ Score) ;
- le **rang par région** et le **Top 3 par région** côté recruteurs ;
- le **Δ** si la colonne est vide (= Score challenge − Score juin) ;
- les KPIs et le total de progression.

> Tu peux donc garder **exactement** la mise en forme de ton Excel : publie
> les onglets « Agences » et « Users » en CSV, colle les 2 URL, c'est tout.

---

## 3) Mise à jour hebdo (30 secondes)
Si le Sheet est branché : **rien à faire**, le site se met à jour seul.
Sinon, éditer `data.json` (semaine, période, chiffres) et re-uploader le fichier.

Pense à mettre à jour dans `data.json` le bloc `meta` :
`semaine`, `periode`, `maj` (pour l'affichage bandeau + pied de page).

---

## Notes charte
Couleurs crit. (rouge #D50032, orange #FF6A14, gris foncé #333F48), Raleway + Open Sans.
Or/argent/bronze des médailles = usage podium (hors 4 couleurs, assumé).
Le rouge n'est jamais utilisé comme indicateur d'alerte.
