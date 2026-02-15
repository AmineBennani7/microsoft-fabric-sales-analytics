[![Microsoft Fabric Sales Analytics](https://img.shields.io/badge/Microsoft%20Fabric%20Sales%20Analytics-0ea5e9?style=for-the-badge&labelColor=111827&logo=microsoft&logoColor=white)](#)

[![fr](https://img.shields.io/badge/lang-fr-blue.svg)](README.fr.md)
[![en](https://img.shields.io/badge/lang-en-brightgreen.svg)](README.md)

# Microsoft Fabric Sales Analytics

## Présentation
Démo analytique de bout en bout construite avec **Microsoft Fabric**. Des CSV de ventes quotidiens sont ingérés dans un **Lakehouse**, transformés avec **Spark** en **tables Delta**, modélisés en **schéma en étoile** (Warehouse), puis visualisés dans **Power BI**.

## Architecture (Medallion)
**CSV quotidiens → Bronze (raw) → Silver (clean) → Gold (Warehouse / Star Schema) → Modèle sémantique → Power BI**

| Couche | Objet Fabric | Sortie principale |
|---|---|---|
| **Bronze** | Lakehouse (raw) | `sales_orders_raw` |
| **Silver** | Lakehouse (curated) | `sales_orders_clean` |
| **Gold** | Warehouse | `FactSales`, `DimDate`, `DimChannel` (+ dimension produit) |

## Modèle de données (Star Schema)
![Star Schema](architecture/star_schema.png)

## Pipeline de données (Microsoft Fabric)
Le pipeline orchestre l’ingestion, les transformations et les étapes Warehouse.

![Pipeline Canvas](fabric-pipeline/screenshots/pipeline_canvas.png)

![Pipeline Run Success](fabric-pipeline/screenshots/pipeline_run_success.png)

![Pipeline SQL Step](fabric-pipeline/screenshots/pipeline_scd_daily.png)

## Warehouse (SQL) — SCD
Les dimensions sont maintenues via des patterns SCD :

| Dimension | SCD | Objectif | Implémentation |
|---|---:|---|---|
| `DimChannel` | Type 1 | Conserver la dernière valeur | `MERGE` (écrasement/insertion) |
| `DimProduct_SCD2` | Type 2 | Historiser (ex : changement de catégorie) | Fermeture de la ligne courante + insertion d’une nouvelle version (`start_date`, `end_date`, `is_current`) |

## Rapports Power BI
![Executive Overview](powerbi/screenshots/Executive_overview.png)

![Channel Product Performance](powerbi/screenshots/Channel_product_performance.png)

![Sales Explorer](powerbi/screenshots/Sales_explorer.png)

![Time Analysis](powerbi/screenshots/Time_analysis.png)

## KPI
- Chiffre d’affaires total (£)
- Nombre total de commandes
- Unités vendues
- Panier moyen (£)
- Analyse par canal, catégorie produit et temps

## Stack technique
Microsoft Fabric (OneLake, Lakehouse, Warehouse, Pipelines), Spark (PySpark), Delta Lake, SQL, Power BI.

## Démo vs Production
Ce dépôt est une **démo “production-like”** :
- Les CSV d’entrée sont des **fichiers d’exemple** (ingestion manuelle/simulée).
- Le pipeline n’est pas forcément connecté à une landing zone alimentée automatiquement chaque jour.

Une version 100% professionnelle ajouterait généralement :
- Ingestion automatisée (OneLake/ADLS/S3/API)
- Exécutions planifiées/trigger + traitements idempotents
- Monitoring/alertes + contrôles qualité de base
- Refresh automatique du modèle sémantique / Power BI
