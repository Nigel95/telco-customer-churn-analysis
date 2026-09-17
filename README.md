# Telco Customer Churn Analysis

**Analyse du désabonnement client et identification d'un segment prioritaire de rétention : Python, SQL & Power BI**

## 📌 Présentation du projet

Dans le secteur des télécommunications, la fidélisation des clients constitue un enjeu important. L'objectif de ce projet est d'analyser les données clients afin de comprendre les principales caractéristiques associées au **churn** (désabonnement), puis d'identifier un segment de clientèle pouvant être ciblé en priorité par des actions de rétention.

Ce projet suit une démarche **end-to-end Data Analyst** :

**Python → SQL → Power BI → Business Insights**

L'approche est volontairement descriptive et orientée métier : l'objectif n'est pas de construire un modèle prédictif, mais de transformer les données disponibles en **constats exploitables pour la prise de décision**.

> **Important :** les résultats présentés identifient des associations entre caractéristiques clients et churn. Ils ne permettent pas d'établir de lien de causalité.

---

## 🎯 Objectifs

- Comprendre la structure et la qualité des données clients
- Mesurer le taux global de churn
- Explorer les caractéristiques associées au désabonnement
- Identifier des signaux nécessitant une analyse plus approfondie
- Approfondir ces signaux avec SQL sous un angle business
- Identifier un segment de clientèle particulièrement exposé au churn
- Quantifier le poids de ce segment dans le churn global
- Estimer les revenus mensuels associés aux clients ayant churné
- Transformer les résultats en recommandations de rétention à visualiser dans Power BI

---

## 🗂️ Dataset

Le projet utilise le dataset **Telco Customer Churn**, issu de l'IBM Sample Dataset et disponible sur Kaggle.

Le dataset contient :

- **7 043 clients**
- **21 variables**
- Une variable cible : `Churn`

Principales variables utilisées :

| Variable | Description |
|---|---|
| `customerID` | Identifiant unique du client |
| `tenure` | Ancienneté du client en mois |
| `MonthlyCharges` | Montant mensuel facturé |
| `TotalCharges` | Montant total facturé |
| `Contract` | Type de contrat |
| `InternetService` | Type de service Internet |
| `TechSupport` | Présence d'un support technique |
| `PaymentMethod` | Mode de paiement |
| `Churn` | Indique si le client a quitté l'opérateur |

Le fichier de données brut n'est pas inclus dans le repository. La source du dataset est indiquée afin de pouvoir le récupérer séparément.

---

# 🔎 1. Exploration des données : Python

### Objectif

La première étape consiste à comprendre les données, contrôler leur qualité et faire émerger quelques premiers signaux.

Le notebook Python contient notamment :

- exploration de la structure du dataset ;
- analyse des dimensions et types de variables ;
- statistiques descriptives ;
- contrôle des valeurs manquantes ;
- contrôle des doublons ;
- conversion de `TotalCharges` ;
- analyse du taux global de churn ;
- analyse du churn selon le type de contrat ;
- analyse du churn selon l'ancienneté ;
- analyse du profil contractuel des nouveaux clients.

### Nettoyage des données

La variable `TotalCharges` est initialement importée comme variable textuelle. Elle est convertie en numérique.

11 valeurs ne peuvent pas être converties. Elles correspondent aux clients ayant une ancienneté de 0 mois et sont traitées comme des valeurs de facturation cumulée nulle dans le cadre de cette analyse.

### Premiers constats

Le taux global de churn est de :

**26,54 %**

L'analyse exploratoire fait notamment ressortir deux signaux :

- les clients en contrat **Month-to-month** présentent un taux de churn de **42,71 %** ;
- les clients ayant une ancienneté de **0 à 6 mois** présentent un taux de churn d'environ **53 %**, contre environ **10 %** pour les clients ayant 49 mois ou plus.

L'analyse du profil contractuel des nouveaux clients montre également que :

**95,41 % des clients ayant 0 à 6 mois d'ancienneté sont en contrat Month-to-month.**

Ces observations constituent des **hypothèses de travail** pour l'analyse SQL.

---

# 🗄️ 2. Analyse métier : SQL

Python permet d'identifier les premiers signaux. L'analyse SQL vise ensuite à **approfondir ces signaux et à rechercher un profil client particulièrement concerné par le churn**.

L'analyse suit une progression :

```text
Churn global
     ↓
Type de contrat
     ↓
Ancienneté
     ↓
Montant mensuel
     ↓
Service Internet
     ↓
Support technique
     ↓
Identification d'un segment
     ↓
Quantification du churn
     ↓
Exposition en revenus
```

### Principales analyses SQL

#### Churn selon le contrat

Le contrat **Month-to-month** présente le taux de churn le plus élevé :

- Month-to-month : **42,71 %**
- One year : **11,28 %**
- Two year : **2,83 %**

#### Churn selon l'ancienneté

Le taux de churn diminue progressivement avec l'ancienneté :

| Ancienneté | Taux de churn |
|---|---:|
| 0–6 mois | ~53 % |
| 7–12 mois | ~36 % |
| 13–24 mois | ~29 % |
| 25–48 mois | ~20 % |
| 49+ mois | ~10 % |

#### Montant mensuel

L'analyse par tranche de `MonthlyCharges` montre une relation non linéaire avec le churn.

| MonthlyCharges | Taux de churn |
|---|---:|
| 0–30 € | 9,80 % |
| 30–50 € | 30,80 % |
| 50–70 € | 20,76 % |
| 70–89 € | 37,80 % |
| 90 € et + | 33,17 % |

Le croisement avec le type de contrat montre cependant que le contrat Month-to-month reste associé aux taux de churn les plus élevés dans chaque tranche de facturation étudiée.

#### Service Internet

| InternetService | Taux de churn |
|---|---:|
| Fiber optic | 41,89 % |
| DSL | 18,96 % |
| No internet service | 7,40 % |

#### Support technique

| TechSupport | Taux de churn |
|---|---:|
| No | 41,64 % |
| Yes | 15,17 % |
| No internet service | 7,40 % |

L'absence de support technique est donc fortement associée au churn dans les données étudiées.

---

# 🎯 3. Identification d'un segment prioritaire

Le croisement des principaux signaux permet d'identifier le segment suivant :

**Month-to-month + Fiber optic + No TechSupport**

Ce segment compte :

### **1 796 clients**

Parmi eux :

### **1 033 clients ont churné**

Soit un taux de churn de :

### **57,52 %**

Ce segment représente également :

### **55,27 % de l'ensemble des churners**

Autrement dit, **plus d'un churner sur deux appartient à ce segment** dans le dataset étudié.

---

## 💰 Exposition en revenus

Les clients ayant churné au sein de ce segment représentaient :

### **88 165,75 € de facturation mensuelle**

Cette valeur correspond à la somme des `MonthlyCharges` des clients du segment ayant effectivement churné.

Elle doit être interprétée comme une **exposition mensuelle associée aux churners**, et non comme une perte de chiffre d'affaires démontrée : le dataset ne permet pas de mesurer directement le revenu qui aurait été conservé sans churn.

---

# 📊 4. Power BI — Dashboard

Les résultats de l'analyse SQL sont ensuite destinés à être transformés en dashboard Power BI.

## Page 1 — Vue d'ensemble du churn

La première page présente les principaux indicateurs :

- **7 043** clients
- **1 869** clients ayant churné
- **26,54 %** de taux de churn
- revenus mensuels associés aux churners

Visualisations principales :

- taux de churn par type de contrat ;
- taux de churn selon l'ancienneté ;
- taux de churn par service Internet ;
- taux de churn selon le support technique ;
- analyse croisée du contrat avec certains services.

## Page 2 — Segment prioritaire de rétention

La deuxième page se concentre sur le segment :

**Month-to-month + Fiber optic + No TechSupport**

Indicateurs :

| KPI | Valeur |
|---|---:|
| Clients concernés | 1 796 |
| Clients ayant churné | 1 033 |
| Taux de churn | 57,52 % |
| Part des churners | 55,27 % |
| Revenus mensuels associés aux churners | 88,17 K€ |

La comparaison avec le taux global permet de mettre en évidence un écart de :

**+30,98 points de pourcentage**

entre le segment prioritaire (**57,52 %**) et le taux global de churn (**26,54 %**).

---

# 💡 5. Business Insights

L'analyse met en évidence plusieurs constats :

### 1. Le churn est particulièrement élevé chez les clients en contrat Month-to-month

Le taux de churn atteint **42,71 %**, contre **11,28 %** pour les contrats d'un an et **2,83 %** pour les contrats de deux ans.

### 2. Les premiers mois de la relation client constituent une période à surveiller

Les clients ayant 0 à 6 mois d'ancienneté présentent un taux de churn d'environ **53 %**, contre environ **10 %** chez les clients ayant 49 mois ou plus.

### 3. Le profil contractuel des nouveaux clients est fortement orienté vers le Month-to-month

**95,41 %** des clients ayant 0 à 6 mois d'ancienneté sont en contrat Month-to-month.

### 4. Certains signaux se combinent

Le croisement du contrat, du service Internet et du support technique fait ressortir un segment de **1 796 clients** affichant un taux de churn de **57,52 %**.

### 5. Le segment identifié concentre une part importante du churn

Les **1 033 churners** de ce segment représentent **55,27 % de l'ensemble des clients ayant churné**.

---

# 🚀 6. Pistes d'actions de rétention

À partir des associations observées, trois pistes peuvent être envisagées :

### Encourager les contrats longue durée

Proposer des avantages ou des incitations à la migration des clients Month-to-month vers des contrats d'un ou deux ans.

### Renforcer l'accompagnement des clients Fiber

Porter une attention particulière aux clients Fiber en contrat Month-to-month, notamment durant les premiers mois de leur parcours.

### Développer l'accès au support technique

Cibler en priorité les clients Fiber en contrat Month-to-month ne bénéficiant pas du support technique.

> Ces pistes constituent des hypothèses d'actions issues de l'analyse descriptive. Elles devraient être testées avec des données complémentaires et, idéalement, par une mesure de leur impact avant généralisation.

---

# 🧠 7. Démarche analytique

Le projet suit une logique volontairement progressive :

```text
                    QUESTION BUSINESS
                           │
                           ▼
                 Pourquoi les clients
                    churnent-ils ?
                           │
                           ▼
                      PYTHON
              Exploration & Data Quality
                           │
                           ▼
                      HYPOTHÈSES
          • Contrat Month-to-month
          • Faible ancienneté
                           │
                           ▼
                        SQL
              Analyse métier approfondie
                           │
                           ▼
                 SEGMENT IDENTIFIÉ
      Month-to-month + Fiber + No TechSupport
                           │
                           ▼
                     POWER BI
              Visualisation & pilotage
                           │
                           ▼
                  BUSINESS INSIGHTS
                Pistes de rétention
```

Cette séparation permet d'éviter de reproduire la même analyse avec chaque outil :

- **Python** sert à explorer et faire émerger des hypothèses ;
- **SQL** sert à approfondir et segmenter ;
- **Power BI** sert à communiquer les résultats ;
- **Business reasoning** sert à traduire les constats en pistes d'action.

---

# 🛠️ Technologies utilisées

- **Python**
  - pandas
  - NumPy
  - Matplotlib
  - Seaborn
  - Google Colab

- **SQL**
  - Microsoft SQL Server
  - CTE
  - `CASE WHEN`
  - agrégations
  - `GROUP BY`
  - fonctions de fenêtre
  - `TRY_CAST`
  - `CROSS JOIN`

- **Power BI**
  - Power Query
  - DAX
  - KPI
  - segmentation
  - visualisation interactive

- **GitHub**
  - versionnement
  - documentation
  - présentation du portfolio

---


# 📌 Limites de l'analyse

Cette analyse présente plusieurs limites :

- elle repose sur un dataset descriptif ;
- elle ne constitue pas un modèle prédictif de churn ;
- les associations observées ne démontrent pas de causalité ;
- les actions de rétention proposées nécessiteraient une validation par expérimentation ou analyse complémentaire.

L'objectif est donc de **faire émerger des profils et des hypothèses business**, plutôt que de prétendre expliquer à eux seuls les causes du churn.

---

## 👤 À propos

Projet réalisé dans le cadre d'une démarche de montée en compétences et de constitution d'un portfolio **Data Analyst**.

**Compétences mobilisées :** analyse exploratoire, data cleaning, Python, SQL, segmentation client, KPI, Power BI, DAX, data storytelling et raisonnement business.

