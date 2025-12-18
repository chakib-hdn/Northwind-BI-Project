

# 📊 Projet Northwind – Entrepôt de Données & Visualisation Power BI

## 📌 Description du projet

Ce projet consiste à concevoir et exploiter un **entrepôt de données (Data Warehouse)** basé sur la base **Northwind**, en utilisant :

* **Power BI Desktop** pour l’ETL, la modélisation et le reporting
* **SQL Server (SSMS)** et **fichiers Excel** comme sources de données
* **Modèle en étoile** (Star Schema)
* **Scripts Python intégrés dans Power BI** pour des visualisations avancées
* Une **comparaison ETL Power BI vs Talend**

Le projet aboutit à un **dashboard interactif** contenant **6 visualisations Python**.

---

## 📁 Structure du projet

```text
Northwind_DW/
│
├── data/
│   ├── Customers.xlsx
│   ├── Employees2.xlsx
│   ├── Orders.xlsx
│
├── scripts/
│   ├── Dim_Employee.m
│   ├── Dim_Client.m
│   ├── Dim_Temps.m
│   ├── TF_Commande.m
│
├── exports/
│   ├── captures_dashboard/
│
├── Northwind_DW.pbix
├── rapport_final.pdf
└── README.md
```

---

## ⚙️ Prérequis techniques

### Système

* Windows 10 ou 11 (64 bits)
* RAM : 8 Go recommandés
* Espace disque : ≥ 2 Go

### Logiciels

* **Power BI Desktop (gratuit)**
* **SQL Server + SSMS**
* **Python 3.8 ou plus**

### Librairies Python

```bash
pip install pandas matplotlib numpy seaborn
```

---

## 🚀 Étapes pour reproduire le projet

### 1️⃣ Installation de Power BI Desktop

Télécharger Power BI Desktop via :

* [https://aka.ms/pbidesktopstore](https://aka.ms/pbidesktopstore)

Lancer Power BI et configurer :

* Langue : Français
* Région : France

---

### 2️⃣ Préparation des sources de données

#### 📌 Base SQL Server

* Serveur : `DESKTOP-VEO1CEQ\SQLCHAKIB` (adapter si nécessaire)
* Base de données : `Northwind`
* Tables utilisées :

  * Customers
  * Employees
  * EmployeeTerritories
  * Orders
  * Territories

#### 📌 Fichiers Excel

Placer les fichiers dans un dossier local :

```text
Customers.xlsx
Employees2.xlsx
Orders.xlsx
```

---

### 3️⃣ Importation des données dans Power BI

1. **SQL Server**

   * Obtenir des données → SQL Server
   * Mode : Importer
   * Sélectionner les tables nécessaires

2. **Excel**

   * Obtenir des données → Excel
   * Importer les fichiers `.xlsx`

---

### 4️⃣ Transformation des données (ETL – Power Query)

1. Ouvrir **Transformer les données**
2. Renommer les requêtes :

   * `Customers_ssms`
   * `Employees_ssms`
   * `Orders_excel`, etc.
3. Vérifier les types de données
4. Nettoyage et standardisation

---

### 5️⃣ Création du modèle en étoile

#### 🧱 Tables de dimensions

* `Dim_Employee`
* `Dim_Client`
* `Dim_Temps`

👉 Pour chaque dimension :

* Nouvelle requête → Requête vide
* Coller le script M correspondant (`.m`)
* Renommer
* Fermer & Appliquer

#### 🧮 Table de faits

* `TF_Commande`
* Nombre de lignes attendu : **878**

---

### 6️⃣ Création des relations

Dans la vue **Modèle** de Power BI :

| Table de faits | Dimension    | Clé            |
| -------------- | ------------ | -------------- |
| TF_Commande    | Dim_Temps    | id_temps       |
| TF_Commande    | Dim_Employee | id_seqEmployee |
| TF_Commande    | Dim_Client   | id_seqClient   |

* Cardinalité : **1 → ***
* Sens du filtre : **dimension vers fait**

---

## 🐍 Configuration Python dans Power BI

1. Installer Python
2. Power BI → Fichier → Options → Scripting Python
3. Spécifier le chemin Python, ex :

```text
C:\Users\...\Python39\
```

4. Redémarrer Power BI

---

## 📈 Exécution des visualisations Python

### Principe

* Ajouter un **visuel Python**
* Glisser les champs nécessaires
* Coller le script Python
* Power BI génère automatiquement le DataFrame `dataset`

### Visualisations disponibles

1. 📊 Volume des commandes par mois
2. 🏆 Top 10 clients par commandes livrées
3. 🌍 Top 5 territoires par performance
4. 🔥 Heatmap Clients × Employés
5. 🧭 Répartition par région
6. 📈 Évolution mensuelle des commandes

👉 Tous les scripts sont **reproductibles sans modification**, à condition que :

* Les colonnes existent
* Les relations soient correctes

---

## ✅ Résultats attendus

| Table        | Lignes |
| ------------ | ------ |
| Dim_Temps    | 878    |
| Dim_Client   | 120    |
| Dim_Employee | 58     |
| TF_Commande  | 878    |

Dashboard final :

* 6 graphiques Python
* Interactions avec filtres Power BI
* Données cohérentes avec le rapport

---

## 🛠 Dépannage courant

| Problème                 | Solution                    |
| ------------------------ | --------------------------- |
| Python non détecté       | Vérifier le chemin          |
| Graphique vide           | Vérifier les champs glissés |
| Erreur colonne manquante | Vérifier noms exacts        |
| Performance lente        | Réduire le volume affiché   |

---

## 📌 Notes importantes

* Power BI Desktop suffit pour ce projet
* Talend est présenté à titre comparatif
* Le projet est optimisé pour **POC / projets académiques**
* Aucun serveur BI requis

IHADDADENE Chakib 181831091825

