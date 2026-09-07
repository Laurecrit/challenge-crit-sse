# Challenge Smart Sourcing · Indeed × crit. — Mini-site classement

Site de classement hebdomadaire du challenge CVthèque Indeed, à la charte crit.
Il affiche **2 classements** (Agences national + Users par région), le **suivi des crédits
par région** et le **barème**, avec recherche, filtre par région, KPIs et rafraîchissement
automatique.

> **État au 05/09/2026 — semaine 1.** Le site tourne sur `data.json`, qui contient les
> **données réelles** de la semaine 1 (31/08 → 04/09), générées depuis
> `Challenge_CVtheque_CRIT_Classement_S1_Final2.xlsx` transmis par Indeed
> (version corrigée : la région **E&C** manquait dans le premier envoi).
> 11 régions, 234 agences, 325 licences individuelles.
> Les URL du Google Sheet sont en veille dans `index.html` (constantes
> `SHEET_*_CSV_ATTENTE`) : la publication CSV est bloquée par les restrictions de
> domaine Google Workspace. Dès que l'accès public est ouvert, il suffit de recopier
> ces 2 URL dans `CONFIG` pour repasser en direct.

## Fichiers
- `index.html` — la page (tout le code JS est dedans)
- `style.css` — le style (charte crit.)
- `data.json` — les données (classements, quotas crédits, KPIs, récompenses)
- `crit-logo-blanc.svg`, `crit-embleme.svg` — logos

---

## 1) Mise en ligne sur GitHub Pages (comme AURA)

1. Créer un dépôt GitHub (ex. `challenge-crit-sse`), **Public**.
2. Y déposer tous les fichiers de ce dossier (glisser-déposer via « Add file > Upload files »).
3. Onglet **Settings > Pages** : Source = branche `main`, dossier `/root`, Save.
4. Au bout d'1 min, l'URL s'affiche : `https://<ton-compte>.github.io/challenge-crit-sse/`

Le site marche immédiatement avec les données de `data.json`.

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
Dans `data.json`, le code `EC` de l'export est déjà converti en `E&C`.

**Ce que le site calcule tout seul — rien à trier dans le Sheet :**
- le **classement national des agences** sur la progression (Δ Score) ;
- le **rang par région** et le **Top 3 par région** côté recruteurs ;
- le **Δ** si la colonne est vide (= Score challenge − Score juin) ;
- les KPIs et le total de progression.

> Tu peux donc garder **exactement** la mise en forme de ton Excel : publie
> les onglets « Agences » et « Users » en CSV, colle les 2 URL, c'est tout.

---

## 3) Mise à jour hebdo
Si le Sheet est branché : **rien à faire**, le site se met à jour seul.
Sinon, remplacer `data.json` et re-uploader le fichier.

Bloc `meta` à mettre à jour chaque semaine :
`semaine`, `periode`, `maj`, `source`, et le tableau `kpis`
(4 valeurs reprises telles quelles de l'onglet **Synthèse** du fichier Indeed).

### Structure de `data.json`
| Clé | Contenu |
|---|---|
| `meta.kpis` | les 4 chiffres du bandeau, **repris de l'onglet Synthèse** (jamais recalculés) |
| `meta.quotas_note` | légende des seuils de statut crédits |
| `meta.recompenses` | les 3 cartes de l'onglet Règles |
| `quotas[]` | onglet **Quotas & conso**, une ligne par région + le total |
| `agences[]` | onglet **Agences** (234 lignes, rang national sur la progression) |
| `individuel[]` | onglet **Individuel** (325 lignes, rang par région) |

Chaque entrée porte `actif` (= score de la semaine > 0) : la case **« Actifs seulement »**
de la barre d'outils, cochée par défaut, masque les comptes à zéro. Décocher affiche
l'intégralité du parc, utile pour repérer les dormants.

Chaque entrée porte aussi `src`, le libellé brut de l'export Indeed
(`Clermontferrand · 640`), conservé pour audit : c'est `agence` / `nom` qui est affiché,
après normalisation typographique (`Clermont-Ferrand · 640`).

---

## Notes charte
Couleurs crit. (rouge #D50032, orange #FF6A14, gris foncé #333F48), Raleway + Open Sans.
Or/argent/bronze des médailles = usage podium (hors 4 couleurs, assumé).
Le rouge n'est jamais utilisé comme indicateur d'alerte : les statuts de l'onglet
Crédits sont portés par les pastilles 🔴 🟠 🟢 du fichier de suivi, sur un fond neutre.
