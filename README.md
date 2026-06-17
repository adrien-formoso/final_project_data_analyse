# Dashboard Qualité de l'Air – Séoul

Ce projet applique le processus KDD (Knowledge Discovery in Databases) à un jeu de données multidimensionnel de qualité de l'air pour Séoul, afin d'en extraire des indicateurs et de les restituer dans un tableau de bord interactif.

## Équipe

- Emma Coco
- Uliana Chernysheva
- Adrien Formoso
- Joss Develter
- Ibrahim 

## Description du projet

Le notebook `main.ipynb` charge, nettoie et explore le jeu de données de pollution de l'air de Séoul, l'enrichit avec des données météorologiques externes, puis calcule une série d'indicateurs (clustering du niveau de risque, agrégations par station, tendance temporelle, analyse spatio-temporelle, corrélations entre polluants). Tous ces résultats sont ensuite affichés dans un dashboard interactif construit avec Dash et Plotly.

## Jeux de données

### Pollution de l'air à Séoul (`polution.csv`)

Mesures horaires de qualité de l'air, avec une dimension temporelle (date et heure de mesure), une dimension spatiale (code de station, adresse, latitude, longitude) et une dimension analytique (concentrations de polluants).

| Colonne | Description |
|---|---|
| `Measurement date` | Date et heure de la mesure |
| `Station code` | Identifiant de la station de mesure |
| `Address` | Adresse de la station |
| `Latitude`, `Longitude` | Coordonnées GPS de la station |
| `SO2`, `NO2`, `O3`, `CO`, `PM10`, `PM2.5` | Concentrations des polluants mesurés |

### Données météo de Séoul (enrichissement, point bonus)

- `temperature.csv` / `temperature.parquet` : historique météo agrégé (température, humidité, précipitations, vent, etc.) utilisé pour enrichir les indicateurs de pollution.
- `seoul_2016-01-01_to_2018-01-01.csv` et `seoul_2018-01-01_to_2020-01-01.csv` : exports météo bruts par période, concaténés pour produire le fichier `temperature.parquet`.

## Structure du dépôt

```
.
├── main.ipynb                                  # Notebook principal (KDD + dashboard)
├── polution.csv                                 # Dataset principal : qualité de l'air à Séoul
├── temperature.csv                               # Données météo agrégées
├── temperature.parquet                           # Données météo agrégées (format parquet)
├── seoul_2016-01-01_to_2018-01-01.csv            # Données météo brutes (2016-2018)
├── seoul_2018-01-01_to_2020-01-01.csv            # Données météo brutes (2018-2020)
├── Projet_final_MACSIN4A2225.pdf                 # Sujet du projet
└── README.md
```

## Installation

### Prérequis

- Python 3.9+
- pip

### Dépendances

```bash
pip install pandas numpy plotly dash dash-bootstrap-components scikit-learn pyarrow
```

## Utilisation

1. Vérifier que `polution.csv` se trouve dans le même dossier que le notebook (le notebook le référence sous le nom `data.csv` dans le bloc `__main__` : adapter le chemin si nécessaire).
2. Lancer Jupyter et exécuter toutes les cellules de `main.ipynb` :

```bash
jupyter notebook main.ipynb
```

3. À la fin de l'exécution, le dashboard Dash se lance automatiquement. Ouvrir le lien affiché dans la console (par défaut [http://127.0.0.1:8050/](http://127.0.0.1:8050/)) dans un navigateur.

## Étapes du pipeline (KDD)

1. **Chargement et exploration** (`load_and_explore_data`) : lecture du CSV, affichage des dimensions, types, valeurs manquantes et plages de valeurs.
2. **Enrichissement** (`enrich_with_weather`, `concat_historical_temp`) : fusion des données de pollution avec les données météo externes, par date.
3. **Construction des indicateurs** :
   - **Clustering K-Means** (`apply_kmeans_clustering`) : segmentation en 3 niveaux de risque (Faible / Modéré / Élevé) à partir des concentrations combinées de polluants.
   - **Regroupement** (`analyze_pollution_by_station`) : moyenne des polluants par station.
   - **Analyse temporelle** (`temporal_analysis`) : moyenne journalière des PM10.
   - **Analyse spatio-temporelle** (`spatio_temporal_analysis`) : moyenne des polluants par coordonnées GPS et par jour, utilisée pour la carte animée.
   - **Corrélations** : matrice de corrélation entre polluants.
4. **Visualisation** (`run_interactive_dashboard`) : dashboard Dash regroupant carte spatio-temporelle animée, graphique en barres par station, série temporelle, heatmap de corrélation, boxplot et diagramme circulaire des niveaux de risque.

## Dashboard

Le tableau de bord présente :

- une carte interactive animée (polluant sélectionnable via menu déroulant) ;
- un graphique en barres de la moyenne des PM10 par station ;
- une courbe d'évolution journalière des PM10 ;
- une heatmap des corrélations entre polluants ;
- un boxplot de la distribution des PM2.5 par niveau de risque ;
- un diagramme circulaire de répartition des niveaux de risque.
