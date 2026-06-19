# Dashboard Qualité de l'Air – Séoul

Ce projet applique le processus KDD (Knowledge Discovery in Databases) à un jeu de données multidimensionnel de qualité de l'air pour Séoul, afin d'en extraire des indicateurs et de les restituer dans un tableau de bord interactif.

## Projet académique

**École :** ESILV 
**Formation :** MSDS 1
**Matière :** Data Analysis 
**Projet :** Projet final (KDD & Dashboarding)  
**Professeur :**   ALATRISTA SALAS Hugo
**Année universitaire :** 2025-2026

## Équipe

- Emma Coco
- Uliana Chernysheva
- Adrien Formoso
- Joss Develter
- Ibrahim Alkardo

## Lien vers le Repo GitHub

https://github.com/adrien-formoso/final_project_data_analyse 

## Description du projet

Le notebook `main.ipynb` charge, nettoie et explore le jeu de données de pollution de l'air de Séoul, l'enrichit avec des données météorologiques externes, puis calcule une série d'indicateurs (clustering du niveau de risque, agrégations par station, tendance temporelle, analyse spatio-temporelle et corrélations entre variables météorologiques et polluants). Tous ces résultats sont ensuite affichés dans un dashboard interactif construit avec Dash et Plotly.

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

- `temperature.parquet` : historique météorologique de Séoul (température, humidité, précipitations, vent, etc.) utilisé pour enrichir le dataset principal.
- `seoul_2016-01-01_to_2018-01-01.csv` et `seoul_2018-01-01_to_2020-01-01.csv` : exports météorologiques bruts concaténés pour produire le fichier `temperature.parquet`.

Les données météorologiques sont fusionnées au dataset principal via la date de mesure. Les variables température (`temp`) et humidité (`humidity`) sont ensuite utilisées pour étudier les relations entre conditions météorologiques et niveaux de pollution atmosphérique à travers une analyse de corrélation.

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

1. Vérifier que `polution.csv` et `temperature.parquet` se trouvent dans le même dossier que le notebook.
2. Lancer Jupyter et exécuter toutes les cellules de `main.ipynb` :

```bash
jupyter notebook main.ipynb
```

3. À la fin de l'exécution, le dashboard Dash se lance automatiquement. Ouvrir le lien affiché dans la console (par défaut [http://127.0.0.1:8050/](http://127.0.0.1:8050/)) dans un navigateur.

## Étapes du pipeline (KDD)

1. **Chargement et exploration** (`load_and_explore_data`) : lecture du CSV, affichage des dimensions, types, valeurs manquantes et plages de valeurs.
2. **Enrichissement** (`enrich_with_weather`, `concat_historical_temp`) : fusion des données de pollution avec les données météo externes, par date.
3. **Construction des indicateurs** :
   - **Clustering K-Means** (`apply_kmeans_clustering`) : segmentation non supervisée des mesures en 3 groupes présentant des caractéristiques de pollution similaires, à partir des concentrations combinées de SO2, NO2, O3, CO, PM10 et PM2.5.
   - **Regroupement** (`analyze_pollution_by_station`) : moyenne des polluants par station.
   - **Analyse temporelle** (`temporal_analysis`) : moyenne journalière des PM10.
   - **Analyse spatio-temporelle** (`spatio_temporal_analysis`) : moyenne des polluants par coordonnées GPS et par jour, utilisée pour la carte animée.
   - **Corrélations météo-pollution** : matrice de corrélation entre température, humidité et concentrations des principaux polluants afin d'étudier l'influence des conditions météorologiques sur la qualité de l'air.
4. **Visualisation** (`run_interactive_dashboard`) : dashboard Dash regroupant carte spatio-temporelle animée, graphique en barres par station, série temporelle, heatmap de corrélation, boxplot et diagramme circulaire des niveaux de risque.

## Dashboard

Le tableau de bord présente :

- une carte interactive animée (polluant sélectionnable via menu déroulant) ;
- un graphique en barres de la moyenne des PM10 par station ;
- une courbe d'évolution journalière des PM10 ;
- une heatmap des corrélations entre variables météorologiques (température, humidité) et polluants ;
- un boxplot de la distribution des PM2.5 par niveau de risque ;
- un diagramme circulaire de répartition des niveaux de risque.
