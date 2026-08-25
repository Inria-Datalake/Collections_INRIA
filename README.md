#  Collections Inria 
Tableaux de bord  interactifs pour les collections HAL d'Inria, déployés sur GitHub Pages.

> **URL :** https://inria-datalake.github.io/Collections_INRIA/
>
> **Données :** [api.archives-ouvertes.fr](https://api.archives-ouvertes.fr) (HAL — Archives Ouvertes)

---

##  Collections

| Collection | Code HAL | Dossier | Publications | Période |
|---|---|---|---|---|
| PEPR Santé Numérique | `PEPR_SANTENUM` | `pepr_santenum/` | 323 | 2023–2026 |
| Agroécologie et Numérique | `AGROECONUM` | `agroeconum/` | 268 | 2023–2026 |
| Interstices | `INTERSTICES` | `interstices/` | 656 | 2023–2026 |

---

##  Déploiement

Le site est servi via **GitHub Pages** depuis le dossier `/docs`.

1. Aller dans **Settings → Pages** du dépôt
2. Source : `Deploy from a branch`
3. Branch : `main` — dossier : `/docs`
4. Le site est en ligne sur `https://inria-datalake.github.io/Collections_INRIA/`

---

##  Structure du projet

```
Collections_INRIA/
├── docs/                          ← Dossier servi par GitHub Pages
│   ├── index.html                 ← Page d'accueil (KPIs dynamiques)
│   ├── 404.html                   ← Redirection HashRouter
│   │
│   ├── agroeconum/
│   │   ├── index.html              ← Page du dashboard
│   │   ├── banner.jpg              ← Logo/bannière
│   │   ├── 404.html
│   │   ├── assets/
│   │   │   ├── inria-agroeconum-app.js   ← Bundle React du dashboard
│   │   │   ├── inria-index.css          ← Styles compilés
│   │   │   ├── inria-index.es.js        ← Dépendances React/Vendor
│   │   │   ├── inria-html2canvas.esm.js  ← Capture d'écran (PDF)
│   │   │   ├── inria-jspdf.es.min.js     ← Génération PDF
│   │   │   └── inria-purify.es.js        ← Sanitization HTML
│   │   └── data/
│   │       └── publications.json        ← Données HAL (268 publications)
│   │
│   ├── interstices/
│   │   ├── index.html
│   │   ├── banner.jpg
│   │   ├── 404.html
│   │   ├── assets/
│   │   │   ├── inria-interstices-app.js
│   │   │   ├── inria-index.css
│   │   │   ├── inria-index.es.js
│   │   │   ├── inria-html2canvas.esm.js
│   │   │   ├── inria-jspdf.es.min.js
│   │   │   └── inria-purify.es.js
│   │   └── data/
│   │       └── publications.json        ← Données HAL (656 publications)
│   │
│   └── pepr_santenum/
│       ├── index.html
│       ├── banner.jpg
│       ├── 404.html
│       ├── assets/
│       │   ├── inria-pepr-santenum-app.js
│       │   ├── inria-index.css
│       │   ├── inria-index.es.js
│       │   ├── inria-html2canvas.esm.js
│       │   ├── inria-jspdf.es.min.js
│       │   └── inria-purify.es.js
│       └── data/
│           └── publications.json        ← Données HAL (323 publications)
│
├── prepare_data.ipynb             ← Notebook Jupyter de préparation des données
└── README.md                       ← Ce fichier
```

---

## 🔄 Mise à jour des données (prepare_data.ipynb)

Le notebook `prepare_data.ipynb` récupère les publications depuis l'API HAL et génère les fichiers `publications.json` pour chaque dashboard.

### Prérequis

```bash
pip install requests jupyter
```

### Utilisation

1. Ouvrir le notebook :

```bash
jupyter notebook prepare_data.ipynb
```

2. Exécuter toutes les cellules → les fichiers `docs/<collection>/data/publications.json` sont régénérés.

### Étapes du notebook

| Étape | Description |
|---|---|
| **1. Configuration** | Définition des 3 collections HAL et de leurs codes (`PEPR_SANTENUM`, `AGROECONUM`, `INTERSTICES`). Dictionnaire de correspondance pour les noms de villes. |
| **2. Requête API HAL** | Pour chaque collection, appel à `https://api.archives-ouvertes.fr/search/<collection>/` avec les champs : `halId_s`, `title_s`, `docType_s`, `publicationDateY_i`, `authFullName_s`, `labStructName_s`, `labStructAddress_s`, `labStructCountry_s`, `instStructName_s`, `fr_domainAllCodeLabel_fs`, `keyword_s`, `journalTitle_s`, `openAccess_bool`, `doiId_s`. |
| **3. Extraction de la ville** | Déduction de la ville à partir de l'adresse postale du laboratoire via un dictionnaire de correspondances + extraction par code postal. |
| **4. Normalisation** | Mise en forme normalisée : titre, auteurs, laboratoire, ville, pays, institution, domaines HAL, mots-clés, journal, langue, Open Access, DOI. |
| **5. Export** | Écriture du fichier `docs/<collection>/data/publications.json` au format JSON. |

### Format de sortie (publications.json)

```json
[
  {
    "halId": "hal-04XXXXXX",
    "title": "Titre de la publication",
    "docType": "ART",
    "year": 2024,
    "authors": ["Prénom Nom", "Prénom Nom"],
    "lab": "Nom du laboratoire",
    "labAddress": "Adresse postale",
    "city": "Sophia Antipolis",
    "country": "fr",
    "institution": "INRIA",
    "domains": ["Informatique [cs]"],
    "keywords": ["mot-clé 1", "mot-clé 2"],
    "journal": "Nom du journal",
    "language": "fr",
    "openAccess": true,
    "doi": "10.xxxx/xxxxx"
  }
]
```

### Enrichissement optionnel avec Mistral AI

Pour enrichir les villes manquantes via l'API Mistral AI, le notebook supporte la rotation de plusieurs clés API via les variables d'environnement :

```bash
export MISTRAL_API_KEY="votre_cle_1"
export MISTRAL_API_KEY_2="votre_cle_2"   # optionnel
export MISTRAL_API_KEY_3="votre_cle_3"   # optionnel
```

> ⚠️ **Note :** Sans clés Mistral, l'extraction de la ville utilise le dictionnaire intégré et l'analyse des codes postaux (couverture ~98%).

---

##  Fonctionnalités des dashboards

- **Vue d'ensemble** : KPIs (publications, pays, organismes, auteurs, laboratoires, co-auteurs)
- **Évolution temporelle** : graphique des publications par année
- **Top 10** : pays, villes, laboratoires, organismes
- **Carte géographique** : localisation des co-publications (Leaflet)
- **Réseau de co-autorat** : graphe interactif des collaborations
- **Filtres** : par année, laboratoire, pays, type de document
- **Export PDF** : capture d'écran du dashboard
- **Mode sombre / clair** : bascule de thème
- **Navigation sidebar** : entre les 3 collections

---

## 🛠️ Stack technique

| Composant | Technologie |
|---|---|
| Framework | React (build statique, sans backend) |
| Routing | HashRouter (compatible GitHub Pages) |
| Graphiques | Recharts |
| Cartes | Leaflet + CartoDB tiles |
| Export PDF | html2canvas + jsPDF |
| Données | JSON statique (pas d'API runtime) |
| Hébergement | GitHub Pages |

> **100% statique** — aucune authentification, aucun appel d'API au runtime. Les données sont pré-chargées dans les fichiers `publications.json`.

---

## 👥 Auteur

**Andréa NEBOT** — Data Analyst, INRIA

Inria DataLake · Données issues de HAL (archives-ouvertes.fr)

