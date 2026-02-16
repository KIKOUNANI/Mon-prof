#!/bin/bash

# 🚀 Script de préparation pour GitHub
# Site Éducatif - MERN Stack

echo "🎓 Configuration du projet Site Éducatif pour GitHub"
echo "=================================================="

# Couleurs pour les messages
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# Fonction pour afficher les étapes
print_step() {
    echo -e "${BLUE}[ÉTAPE]${NC} $1"
}

print_success() {
    echo -e "${GREEN}[SUCCÈS]${NC} $1"
}

print_warning() {
    echo -e "${YELLOW}[ATTENTION]${NC} $1"
}

print_error() {
    echo -e "${RED}[ERREUR]${NC} $1"
}

# Vérifier si git est installé
print_step "Vérification de Git..."
if ! command -v git &> /dev/null; then
    print_error "Git n'est pas installé. Installation en cours..."
    sudo apt-get update
    sudo apt-get install git -y
else
    print_success "Git est installé"
fi

# Initialiser git si nécessaire
if [ ! -d .git ]; then
    print_step "Initialisation du dépôt Git..."
    git init
    print_success "Dépôt Git initialisé"
fi

# Créer .gitignore
print_step "Création du fichier .gitignore..."
cat > .gitignore << 'EOF'
# Dépendances
node_modules/
package-lock.json
yarn.lock

# Environnement
.env
.env.local
.env.development
.env.production
.env.test

# Build
dist/
build/
*.js.map
*.css.map

# Logs
logs/
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# IDE
.vscode/
.idea/
*.swp
*.swo
*~
.DS_Store

# Backend
backend/node_modules/
backend/.env
backend/package-lock.json

# Frontend
frontend/node_modules/
frontend/.env
frontend/package-lock.json
frontend/build/
frontend/.cache/

# Base de données
*.db
*.sqlite

# Tests
coverage/
.nyc_output/

# Docker
docker-compose.override.yml

# Temporary files
tmp/
temp/
EOF
print_success "Fichier .gitignore créé"

# Créer README.md
print_step "Création du README.md..."
cat > README.md << 'EOF'
# 🎓 Site Éducatif - Plateforme d'Apprentissage Interactive

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
![React](https://img.shields.io/badge/React-18.2.0-61DAFB?logo=react)
![Node.js](https://img.shields.io/badge/Node.js-18.x-339933?logo=node.js)
![MongoDB](https://img.shields.io/badge/MongoDB-6.x-47A248?logo=mongodb)

## 📋 Description

Plateforme éducative complète permettant aux élèves d'accéder à des cours, de faire des exercices interactifs, et de suivre leur progression avec des fonctionnalités de gamification.

### ✨ Fonctionnalités principales

- ✅ Système d'authentification (élèves/enseignants)
- ✅ Résumés de cours téléchargeables (PDF)
- ✅ Exercices interactifs avec correction automatique
- ✅ Gamification (badges, points, défis quotidiens)
- ✅ Assistant IA pour l'aide aux devoirs
- ✅ Mode examen chronométré
- ✅ Tableau de bord de progression
- ✅ Mode hors ligne (PWA)
- ✅ Support multilingue

## 🏗️ Architecture du projet
