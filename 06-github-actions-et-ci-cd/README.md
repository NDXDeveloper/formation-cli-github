🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 6. GitHub Actions et CI/CD

## Introduction

GitHub Actions représente l'une des fonctionnalités les plus puissantes de GitHub, permettant d'automatiser vos workflows de développement directement depuis votre repository. Avec la CLI GitHub, vous pouvez interagir avec ces workflows de manière efficace et intégrée à votre environnement de développement.

## Qu'est-ce que GitHub Actions ?

GitHub Actions est une plateforme d'intégration continue et de déploiement continu (CI/CD) qui vous permet d'automatiser vos workflows de développement. Elle vous permet de :

- **Automatiser les tests** lors de chaque push ou pull request
- **Déployer automatiquement** votre application en production
- **Exécuter des tâches** sur déclenchement d'événements GitHub
- **Orchestrer des workflows complexes** avec plusieurs étapes

## Concepts clés

### Workflow
Un workflow est un processus automatisé configurable composé d'un ou plusieurs jobs. Les workflows sont définis dans des fichiers YAML stockés dans le répertoire `.github/workflows/` de votre repository.

### Job
Un job est un ensemble d'étapes qui s'exécutent sur le même runner. Par défaut, les jobs s'exécutent en parallèle, mais peuvent être configurés pour s'exécuter séquentiellement.

### Step
Une step est une tâche individuelle qui peut exécuter des commandes ou utiliser des actions. Chaque step s'exécute dans son propre processus.

### Action
Une action est une application portable réutilisable qui effectue une tâche complexe fréquemment répétée.

### Runner
Un runner est un serveur qui exécute vos workflows. GitHub fournit des runners hébergés avec Ubuntu Linux, Windows et macOS.

## Pourquoi utiliser la CLI GitHub avec Actions ?

La CLI GitHub offre plusieurs avantages pour travailler avec GitHub Actions :

### 🚀 **Efficacité**
- Visualisation rapide de l'état des workflows depuis le terminal
- Pas besoin de naviguer dans l'interface web pour des tâches courantes
- Intégration naturelle dans votre workflow de développement

### 🔍 **Debugging facilité**
- Accès direct aux logs d'exécution
- Téléchargement des artifacts en une commande
- Identification rapide des échecs et erreurs

### ⚡ **Contrôle avancé**
- Déclenchement manuel de workflows
- Re-exécution de jobs échoués
- Annulation de workflows en cours

### 🔧 **Automatisation**
- Intégration dans des scripts de déploiement
- Surveillance automatisée des builds
- Notifications personnalisées

## Structure typique d'un workflow

Voici un exemple de workflow GitHub Actions basique :

```yaml
name: CI Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'

    - name: Install dependencies
      run: npm ci

    - name: Run tests
      run: npm test

    - name: Build project
      run: npm run build
```

## Cas d'usage courants

### 🧪 **Tests automatisés**
- Exécution des tests unitaires et d'intégration
- Tests de régression sur plusieurs versions
- Validation des pull requests

### 📦 **Build et packaging**
- Compilation d'applications
- Création d'images Docker
- Génération d'artifacts

### 🚀 **Déploiement**
- Déploiement automatique en staging/production
- Publication de packages NPM/PyPI
- Mise à jour de documentation

### 🔔 **Notifications**
- Envoi d'alertes sur échec
- Rapports de couverture de code
- Notifications Slack/Discord

## Intégration avec votre workflow de développement

La CLI GitHub s'intègre parfaitement dans un workflow de développement moderne :

```bash
# 1. Développement local
git checkout -b feature/nouvelle-fonctionnalite
# ... développement ...

# 2. Commit et push
git add .
git commit -m "Ajout nouvelle fonctionnalité"
git push origin feature/nouvelle-fonctionnalite

# 3. Création PR avec CLI
gh pr create --title "Nouvelle fonctionnalité" --body "Description..."

# 4. Surveillance du workflow avec CLI
gh run list
gh run watch  # Surveillance en temps réel

# 5. Une fois les tests passés, merge
gh pr merge --squash
```

## Prérequis pour cette section

Avant de continuer, assurez-vous d'avoir :

- ✅ Un repository avec au moins un workflow GitHub Actions configuré
- ✅ La CLI GitHub installée et configurée
- ✅ Des permissions suffisantes pour visualiser et interagir avec les Actions
- ✅ Compréhension de base des concepts Git et GitHub

> 💡 **Astuce** : Si vous n'avez pas encore de workflow configuré, vous pouvez en créer un simple en ajoutant un fichier `.github/workflows/test.yml` dans votre repository avec le contenu de l'exemple ci-dessus.

## Ce que vous allez apprendre

Dans les sections suivantes, vous maîtriserez :

- **6.1** : Comment visualiser et surveiller vos workflows
- **6.2** : Télécharger et analyser les logs d'exécution
- **6.3** : Déclencher des workflows manuellement
- **6.4** : Débugger efficacement les échecs de CI

Prêt à plonger dans le monde de l'automatisation avec GitHub Actions ? C'est parti ! 🚀


⏭️
