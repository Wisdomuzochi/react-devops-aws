# react-devops-aws ⚛️

![CI/CD](https://github.com/Wisdomuzochi/react-devops-aws/actions/workflows/deploy.yml/badge.svg)
![AWS S3](https://img.shields.io/badge/AWS-S3-orange?logo=amazon-aws)
![CloudFront](https://img.shields.io/badge/AWS-CloudFront-orange?logo=amazon-aws)
![React](https://img.shields.io/badge/React-18-blue?logo=react)

Une **Single Page Application React** déployée automatiquement sur **AWS S3 + CloudFront** via un pipeline **GitHub Actions** — avec HTTPS, CDN mondial et invalidation de cache automatique à chaque push sur `main`.

> Ce projet illustre le cycle de déploiement continu (CD) d'un frontend moderne : du code local à une URL publique mondiale, sans aucune intervention manuelle.

---

## Architecture

```
Développeur (git push)
       │
       ▼
┌──────────────────────┐
│   GitHub Actions     │  ← Pipeline CD déclenché sur push vers main
│                      │
│  1. Checkout code    │
│  2. Setup Node.js    │
│  3. npm ci           │
│  4. npm run build    │
│  5. Configure AWS    │
│  6. Sync → S3        │
│  7. Invalide cache   │
└────────┬─────────────┘
         │
         ▼
┌──────────────────────┐
│      AWS S3          │  ← Stockage des fichiers statiques (HTML/CSS/JS)
│  (eu-west-3 / Paris) │     Static Website Hosting activé
│                      │     Cache-Control : max-age=31536000
└────────┬─────────────┘
         │
         ▼
┌──────────────────────┐
│   AWS CloudFront     │  ← CDN mondial — Points of Presence worldwide
│                      │     HTTP → HTTPS redirect automatique
│                      │     Invalidation cache à chaque déploiement
│                      │
│  URL publique HTTPS  │  ← Accessible partout dans le monde
└──────────────────────┘
```

---

## Fonctionnement du déploiement

Chaque `git push` sur `main` déclenche automatiquement :

```
1. Build de production React (npm run build)
       ↓
2. Synchronisation des fichiers vers S3 (aws s3 sync --delete)
       ↓
3. Invalidation du cache CloudFront (/* )
       ↓
4. Nouvelle version en ligne en ~2 minutes
```

**Zéro intervention manuelle. Zéro downtime.**

---

## Stack technique

| Couche | Technologie |
|---|---|
| Framework Frontend | React 18 |
| Build tool | Create React App |
| Stockage | AWS S3 (Static Website Hosting) |
| CDN | AWS CloudFront |
| CI/CD | GitHub Actions |
| Cloud | AWS (eu-west-3 — Paris) |

---

## Structure du projet

```
react-devops-aws/
├── .github/
│   └── workflows/
│       └── deploy.yml      # Pipeline CD GitHub Actions
├── public/
│   └── index.html
├── src/
│   ├── App.js              # Composant principal React
│   ├── App.css
│   └── index.js
├── package.json
└── package-lock.json
```

---

## Démarrage rapide

### Prérequis

- Node.js 20+
- npm

### Lancer en local

```bash
# Cloner le repo
git clone git@github.com:Wisdomuzochi/react-devops-aws.git
cd react-devops-aws

# Installer les dépendances
npm install

# Lancer le serveur de développement
npm start
```

App disponible sur : `http://localhost:3000`

### Build de production

```bash
npm run build
```

Génère un dossier `build/` contenant les fichiers statiques optimisés et minifiés.

---

## Pipeline CI/CD

Le pipeline est défini dans `.github/workflows/deploy.yml` et se déclenche automatiquement à chaque push sur `main`.

### Secrets GitHub requis

| Secret | Description |
|---|---|
| `AWS_ACCESS_KEY_ID` | Clé d'accès de l'utilisateur IAM |
| `AWS_SECRET_ACCESS_KEY` | Clé secrète de l'utilisateur IAM |
| `CLOUDFRONT_DISTRIBUTION_ID` | ID de la distribution CloudFront |

L'utilisateur IAM suit le **principe du moindre privilège** — permissions limitées à S3 et CloudFront uniquement.

---

## Configuration AWS

### S3 — Static Website Hosting

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::react-devops-aws-wisdom/*"
    }
  ]
}
```

### CloudFront

| Paramètre | Valeur |
|---|---|
| Origin | S3 Website Endpoint |
| Viewer Protocol Policy | Redirect HTTP to HTTPS |
| Cache-Control | max-age=31536000 |
| Invalidation | `/*` à chaque déploiement |

---

## Concepts DevOps illustrés

- **Continuous Deployment (CD)** — chaque push déclenche un déploiement automatique en production
- **Static Site Hosting** — séparation frontend/backend, scalabilité infinie, coût quasi nul
- **CDN global** — distribution mondiale via CloudFront Points of Presence
- **Cache invalidation** — mise à jour immédiate du contenu après chaque déploiement
- **IAM least-privilege** — permissions minimales nécessaires pour le pipeline
- **Cache-Control optimisé** — performance maximale pour les assets statiques

---

## Auteur

**Wisdom MUONAKA**
[GitHub](https://github.com/Wisdomuzochi) · [LinkedIn](https://linkedin.com/in/wisdom-muonaka-45781b321)