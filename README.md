```markdown
# 🌊 AquaSense — Surveillance Intelligente de l'Eau

> **Application mobile de surveillance intelligente des compteurs d'eau avec détection d'anomalies par IA**

---

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.11-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python" />
  <img src="https://img.shields.io/badge/React-18-61DAFB?style=for-the-badge&logo=react&logoColor=black" alt="React" />
  <img src="https://img.shields.io/badge/FastAPI-0.100-009688?style=for-the-badge&logo=fastapi&logoColor=white" alt="FastAPI" />
  <img src="https://img.shields.io/badge/Licence-Prototype-orange?style=for-the-badge" alt="Licence" />
</p>

---

## 💡 Navigation Rapide

- [🏠 Description](#-description)
- [📱 Aperçu de l'Application](#-aperçu-de-lapplication)
- [📋 Prérequis & Matériel](#-prérequis--matériel)
- [🚀 Installation](#-installation)
- [🤖 Modèles IA](#-modèles-ia)
- [👤 Compte de Test](#-compte-de-test)
- [📁 Structure du Projet](#-structure-du-projet)
- [🛠️ Technologies](#️-technologies)
- [📊 Seuils de Consommation](#-seuils-de-consommation-m³h)
- [🔌 API Endpoints](#-api-endpoints)
- [🔧 Commandes Utiles](#-commandes-utiles)
- [🗺️ Roadmap](#️-roadmap)

---

## 🏠 Description

**AquaSense** est une solution complète IoT + IA conçue pour transformer la gestion de la ressource en eau :

* 📊 **Surveillance en temps réel** de la consommation d'eau.
* 🚨 **Détection automatique d'anomalies** (fuites, surconsommation, anomalies saisonnières).
* 🔔 **Système d'alertes intelligentes** instantanées.
* 📈 **Analyse de l'historique** et tendances de consommation.
* ⚙️ **Seuils personnalisables** selon le type de bâtiment (résidentiel, commercial, industriel).

---

## 📱 Aperçu de l'Application

<div align="center">

| 🔐 Login | 📊 Dashboard | 🔔 Alertes | 👤 Profil |
| :---: | :---: | :---: | :---: |
| ![](assets/screen_login.png.jpeg) | ![](assets/screen_dashboard.png.jpeg) | ![](assets/screen_alerts.png.jpeg) | ![](assets/screen_profile.png.jpeg) |

</div>

---

## 📋 Prérequis & Matériel

### 🖥️ Environnement Logiciel
* **Python** : `3.11+`
* **Node.js** : `18+`
* **npm** : `9+`

### 📷 Matériel Requis

| Composant | Rôle |
| :--- | :--- |
| **ESP32-CAM** | Capture l'image du compteur et l'envoie au backend via Wi-Fi |
| **Compteur d'eau** | Source de données à surveiller |
| **Alimentation 5V** | Alimente le module ESP32-CAM |

> ℹ️ **Note :** L'ESP32-CAM envoie les images JPG au backend via Wi-Fi, aucun câblage supplémentaire n'est requis.

---

## 🚀 Installation

### 1. Frontend (React + Vite)

```bash
# Installer les dépendances
npm install

# Lancer le serveur de développement
npm run dev
```

📍 Application disponible sur : `http://localhost:5173`

### 2. Backend (FastAPI)

```bash
# Aller dans le dossier backend
cd aquasense-backend

# Installer les dépendances Python
py -3.11 -m pip install -r requirements.txt

# Lancer le serveur API
py -3.11 -m uvicorn main:app --reload --host 127.0.0.1 --port 8000
```

📍 API disponible sur : `http://127.0.0.1:8000`  
📖 Documentation Swagger interactive : `http://127.0.0.1:8000/docs`

---

## 🤖 Modèles IA

### Vue d'ensemble du pipeline complet

<p align="center">
  <img src="assets/pipeline_overview.png" alt="Pipeline de traitement complet" width="800" />
</p>

Le pipeline de traitement se divise en deux branches complémentaires :
* 🔍 **OCR** (`< 100 ms`) : Lecture automatique de l'index en m³ depuis la capture caméra.
* ⚡ **XGBoost** (`< 10 ms`) : Classification temps réel de l'anomalie parmi 6 classes.

---

### Module OCR — 2 ÉTAPES

La lecture des chiffres repose sur deux passes YOLOv8 successives :

<p align="center">
  <img src="assets/ocr_pipeline.png" alt="Pipeline OCR" width="700" />
</p>

| Étape | Modèle | Rôle |
| :---: | :--- | :--- |
| **Stage 1** | `best.pt` (YOLOv8n-seg) | **Segmentation** — Isole la zone d'affichage des chiffres |
| **Stage 2** | `best.pt` (YOLOv8n-det) | **Détection 0–9** — Identifie et lit chaque chiffre individuellement |

---

### Détection d'Anomalies — XGBoost

<p align="center">
  <img src="assets/model_accuracy.png" alt="Comparaison des modèles" width="600" />
</p>

Le modèle **XGBoost** surpasse les autres algorithmes évalués avec **90,4 % d'accuracy** et un **F1-score de 0,904**.  
Il catégorise chaque relevé parmi **6 catégories** :

`normal` | `surconsommation` | `fuite_nocturne` | `anomalie_saisonniere` | `pic_inhabituel` | `conso_nulle`

---

### 📁 Fichiers Requis pour l'IA

Pour activer l'ensemble des modules d'IA, placez les fichiers suivants dans `aquasense-backend/ai_models/` :

| Fichier | Description |
| :--- | :--- |
| `best.pt` | Modèle YOLOv8 pour la détection et lecture de chiffres |
| `best_model.pkl` | Modèle XGBoost pour la classification d'anomalies |
| `scaler.pkl` | Normaliseur de données |
| `le_building.pkl` | LabelEncoder pour le type de bâtiment |
| `le_season.pkl` | LabelEncoder pour la saisonnalité |
| `metadata.json` | Métadonnées de configuration du modèle |

> ⚠️ **Note :** En l'absence de ces fichiers, le backend bascule automatiquement sur des règles heuristiques.

---

## 👤 Compte de Test

> **Email :** `demo@aquasense.tn`  
> **Mot de passe :** `password123`

---

## 📁 Structure du Projet

```text
AquaSense Mobile App Prototype/
├── src/                          # Frontend React
│   ├── api/                      # Appels API HTTP
│   │   ├── auth.ts               # Authentification
│   │   ├── users.ts              # Gestion utilisateurs
│   │   ├── readings.ts           # Relevés de consommation
│   │   └── alerts.ts             # Alertes
│   ├── app/
│   │   ├── components/
│   │   │   ├── screens/          # Écrans principaux
│   │   │   │   ├── LoginScreen.tsx
│   │   │   │   ├── RegisterScreen.tsx
│   │   │   │   ├── DashboardScreen.tsx
│   │   │   │   ├── ProfileScreen.tsx
│   │   │   │   ├── AlertsScreen.tsx
│   │   │   │   └── ...
│   │   │   └── ui/               # Composants UI (shadcn)
│   │   └── routes.tsx            # Navigation React Router
│   └── styles/                   # Styles globaux (Tailwind CSS)
│
├── aquasense-backend/            # Backend FastAPI
│   ├── routes/                   # Endpoints API REST
│   │   ├── auth.py               # /auth/login, /auth/register
│   │   ├── users.py              # /users/{id}
│   │   ├── readings.py           # /readings/*
│   │   ├── alerts.py             # /alerts/*
│   │   └── settings_api.py       # /settings/*
│   ├── models/                   # Modèles & Pipelines IA
│   │   ├── anomaly.py            # Détection d'anomalies XGBoost
│   │   └── yolo_ocr.py           # Engine OCR YOLOv8
│   ├── services/                 # Métier & Services
│   ├── database.py               # Config SQLAlchemy & SQLite
│   ├── main.py                   # Point d'entrée FastAPI
│   └── requirements.txt          # Dépendances Python
│
├── package.json                  # Dépendances Node.js / Scripts
├── vite.config.ts                # Configuration Vite
└── README.md                     # Documentation
```

---

## 🛠️ Technologies

| Domaines | Stack Technologique |
| :--- | :--- |
| **Frontend** | React 18, TypeScript, Vite, Tailwind CSS, Recharts |
| **Backend** | FastAPI, Python 3.11, SQLAlchemy, SQLite |
| **IA & Vision** | YOLOv8 (OCR), XGBoost (Classification), EasyOCR |
| **Sécurité** | JWT (python-jose), bcrypt |
| **Spécifications API** | REST Architecture, OpenAPI / Swagger |

---

## 📊 Seuils de Consommation (m³/h)

| Type de Bâtiment | Consommation Normale | Seuil d'Alerte |
| :--- | :---: | :---: |
| 🏠 **Maison** | 0.013 | 0.018 |
| 🏢 **Appartement** | 0.009 | 0.013 |
| ☕ **Café** | 0.045 | 0.065 |
| 🍽️ **Restaurant** | 0.090 | 0.130 |
| 🏨 **Hôtel** | 0.250 | 0.375 |
| 🏙️ **Immeuble** | 0.120 | 0.175 |
| 🏭 **Usine** | 0.400 | 0.600 |

---

## 🔌 API Endpoints

| Méthode | Endpoint | Description |
| :---: | :--- | :--- |
| `POST` | `/auth/login` | Connexion utilisateur et génération de token |
| `POST` | `/auth/register` | Inscription d'un nouvel utilisateur |
| `GET` | `/users/{id}` | Récupération des informations du profil |
| `GET` | `/readings/` | Historique des relevés de consommation |
| `POST` | `/readings/` | Enregistrement d'un nouveau relevé (Image / Manuel) |
| `GET` | `/alerts/` | Liste complète des alertes générées |
| `PUT` | `/alerts/{id}` | Marquer une alerte comme lue |
| `GET` | `/settings/` | Paramètres et seuils de l'utilisateur |

> 💡 **Documentation Interactive :** Accédez à Swagger UI sur `http://127.0.0.1:8000/docs` dès le démarrage du serveur backend.

---

## 🔧 Commandes Utiles

```bash
# Terminal 1 : Lancer le Frontend
npm run dev

# Terminal 2 : Lancer le Backend FastAPI
cd aquasense-backend
py -3.11 -m uvicorn main:app --reload --port 8000

# Réinitialiser la base de données (PowerShell)
Remove-Item -Path "aquasense-backend/data/aquasense.db" -Force
```

---

## 🗺️ Roadmap

- [ ] 📡 **Multi-compteurs** : Prise en charge de plusieurs compteurs par compte utilisateur.
- [ ] 📄 **Rapports** : Génération et export PDF automatique des bilans de consommation.
- [ ] 📱 **Mobile Native** : Migration vers React Native pour iOS & Android.
- [ ] 🗄 **Base de données** : Migration SQLite vers PostgreSQL en production.
- [ ] 🎛️️ **Back-Office** : Déploiement d'un tableau de bord administrateur global.

---

## 📝 Licence

Projet prototype — **AquaSense**

```
