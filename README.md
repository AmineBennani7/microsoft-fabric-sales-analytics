# Microsoft Fabric Sales Analytics

[🇬🇧 English](#english) | [🇫🇷 Français](#français)

---

## Quick Links
- **Star schema:** [Data Model](#data-model-star-schema)
- **Pipeline (Fabric):** [Data Pipeline](#data-pipeline-microsoft-fabric)
- **Power BI dashboards:** [Power BI Reports](#power-bi-reports)

---

# English

## Project Overview
This repository showcases an **end-to-end analytics solution built with Microsoft Fabric**. The project simulates an e-commerce sales analytics platform where **daily CSV files** are ingested into a Lakehouse, transformed using **Apache Spark**, stored as **Delta tables**, modeled using a **Star Schema**, and finally visualized in **Power BI**.

**Main goals**
- Build a modern BI pipeline using Microsoft Fabric (OneLake + Lakehouse + Spark + Pipelines)
- Model clean analytical tables (Fact + Dimensions) for reporting
- Deliver interactive dashboards with actionable KPIs

---

## Repository Structure
- `architecture/` → data model screenshots (Star Schema)
- `data/sample/` → small sample CSV file(s) for demonstration
- `fabric-pipeline/screenshots/` → pipeline screenshots (orchestration + runs)
- `notebooks/` → Spark notebooks (transformation + validation)
- `sql/` → warehouse / staging / SCD scripts
- `powerbi/screenshots/` → report screenshots

---

## Architecture
**Flow:** Daily CSV → Lakehouse (OneLake) → Spark transformations (Delta) → Star Schema → Power BI

## Medallion Architecture (Bronze / Silver / Gold)

This project follows a **Medallion Architecture** pattern to structure the analytics lifecycle:

**Raw CSV (daily sales files)**
→ **Bronze (Lakehouse - Raw)**
→ **Silver (Lakehouse - Clean/Curated)**
→ **Gold (Warehouse - Star Schema)**
→ Semantic Model
→ Power BI

### Bronze (Raw)
**Purpose:** ingest data exactly as received (append-only), preserve lineage and enable reprocessing.  
**Where in this repo / Fabric:** Lakehouse raw zone (e.g., `raw/orders/`) containing daily CSV files such as:
- `data/sample/orders_2026-01-01.csv`

Typical table / folder:
- `sales_orders_raw` (raw ingestion)

### Silver (Clean / Curated)
**Purpose:** clean and standardize the raw data into a consistent, analytics-ready format.  
Includes:
- schema enforcement and type casting (dates, numeric fields)
- basic data quality checks (nulls, duplicates, invalid rows)
- standardized columns and business logic preparation

Typical table:
- `sales_orders_clean` (cleaned Delta table)

### Gold (Business-ready)
**Purpose:** deliver business-friendly tables optimized for BI and analytics.  
In this project, the **Gold layer is the Warehouse modeled as a Star Schema**:
- `FactSales`
- `DimProduct`
- `DimDate`
- `DimChannel`

---

## Data Model (Star Schema)
The warehouse follows a **Star Schema** design:
- **Fact table:** `FactSales` (transactions / measures)
- **Dimensions:** `DimDate`, `DimProduct`, `DimChannel`

![Star Schema](architecture/star_schema.png)

---

## Data Pipeline (Microsoft Fabric)
The pipeline orchestrates the processing workflow:
1. **Get Metadata**: checks whether new daily CSV files exist
2. **Condition**: stops cleanly if no files are found
3. **Set Variable**: stores the execution timestamp for traceability
4. **Notebook**: runs Spark transformations (raw → curated Delta tables)
5. **Script**: optional SQL step (e.g., SCD merge / staging)
6. **Notebook**: runs validation checks
7. **Web activity**: sends a completion notification

### Pipeline canvas
![Pipeline Canvas](fabric-pipeline/screenshots/pipeline_canvas.png)

### Example run (success)
![Pipeline Run Success](fabric-pipeline/screenshots/pipeline_run_success.png)

### Example SQL/SCD script step
![Pipeline SCD Script](fabric-pipeline/screenshots/pipeline_scd_script.png)

---

## SQL Layer (Warehouse + SCD)
The `sql/` folder contains SQL scripts used to:
- reset / create staging tables
- implement merge logic (e.g., **SCD Type 1**)
- run demo / validation queries

This demonstrates practical data warehousing patterns (staging, merges, analytical modeling).

---

## Power BI Reports
The `powerbi/screenshots/` folder contains the final dashboards built on top of the Star Schema.

### Executive overview
![Executive Overview](powerbi/screenshots/Executive_overview.png)

### Channel & category performance
![Channel Product Performance](powerbi/screenshots/Channel_product_performance.png)

### Sales explorer (transaction/detail view)
![Sales Explorer](powerbi/screenshots/Sales_explorer.png)

### Time analysis
![Time Analysis](powerbi/screenshots/Time_analysis.png)

---

## KPIs Included
Examples of metrics available in the dashboards:
- **Total Revenue (£)**
- **Total Orders**
- **Units Sold**
- **Average Order Value (£)**
- Revenue & orders by **channel**, **product category**, and **time**

---

## Tech Stack
- **Microsoft Fabric** (OneLake, Lakehouse, Pipelines)
- **Apache Spark / PySpark** (transformations)
- **Delta Lake** (curated tables)
- **SQL** (warehouse / staging / merges)
- **Power BI** (dashboards)

---

# Français

## Présentation du projet
Ce dépôt présente une **solution analytique de bout en bout développée avec Microsoft Fabric**. Le projet simule un cas e-commerce où des **fichiers CSV quotidiens** sont ingérés dans un Lakehouse, transformés avec **Apache Spark**, stockés en **tables Delta**, modélisés en **schéma en étoile (Star Schema)**, puis visualisés dans **Power BI**.

**Objectifs principaux**
- Construire un pipeline BI moderne avec Microsoft Fabric (OneLake + Lakehouse + Spark + Pipelines)
- Produire des tables analytiques propres (faits + dimensions) pour le reporting
- Livrer des dashboards interactifs avec des KPI exploitables

---

## Structure du dépôt
- `architecture/` → captures du modèle (Star Schema)
- `data/sample/` → petit CSV d’exemple
- `fabric-pipeline/screenshots/` → captures du pipeline (orchestration + exécutions)
- `notebooks/` → notebooks Spark (transformation + validation)
- `sql/` → scripts SQL (staging / SCD / requêtes)
- `powerbi/screenshots/` → captures Power BI

## Architecture Medallion (Bronze / Silver / Gold)

Ce projet suit une **architecture Medallion** (Bronze/Silver/Gold) pour organiser le cycle de vie des données :

**CSV bruts (ventes quotidiennes)**
→ **Bronze (Lakehouse - Raw)**
→ **Silver (Lakehouse - Clean/Curated)**
→ **Gold (Warehouse - Star Schema)**
→ Modèle sémantique
→ Power BI

### Bronze (Raw)
**Objectif :** ingérer les données telles quelles (append-only), conserver la traçabilité et permettre le retraitement.  
**Dans Fabric / dans ce repo :** zone raw du Lakehouse (ex : `raw/orders/`) avec les CSV quotidiens, par exemple :
- `data/sample/orders_2026-01-01.csv`

Table / dossier typique :
- `sales_orders_raw`

### Silver (Clean / Curated)
**Objectif :** nettoyer et standardiser les données afin d’obtenir un format cohérent prêt pour l’analyse.  
Contient :
- typage et normalisation (dates, numériques)
- contrôles qualité (valeurs nulles, doublons, lignes invalides)
- colonnes standardisées et logique métier de base

Table typique :
- `sales_orders_clean` (table Delta nettoyée)

### Gold (Business-ready)
**Objectif :** produire des tables orientées métier, optimisées pour la BI.  
Dans ce projet, la couche **Gold correspond au Warehouse en schéma en étoile** :
- `FactSales`
- `DimProduct`
- `DimDate`
- `DimChannel`



---

## Modèle de données (Star Schema)
Le Data Warehouse suit un **schéma en étoile** :
- **Table de faits :** `FactSales`
- **Dimensions :** `DimDate`, `DimProduct`, `DimChannel`

![Star Schema](architecture/star_schema.png)

---

## Pipeline de données (Microsoft Fabric)
Le pipeline orchestre le traitement quotidien :
1. **Get Metadata** : vérifie la présence de nouveaux fichiers
2. **Condition** : arrêt propre si aucun fichier
3. **Set Variable** : enregistre un timestamp d’exécution
4. **Notebook** : transformations Spark (raw → Delta)
5. **Script** : étape SQL optionnelle (ex : merge / SCD)
6. **Notebook** : contrôles de validation
7. **Web** : notification de fin d’exécution

### Canvas du pipeline
![Pipeline Canvas](fabric-pipeline/screenshots/pipeline_canvas.png)

### Exemple d’exécution (succès)
![Pipeline Run Success](fabric-pipeline/screenshots/pipeline_run_success.png)

### Exemple d’étape SQL/SCD
![Pipeline SCD Script](fabric-pipeline/screenshots/pipeline_scd_script.png)

---

## Couche SQL (Warehouse + SCD)
Le dossier `sql/` contient des scripts pour :
- créer / réinitialiser les tables de staging
- implémenter des merges (ex : **SCD Type 1**)
- exécuter des requêtes de démonstration / validation

---

## Rapports Power BI
Les captures Power BI sont disponibles dans `powerbi/screenshots/`.

### Vue exécutive
![Executive Overview](powerbi/screenshots/Executive_overview.png)

### Performance par canal & catégorie
![Channel Product Performance](powerbi/screenshots/Channel_product_performance.png)

### Exploration des ventes (détail des transactions)
![Sales Explorer](powerbi/screenshots/Sales_explorer.png)

### Analyse temporelle
![Time Analysis](powerbi/screenshots/Time_analysis.png)

---

## KPI disponibles
Exemples d’indicateurs visibles dans les dashboards :
- **Chiffre d’affaires total (£)**
- **Nombre total de commandes**
- **Unités vendues**
- **Panier moyen (£)**
- Analyse par **canal**, **catégorie**, et **temps**

---

## Stack technique
- **Microsoft Fabric** (OneLake, Lakehouse, Pipelines)
- **Apache Spark / PySpark**
- **Delta Lake**
- **SQL**
- **Power BI**
