# Exercices Pratiques - Formation CLI GitHub

Cette page regroupe tous les exercices pratiques de la formation, organisés par section. Chaque exercice peut être réalisé indépendamment et inclut les solutions.

## 🎯 Guide d'utilisation

### Niveaux de difficulté
- 🟢 **Débutant** : Exercices de base pour découvrir les commandes
- 🟡 **Intermédiaire** : Exercices combinant plusieurs concepts
- 🔴 **Avancé** : Exercices complexes nécessitant une bonne maîtrise

### Structure des exercices
Chaque exercice contient :
- **Objectif** : Ce que vous allez apprendre
- **Prérequis** : Ce qui doit être en place avant de commencer
- **Étapes** : Instructions détaillées
- **Solution** : Commandes et explications
- **Aller plus loin** : Variantes et améliorations possibles

---

## 📋 Section 1 : Introduction et Installation

### Exercice 1.1 - Installation et première connexion 🟢

**Objectif :** Installer la CLI GitHub et se connecter à son compte

**Prérequis :**
- Compte GitHub actif
- Terminal fonctionnel

**Étapes :**
1. Installer la CLI GitHub sur votre système
2. Vérifier l'installation
3. Se connecter à GitHub
4. Vérifier l'authentification

**Solution :**
```bash
# Installation (exemple Ubuntu)
sudo apt install gh

# Vérification
gh --version

# Connexion
gh auth login

# Vérification
gh auth status
```

**Aller plus loin :**
- Configurer l'authentification SSH
- Tester différents protocoles (HTTPS vs SSH)

### Exercice 1.2 - Configuration personnalisée 🟡

**Objectif :** Personnaliser la configuration de la CLI GitHub

**Étapes :**
1. Changer l'éditeur par défaut vers VS Code
2. Configurer le protocole Git en SSH
3. Définir le navigateur par défaut
4. Lister toutes les configurations

**Solution :**
```bash
# Éditeur par défaut
gh config set editor "code --wait"

# Protocole Git
gh config set git_protocol ssh

# Navigateur par défaut
gh config set browser firefox

# Lister les configurations
gh config list
```

---

## 📋 Section 2 : Authentification et Configuration

### Exercice 2.1 - Gestion des tokens 🟢

**Objectif :** Créer et gérer des tokens d'accès personnels

**Étapes :**
1. Créer un token avec permissions limitées
2. Se connecter avec ce token
3. Tester les permissions
4. Révoquer le token

**Solution :**
```bash
# Connexion avec token
gh auth login --with-token < token.txt

# Vérifier les permissions
gh auth status

# Se déconnecter
gh auth logout
```

### Exercice 2.2 - Configuration multi-comptes 🟡

**Objectif :** Gérer plusieurs comptes GitHub

**Étapes :**
1. Configurer un compte principal
2. Ajouter un compte secondaire
3. Basculer entre les comptes
4. Vérifier le compte actif

**Solution :**
```bash
# Compte principal
gh auth login --hostname github.com

# Compte secondaire (GitHub Enterprise par exemple)
gh auth login --hostname github.enterprise.com

# Basculer
gh auth switch --hostname github.enterprise.com

# Vérifier
gh auth status
```

---

## 📋 Section 3 : Gestion des Repositories

### Exercice 3.1 - Création d'un repository 🟢

**Objectif :** Créer un nouveau repository avec la CLI GitHub

**Étapes :**
1. Créer un repository public nommé "cli-github-test"
2. Ajouter une description
3. Initialiser avec un README
4. Cloner le repository localement

**Solution :**
```bash
# Créer le repository
gh repo create cli-github-test --public --description "Repository de test pour la CLI GitHub" --add-readme

# Cloner
gh repo clone cli-github-test
cd cli-github-test
```

### Exercice 3.2 - Fork et configuration upstream 🟡

**Objectif :** Forker un repository et configurer l'upstream

**Étapes :**
1. Forker le repository "octocat/Hello-World"
2. Cloner votre fork
3. Configurer l'upstream
4. Synchroniser avec l'upstream

**Solution :**
```bash
# Fork
gh repo fork octocat/Hello-World --clone

# Aller dans le dossier
cd Hello-World

# Vérifier les remotes
git remote -v

# Synchroniser
gh repo sync
```

### Exercice 3.3 - Repository privé avec collaborateurs 🟡

**Objectif :** Créer un repository privé et inviter des collaborateurs

**Étapes :**
1. Créer un repository privé "projet-secret"
2. Ajouter un fichier de configuration
3. Inviter un collaborateur
4. Gérer les permissions

**Solution :**
```bash
# Créer repository privé
gh repo create projet-secret --private

# Cloner et ajouter du contenu
gh repo clone projet-secret
cd projet-secret
echo "# Projet Secret" > README.md
git add README.md
git commit -m "Initial commit"
git push

# Inviter collaborateur (remplacer USERNAME)
gh api repos/:owner/:repo/collaborators/USERNAME -X PUT
```

---

## 📋 Section 4 : Workflow avec les Issues

### Exercice 4.1 - Création et gestion d'issues 🟢

**Objectif :** Créer, labelliser et fermer des issues

**Étapes :**
1. Créer une issue "Bug: Login ne fonctionne pas"
2. Ajouter le label "bug"
3. Assigner l'issue à vous-même
4. Fermer l'issue

**Solution :**
```bash
# Dans un repository existant
cd cli-github-test

# Créer issue
gh issue create --title "Bug: Login ne fonctionne pas" --body "Description détaillée du bug"

# Ajouter label
gh issue edit 1 --add-label "bug"

# S'assigner
gh issue edit 1 --add-assignee @me

# Fermer
gh issue close 1
```

### Exercice 4.2 - Issues avec templates 🟡

**Objectif :** Créer des issues en utilisant des templates

**Étapes :**
1. Créer un template d'issue pour les bugs
2. Créer une issue en utilisant ce template
3. Modifier le template
4. Lister toutes les issues

**Solution :**
```bash
# Créer le dossier templates
mkdir -p .github/ISSUE_TEMPLATE

# Créer template bug
cat > .github/ISSUE_TEMPLATE/bug_report.md << EOF
---
name: Bug Report
about: Créer un rapport de bug
title: '[BUG] '
labels: 'bug'
assignees: ''
---

**Décrivez le bug**
Description claire du problème.

**Étapes pour reproduire**
1. Aller à '...'
2. Cliquer sur '....'
3. Faire défiler jusqu'à '....'
4. Voir l'erreur

**Comportement attendu**
Description claire de ce qui devrait se passer.
EOF

# Commit du template
git add .github/
git commit -m "Add bug report template"
git push

# Créer issue avec template
gh issue create --template bug_report.md

# Lister issues
gh issue list
```

### Exercice 4.3 - Workflow complet avec issue 🟡

**Objectif :** Workflow complet : issue → branche → commit → PR

**Étapes :**
1. Créer une issue "Feature: Ajouter page contact"
2. Créer une branche liée à l'issue
3. Faire des commits qui référencent l'issue
4. Créer une PR qui ferme l'issue

**Solution :**
```bash
# Créer issue
gh issue create --title "Feature: Ajouter page contact" --body "Ajouter une page de contact avec formulaire"

# Noter le numéro d'issue (ex: #2)
ISSUE_NUMBER=2

# Créer branche
git checkout -b feature/contact-page

# Créer fichier et commit
echo "<h1>Page Contact</h1>" > contact.html
git add contact.html
git commit -m "Add contact page

Fixes #${ISSUE_NUMBER}"

# Push et créer PR
git push -u origin feature/contact-page
gh pr create --title "Add contact page" --body "Closes #${ISSUE_NUMBER}"
```

---

## 📋 Section 5 : Maîtrise des Pull Requests

### Exercice 5.1 - Première Pull Request 🟢

**Objectif :** Créer, reviewer et merger une Pull Request

**Étapes :**
1. Créer une branche "improve-readme"
2. Modifier le README.md
3. Créer une Pull Request
4. Merger la PR

**Solution :**
```bash
# Créer branche
git checkout -b improve-readme

# Modifier README
echo "## Description améliorée" >> README.md
git add README.md
git commit -m "Improve README with better description"

# Push et créer PR
git push -u origin improve-readme
gh pr create --title "Improve README" --body "Add better description section"

# Merger (si pas de review requise)
gh pr merge --merge
```

### Exercice 5.2 - Pull Request avec review 🟡

**Objectif :** Gérer une PR avec processus de review

**Étapes :**
1. Créer une PR en mode draft
2. Demander une review
3. Appliquer les commentaires
4. Approuver et merger

**Solution :**
```bash
# Créer branche
git checkout -b feature/new-feature

# Ajouter code
echo "console.log('New feature');" > feature.js
git add feature.js
git commit -m "Add new feature"

# Push et créer draft PR
git push -u origin feature/new-feature
gh pr create --title "Add new feature" --body "WIP: Adding new feature" --draft

# Sortir du mode draft
gh pr ready

# Demander review (remplacer USERNAME)
gh pr edit --add-reviewer USERNAME

# Voir les reviews
gh pr view --comments

# Auto-merger si approuvé
gh pr merge --auto --squash
```

### Exercice 5.3 - Gestion des conflits 🔴

**Objectif :** Résoudre des conflits dans une Pull Request

**Étapes :**
1. Créer deux branches qui modifient le même fichier
2. Merger la première
3. Créer PR pour la seconde (conflit)
4. Résoudre le conflit

**Solution :**
```bash
# Branche 1
git checkout main
git checkout -b feature/header-v1
echo "# Mon Super Projet v1" > README.md
git add README.md
git commit -m "Update header v1"
git push -u origin feature/header-v1
gh pr create --title "Header v1" --body "Update header to v1"
gh pr merge --merge

# Branche 2 (va créer conflit)
git checkout main
git pull  # Récupérer les changements
git checkout -b feature/header-v2
echo "# Mon Excellent Projet v2" > README.md
git add README.md
git commit -m "Update header v2"
git push -u origin feature/header-v2
gh pr create --title "Header v2" --body "Update header to v2"

# Résoudre conflit
git checkout feature/header-v2
git rebase main
# Résoudre manuellement le conflit dans l'éditeur
git add README.md
git rebase --continue
git push --force-with-lease

# Merger
gh pr merge --squash
```

---

## 📋 Section 6 : GitHub Actions et CI/CD

### Exercice 6.1 - Monitoring des workflows 🟢

**Objectif :** Surveiller l'exécution des GitHub Actions

**Étapes :**
1. Lister tous les workflows
2. Voir les détails d'une exécution
3. Télécharger les logs
4. Relancer une exécution échouée

**Solution :**
```bash
# Lister les workflows
gh run list

# Voir détails d'une exécution
gh run view [RUN_ID]

# Télécharger logs
gh run download [RUN_ID]

# Relancer si échec
gh run rerun [RUN_ID]

# Suivre en temps réel
gh run watch [RUN_ID]
```

### Exercice 6.2 - Workflow simple avec CLI 🟡

**Objectif :** Créer et déclencher un workflow simple

**Étapes :**
1. Créer un workflow de test simple
2. Le déclencher manuellement
3. Analyser les résultats
4. Modifier et relancer

**Solution :**
```bash
# Créer le dossier workflows
mkdir -p .github/workflows

# Créer workflow simple
cat > .github/workflows/hello.yml << EOF
name: Hello World
on:
  workflow_dispatch:
  push:
    branches: [ main ]

jobs:
  hello:
    runs-on: ubuntu-latest
    steps:
      - name: Hello
        run: echo "Hello from GitHub Actions!"

      - name: Show date
        run: date
EOF

# Commit et push
git add .github/
git commit -m "Add hello world workflow"
git push

# Déclencher manuellement
gh workflow run hello.yml

# Voir les exécutions
gh run list --workflow=hello.yml
```

### Exercice 6.3 - Debugging d'un workflow 🔴

**Objectif :** Diagnostiquer et corriger un workflow échoué

**Étapes :**
1. Créer un workflow qui échoue intentionnellement
2. Analyser les logs d'erreur
3. Corriger le problème
4. Vérifier la correction

**Solution :**
```bash
# Workflow avec erreur intentionnelle
cat > .github/workflows/test-broken.yml << EOF
name: Test Broken
on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run broken command
        run: |
          echo "Starting test..."
          non_existent_command
          echo "This won't execute"
EOF

# Commit
git add .github/workflows/test-broken.yml
git commit -m "Add broken workflow"
git push

# Attendre l'échec et analyser
gh run list
gh run view --log [FAILED_RUN_ID]

# Corriger
cat > .github/workflows/test-broken.yml << EOF
name: Test Fixed
on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run working command
        run: |
          echo "Starting test..."
          echo "Test completed successfully!"
EOF

# Commit correction
git add .github/workflows/test-broken.yml
git commit -m "Fix broken workflow"
git push
```

---

## 📋 Section 7 : Gestion des Releases

### Exercice 7.1 - Première release 🟢

**Objectif :** Créer sa première release avec assets

**Étapes :**
1. Préparer des fichiers pour la release
2. Créer une release v1.0.0
3. Attacher des assets
4. Publier la release

**Solution :**
```bash
# Préparer des assets
echo "Application version 1.0.0" > app.txt
zip app-v1.0.0.zip app.txt

# Créer release avec assets
gh release create v1.0.0 \
  --title "Version 1.0.0" \
  --notes "Première version stable de l'application" \
  app-v1.0.0.zip

# Lister les releases
gh release list

# Voir détails
gh release view v1.0.0
```

### Exercice 7.2 - Release avec notes automatiques 🟡

**Objectif :** Générer des notes de release automatiquement

**Étapes :**
1. Faire plusieurs commits avec des messages structurés
2. Créer une release avec génération automatique des notes
3. Modifier les notes après création
4. Marquer comme pre-release

**Solution :**
```bash
# Commits structurés
echo "Feature 1" > feature1.txt
git add feature1.txt
git commit -m "feat: add feature 1"

echo "Feature 2" > feature2.txt
git add feature2.txt
git commit -m "feat: add feature 2"

echo "Bugfix" > bugfix.txt
git add bugfix.txt
git commit -m "fix: correct major bug"

git push

# Release avec notes auto
gh release create v1.1.0 \
  --title "Version 1.1.0" \
  --generate-notes

# Modifier en pre-release
gh release edit v1.1.0 --prerelease

# Voir différence entre releases
gh release view v1.0.0
gh release view v1.1.0
```

### Exercice 7.3 - Workflow de release automatisé 🔴

**Objectif :** Automatiser les releases avec GitHub Actions

**Étapes :**
1. Créer un workflow de release automatique
2. Déclencher par tag
3. Générer des artifacts
4. Publier automatiquement

**Solution :**
```bash
# Workflow de release automatique
cat > .github/workflows/release.yml << EOF
name: Release
on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v3

      - name: Build artifacts
        run: |
          mkdir dist
          echo "Built app version \${GITHUB_REF#refs/tags/}" > dist/app.txt
          tar -czf dist/app.tar.gz dist/app.txt

      - name: Create Release
        uses: softprops/action-gh-release@v1
        with:
          files: dist/app.tar.gz
          generate_release_notes: true
EOF

# Commit workflow
git add .github/workflows/release.yml
git commit -m "Add automated release workflow"
git push

# Créer tag pour déclencher
git tag v1.2.0
git push origin v1.2.0

# Vérifier la release automatique
sleep 30
gh release list
```

---

## 📋 Section 8 : Collaboration Avancée

### Exercice 8.1 - Gestion d'équipe 🟡

**Objectif :** Organiser la collaboration en équipe

**Étapes :**
1. Créer un repository d'équipe
2. Configurer les templates
3. Mettre en place des règles de protection
4. Organiser avec des projets

**Solution :**
```bash
# Créer repo équipe
gh repo create projet-equipe --public

# Cloner et setup
gh repo clone projet-equipe
cd projet-equipe

# Templates d'issue
mkdir -p .github/ISSUE_TEMPLATE
cat > .github/ISSUE_TEMPLATE/feature.md << EOF
---
name: Feature Request
about: Proposer une nouvelle fonctionnalité
title: '[FEATURE] '
labels: 'enhancement'
---

**Description de la fonctionnalité**
Description claire de la fonctionnalité demandée.

**Motivation**
Pourquoi cette fonctionnalité est-elle nécessaire ?
EOF

# Template de PR
cat > .github/pull_request_template.md << EOF
## Description
Décrivez les changements de cette PR.

## Type de changement
- [ ] Bug fix
- [ ] Nouvelle fonctionnalité
- [ ] Breaking change
- [ ] Documentation

## Checklist
- [ ] Tests ajoutés/modifiés
- [ ] Documentation mise à jour
- [ ] Code reviewé
EOF

# Commit templates
git add .github/
git commit -m "Add collaboration templates"
git push
```

### Exercice 8.2 - Workflow avec projets GitHub 🔴

**Objectif :** Utiliser les projets GitHub pour organiser le travail

**Étapes :**
1. Créer un projet GitHub
2. Ajouter des issues au projet
3. Organiser en colonnes
4. Automatiser les mouvements

**Solution :**
```bash
# Créer projet (via API car pas encore en CLI)
gh api graphql -f query='
  mutation {
    createProjectV2(input: {
      ownerId: "USER_ID"
      title: "Projet Équipe"
    }) {
      projectV2 {
        id
        title
      }
    }
  }'

# Créer issues pour le projet
gh issue create --title "Setup: Configuration initiale" --label "setup"
gh issue create --title "Feature: Page d'accueil" --label "enhancement"
gh issue create --title "Bug: Erreur 404" --label "bug"

# Lister issues
gh issue list

# Les ajouter au projet se fait via l'interface web
echo "Ajouter les issues au projet via l'interface GitHub Projects"
```

---

## 📋 Section 9 : Intégration avec Git

### Exercice 9.1 - Alias et shortcuts 🟢

**Objectif :** Créer des alias pour optimiser le workflow

**Étapes :**
1. Créer des alias Git utiles
2. Créer des alias shell pour la CLI GitHub
3. Tester les raccourcis
4. Documenter les alias

**Solution :**
```bash
# Alias Git
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'

# Alias shell (ajouter au .bashrc ou .zshrc)
echo 'alias ghs="gh status"' >> ~/.bashrc
echo 'alias ghpr="gh pr create"' >> ~/.bashrc
echo 'alias ghprs="gh pr list"' >> ~/.bashrc
echo 'alias ghis="gh issue list"' >> ~/.bashrc

# Recharger
source ~/.bashrc

# Tester
ghs
git st
```

### Exercice 9.2 - Workflow complet optimisé 🟡

**Objectif :** Implémenter un workflow complet optimisé

**Étapes :**
1. Créer un script de nouveau feature
2. Script de mise à jour des branches
3. Script de nettoyage
4. Tester le workflow complet

**Solution :**
```bash
# Script nouvelle feature
cat > new-feature.sh << 'EOF'
#!/bin/bash
if [ -z "$1" ]; then
  echo "Usage: ./new-feature.sh <feature-name>"
  exit 1
fi

FEATURE_NAME=$1
BRANCH_NAME="feature/$FEATURE_NAME"

echo "🚀 Création nouvelle feature: $FEATURE_NAME"

# Mise à jour main
git checkout main
git pull origin main

# Nouvelle branche
git checkout -b $BRANCH_NAME

# Commit initial
echo "# Feature: $FEATURE_NAME" > "feature-$FEATURE_NAME.md"
git add .
git commit -m "feat: init $FEATURE_NAME"

# Push et PR draft
git push -u origin $BRANCH_NAME
gh pr create --title "Feature: $FEATURE_NAME" --body "Work in progress for $FEATURE_NAME" --draft

echo "✅ Feature branch créée et PR draft ouverte"
EOF

chmod +x new-feature.sh

# Script de mise à jour
cat > update-branch.sh << 'EOF'
#!/bin/bash
echo "🔄 Mise à jour de la branche courante"

CURRENT_BRANCH=$(git branch --show-current)
if [ "$CURRENT_BRANCH" = "main" ]; then
  echo "Déjà sur main, pull simple"
  git pull origin main
else
  echo "Rebase de $CURRENT_BRANCH sur main"
  git fetch origin main
  git rebase origin/main
fi

echo "✅ Branche mise à jour"
EOF

chmod +x update-branch.sh

# Tester
./new-feature.sh test-workflow
```

### Exercice 9.3 - Hooks Git avec CLI GitHub 🔴

**Objectif :** Automatiser avec des hooks Git

**Étapes :**
1. Créer un hook pre-commit pour vérifier le format
2. Hook post-commit pour créer des issues
3. Hook pre-push pour vérifier les tests
4. Tester tous les hooks

**Solution :**
```bash
# Hook pre-commit
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
echo "🔍 Vérification pre-commit..."

# Vérifier format des messages de commit
if git diff --cached --name-only | grep -E "\.(js|py|md)$"; then
  echo "✅ Fichiers détectés pour vérification"
fi

# Vérifier pas de console.log (exemple)
if git diff --cached | grep -E "console\.log"; then
  echo "❌ console.log détecté, veuillez les supprimer"
  exit 1
fi

echo "✅ Pre-commit OK"
EOF

chmod +x .git/hooks/pre-commit

# Hook post-commit
cat > .git/hooks/post-commit << 'EOF'
#!/bin/bash
COMMIT_MSG=$(git log -1 --pretty=%B)

# Si commit contient "TODO", créer une issue
if echo "$COMMIT_MSG" | grep -i "TODO"; then
  echo "📝 TODO détecté, création d'une issue..."
  TODO_TEXT=$(echo "$COMMIT_MSG" | grep -i "TODO")
  gh issue create --title "TODO: $TODO_TEXT" --body "Créé automatiquement depuis le commit $(git rev-parse HEAD)"
fi
EOF

chmod +x .git/hooks/post-commit

# Tester
echo "// TODO: refactor this function" > test.js
git add test.js
git commit -m "Add test function - TODO: refactor this function"
```

---

## 📋 Section 10 : Automatisation et Scripts

### Exercice 10.1 - Script de synchronisation 🟡

**Objectif :** Automatiser la synchronisation de repositories

**Étapes :**
1. Script pour synchroniser tous les forks
2. Script de backup des repositories
3. Script de nettoyage des branches
4. Automatiser avec cron

**Solution :**
```bash
# Script de synchronisation des forks
cat > sync-forks.sh << 'EOF'
#!/bin/bash
echo "🔄 Synchronisation des forks..."

# Lister tous les repos forkés
gh repo list --fork --json name,owner | jq -r '.[] | "\(.owner.login)/\(.name)"' | while read repo; do
  echo "Synchronisation de $repo"
  gh repo sync $repo
done

echo "✅ Tous les forks synchronisés"
EOF

chmod +x sync-forks.sh

# Script de backup
cat > backup-repos.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="github-backup-$(date +%Y%m%d)"
mkdir -p $BACKUP_DIR

echo "💾 Backup des repositories dans $BACKUP_DIR"

gh repo list --json name | jq -r '.[].name' | while read repo; do
  echo "Backup de $repo"
  gh repo clone $repo $BACKUP_DIR/$repo
done

tar -czf $BACKUP_DIR.tar.gz $BACKUP_DIR
rm -rf $BACKUP_DIR

echo "✅ Backup créé: $BACKUP_DIR.tar.gz"
EOF

chmod +x backup-repos.sh

# Script nettoyage branches
cat > clean-branches.sh << 'EOF'
#!/bin/bash
echo "🧹 Nettoyage des branches mergées"

# Branches locales mergées
git branch --merged main | grep -v "main\|master" | xargs -n 1 git branch -d

# Branches distantes supprimées
git remote prune origin

echo "✅ Nettoyage terminé"
EOF

chmod +x clean-branches.sh

# Tester les scripts
./sync-forks.sh
./clean-branches.sh
```

### Exercice 10.2 - Dashboard automatisé 🔴

**Objectif :** Créer un dashboard de suivi automatisé

**Étapes :**
1. Script de génération de rapport d'activité
2. Automatiser l'envoi de notifications
3. Générer des métriques de productivité
4. Sauvegarder l'historique

**Solution :**
```bash
# Script de dashboard
cat > dashboard.sh << 'EOF'
#!/bin/bash
REPORT_FILE="github-dashboard-$(date +%Y%m%d).md"

echo "📊 Génération du dashboard GitHub..."

cat > $REPORT_FILE << EODASH
# Dashboard GitHub - $(date +%Y-%m-%d)

## Résumé de l'activité

### Repositories
EODASH

# Statistiques repositories
echo "- Total repositories: $(gh repo list --json name | jq length)" >> $REPORT_FILE
echo "- Repositories publics: $(gh repo list --public --json name | jq length)" >> $REPORT_FILE
echo "- Repositories privés: $(gh repo list --private --json name | jq length)" >> $REPORT_FILE

cat >> $REPORT_FILE << EODASH

### Issues et PRs (7 derniers jours)
EODASH

# Issues récentes
echo "- Issues créées: $(gh issue list --state all --created "$(date -d '7 days ago' +%Y-%m-%d)" --json number | jq length)" >> $REPORT_FILE
echo "- PRs créées: $(gh pr list --state all --created "$(date -d '7 days ago' +%Y-%m-%d)" --json number | jq length)" >> $REPORT_FILE

cat >> $REPORT_FILE << EODASH

### Top repositories par activité
EODASH

# Top repos (simplifié)
gh repo list --json name,updatedAt | jq -r '.[] | "\(.name) - \(.updatedAt)"' | head -5 >> $REPORT_FILE

echo "✅ Dashboard généré: $REPORT_FILE"

# Optionnel: créer une issue avec le rapport
gh issue create --title "Dashboard $(date +%Y-%m-%d)" --body "$(cat $REPORT_FILE)"
EOF

chmod +x dashboard.sh
./dashboard.sh
```

---

## 📋 Section 11 : Extensions et Personnalisation

### Exercice 11.1 - Installation et test d'extensions 🟢

**Objectif :** Installer et utiliser des extensions populaires

**Étapes :**
1. Lister les extensions disponibles
2. Installer gh-dash pour un dashboard interactif
3. Installer gh-copilot pour l'aide IA
4. Tester les nouvelles fonctionnalités

**Solution :**
```bash
# Lister extensions disponibles
gh extension browse

# Installer gh-dash
gh extension install dlvhdr/gh-dash

# Installer gh-copilot (si disponible)
gh extension install github/gh-copilot

# Lister extensions installées
gh extension list

# Tester gh-dash
gh dash

# Tester aide avec copilot
gh copilot suggest "create a new repository"
gh copilot explain "git rebase -i HEAD~3"
```

### Exercice 11.2 - Créer sa première extension 🟡

**Objectif :** Développer une extension CLI GitHub personnalisée

**Étapes :**
1. Créer une extension simple "gh hello"
2. La tester localement
3. La publier sur GitHub
4. L'installer depuis le repository

**Solution :**
```bash
# Créer le repository d'extension
gh repo create gh-hello --public
gh repo clone gh-hello
cd gh-hello

# Créer l'extension bash
cat > gh-hello << 'EOF'
#!/bin/bash
set -e

# Extension gh-hello - Salutation personnalisée

case "$1" in
  "" | "-h" | "--help")
    echo "Usage: gh hello [nom]"
    echo "Affiche une salutation personnalisée"
    echo ""
    echo "Options:"
    echo "  -h, --help     Affiche cette aide"
    echo "  --version      Affiche la version"
    exit 0
    ;;
  "--version")
    echo "gh-hello version 1.0.0"
    exit 0
    ;;
  *)
    NAME=$1
    USER=$(gh api user --jq .login)
    echo "👋 Salut $NAME !"
    echo "Tu es connecté en tant que $USER sur GitHub"

    # Statistiques rapides
    REPOS=$(gh repo list --json name | jq length)
    echo "📊 Tu as $REPOS repositories"
    ;;
esac
EOF

chmod +x gh-hello

# Tester localement
./gh-hello "Développeur"

# Créer README
cat > README.md << EOF
# gh-hello

Extension CLI GitHub pour saluer les utilisateurs.

## Installation

\`\`\`bash
gh extension install $(gh api user --jq .login)/gh-hello
\`\`\`

## Usage

\`\`\`bash
gh hello "Votre Nom"
\`\`\`
EOF

# Publier
git add .
git commit -m "Initial release of gh-hello extension"
git push

# Installer depuis le repo
gh extension install $(gh api user --jq .login)/gh-hello

# Tester l'extension installée
gh hello "World"
```

### Exercice 11.3 - Extension avancée avec configuration 🔴

**Objectif :** Créer une extension sophistiquée avec fichier de config

**Étapes :**
1. Créer "gh-stats" pour des statistiques personnalisées
2. Ajouter un fichier de configuration
3. Implémenter plusieurs sous-commandes
4. Ajouter la gestion d'erreurs

**Solution :**
```bash
# Nouveau repository pour l'extension avancée
gh repo create gh-stats --public
gh repo clone gh-stats
cd gh-stats

# Structure de l'extension
mkdir -p bin config

# Script principal
cat > bin/gh-stats << 'EOF'
#!/bin/bash
set -e

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
CONFIG_DIR="$HOME/.config/gh-stats"
CONFIG_FILE="$CONFIG_DIR/config.json"

# Créer config par défaut si inexistant
create_default_config() {
  mkdir -p "$CONFIG_DIR"
  cat > "$CONFIG_FILE" << EOJSON
{
  "default_timeframe": "30d",
  "include_private": false,
  "preferred_format": "table"
}
EOJSON
}

# Charger configuration
load_config() {
  if [ ! -f "$CONFIG_FILE" ]; then
    create_default_config
  fi

  TIMEFRAME=$(jq -r '.default_timeframe' "$CONFIG_FILE")
  INCLUDE_PRIVATE=$(jq -r '.include_private' "$CONFIG_FILE")
  FORMAT=$(jq -r '.preferred_format' "$CONFIG_FILE")
}

# Sous-commande: repositories
cmd_repos() {
  echo "📊 Statistiques des repositories"
  echo "================================"

  TOTAL=$(gh repo list --json name | jq length)
  PUBLIC=$(gh repo list --public --json name | jq length)
  PRIVATE=$(gh repo list --private --json name | jq length)

  if [ "$FORMAT" = "json" ]; then
    jq -n --arg total "$TOTAL" --arg public "$PUBLIC" --arg private "$PRIVATE" \
      '{total: $total, public: $public, private: $private}'
  else
    printf "%-15s %s\n" "Total:" "$TOTAL"
    printf "%-15s %s\n" "Publics:" "$PUBLIC"
    printf "%-15s %s\n" "Privés:" "$PRIVATE"
  fi
}

# Sous-commande: activity
cmd_activity() {
  echo "📈 Activité récente ($TIMEFRAME)"
  echo "==============================="

  # Calcul de la date limite
  if command -v gdate >/dev/null 2>&1; then
    DATE_CMD=gdate  # macOS
  else
    DATE_CMD=date   # Linux
  fi

  DAYS=$(echo "$TIMEFRAME" | sed 's/d//')
  SINCE=$($DATE_CMD -d "$DAYS days ago" +%Y-%m-%d 2>/dev/null || $DATE_CMD -v-"$DAYS"d +%Y-%m-%d)

  # Issues créées
  ISSUES_CREATED=$(gh search issues --author=@me --created=">$SINCE" --json number | jq length)

  # PRs créées
  PRS_CREATED=$(gh search prs --author=@me --created=">$SINCE" --json number | jq length)

  if [ "$FORMAT" = "json" ]; then
    jq -n --arg issues "$ISSUES_CREATED" --arg prs "$PRS_CREATED" --arg since "$SINCE" \
      '{issues_created: $issues, prs_created: $prs, since: $since}'
  else
    printf "%-20s %s\n" "Issues créées:" "$ISSUES_CREATED"
    printf "%-20s %s\n" "PRs créées:" "$PRS_CREATED"
    printf "%-20s %s\n" "Depuis le:" "$SINCE"
  fi
}

# Sous-commande: config
cmd_config() {
  case "$1" in
    "set")
      if [ -z "$2" ] || [ -z "$3" ]; then
        echo "Usage: gh stats config set <key> <value>"
        exit 1
      fi

      # Mettre à jour la config
      jq --arg key "$2" --arg value "$3" '.[$key] = $value' "$CONFIG_FILE" > tmp.$.json
      mv tmp.$.json "$CONFIG_FILE"
      echo "✅ Configuration mise à jour: $2 = $3"
      ;;
    "get")
      if [ -z "$2" ]; then
        cat "$CONFIG_FILE"
      else
        jq -r --arg key "$2" '.[$key]' "$CONFIG_FILE"
      fi
      ;;
    "reset")
      create_default_config
      echo "✅ Configuration réinitialisée"
      ;;
    *)
      echo "Usage: gh stats config [set|get|reset]"
      ;;
  esac
}

# Menu principal
case "$1" in
  "" | "-h" | "--help")
    cat << EOF
gh-stats - Statistiques GitHub personnalisées

Usage:
  gh stats repos              Statistiques des repositories
  gh stats activity           Activité récente
  gh stats config <cmd>       Gestion de la configuration

Options:
  -h, --help                  Affiche cette aide
  --version                   Affiche la version

Configuration:
  gh stats config set default_timeframe "7d"
  gh stats config set preferred_format "json"
EOF
    ;;
  "--version")
    echo "gh-stats version 1.0.0"
    ;;
  "repos")
    load_config
    cmd_repos
    ;;
  "activity")
    load_config
    cmd_activity
    ;;
  "config")
    shift
    cmd_config "$@"
    ;;
  *)
    echo "Commande inconnue: $1"
    echo "Utilisez 'gh stats --help' pour voir les commandes disponibles"
    exit 1
    ;;
esac
EOF

chmod +x bin/gh-stats

# Lien symbolique pour gh
ln -sf bin/gh-stats gh-stats

# README complet
cat > README.md << EOF
# gh-stats

Extension CLI GitHub avancée pour des statistiques personnalisées.

## Installation

\`\`\`bash
gh extension install $(gh api user --jq .login)/gh-stats
\`\`\`

## Usage

### Statistiques des repositories
\`\`\`bash
gh stats repos
\`\`\`

### Activité récente
\`\`\`bash
gh stats activity
\`\`\`

### Configuration
\`\`\`bash
# Voir la configuration
gh stats config get

# Modifier la période par défaut
gh stats config set default_timeframe "7d"

# Changer le format de sortie
gh stats config set preferred_format "json"

# Réinitialiser
gh stats config reset
\`\`\`

## Configuration

Le fichier de configuration est stocké dans \`~/.config/gh-stats/config.json\`.

Options disponibles:
- \`default_timeframe\`: Période par défaut (ex: "7d", "30d")
- \`include_private\`: Inclure les repos privés (true/false)
- \`preferred_format\`: Format de sortie ("table" ou "json")
EOF

# Publier
git add .
git commit -m "Add advanced gh-stats extension with configuration"
git push

# Installer et tester
gh extension install $(gh api user --jq .login)/gh-stats

gh stats repos
gh stats activity
gh stats config set default_timeframe "7d"
gh stats activity
```

---

## 📋 Section 12 : Dépannage et Bonnes Pratiques

### Exercice 12.1 - Diagnostic et résolution d'erreurs 🟢

**Objectif :** Identifier et résoudre les erreurs courantes

**Étapes :**
1. Simuler des erreurs d'authentification
2. Résoudre des problèmes de permissions
3. Corriger des erreurs de configuration
4. Documenter les solutions

**Solution :**
```bash
# Script de diagnostic
cat > diagnose.sh << 'EOF'
#!/bin/bash
echo "🔍 Diagnostic CLI GitHub"
echo "========================"

# Vérifier l'installation
if command -v gh &> /dev/null; then
    echo "✅ CLI GitHub installée: $(gh --version)"
else
    echo "❌ CLI GitHub non installée"
    exit 1
fi

# Vérifier l'authentification
if gh auth status &> /dev/null; then
    echo "✅ Authentification: OK"
    gh auth status
else
    echo "❌ Problème d'authentification"
    echo "💡 Solution: gh auth login"
fi

# Vérifier Git
if command -v git &> /dev/null; then
    echo "✅ Git installé: $(git --version)"

    # Vérifier la configuration Git
    if git config user.name &> /dev/null && git config user.email &> /dev/null; then
        echo "✅ Git configuré"
    else
        echo "⚠️  Git non configuré"
        echo "💡 Solution: git config --global user.name 'Nom'"
        echo "💡 Solution: git config --global user.email 'email@example.com'"
    fi
else
    echo "❌ Git non installé"
fi

# Vérifier la connectivité
if curl -s --max-time 5 https://api.github.com &> /dev/null; then
    echo "✅ Connexion GitHub API: OK"
else
    echo "❌ Problème de connexion à GitHub"
    echo "💡 Vérifiez votre connexion internet"
fi

# Vérifier les permissions dans le repo actuel
if git rev-parse --git-dir &> /dev/null; then
    echo "✅ Dans un repository Git"

    ORIGIN=$(git remote get-url origin 2>/dev/null || echo "")
    if [[ $ORIGIN == *"github.com"* ]]; then
        echo "✅ Repository GitHub détecté"

        # Test permissions
        if gh repo view &> /dev/null; then
            echo "✅ Permissions lecture: OK"
        else
            echo "❌ Problème de permissions"
            echo "💡 Vérifiez que vous avez accès à ce repository"
        fi
    else
        echo "ℹ️  Repository non-GitHub ou pas de remote origin"
    fi
else
    echo "ℹ️  Pas dans un repository Git"
fi

echo ""
echo "🎯 Diagnostic terminé"
EOF

chmod +x diagnose.sh
./diagnose.sh

# Simuler et résoudre erreur d'auth
echo "🧪 Test résolution erreur d'authentification"
gh auth logout
gh repo list  # Doit échouer

echo "🔧 Résolution..."
gh auth login
gh repo list  # Doit fonctionner
```

### Exercice 12.2 - Optimisation des performances 🟡

**Objectif :** Optimiser l'utilisation de la CLI GitHub

**Étapes :**
1. Mesurer les temps de réponse
2. Optimiser les requêtes
3. Mettre en cache les résultats
4. Créer des raccourcis efficaces

**Solution :**
```bash
# Script de benchmark
cat > benchmark.sh << 'EOF'
#!/bin/bash
echo "⏱️  Benchmark CLI GitHub"
echo "======================="

# Fonction pour mesurer le temps
time_command() {
    local cmd="$1"
    local desc="$2"

    echo -n "Testing $desc... "
    start_time=$(date +%s.%N)
    eval "$cmd" > /dev/null 2>&1
    end_time=$(date +%s.%N)

    duration=$(echo "$end_time - $start_time" | bc)
    printf "%.3fs\n" "$duration"
}

# Tests de performance
time_command "gh repo list" "Liste repositories"
time_command "gh issue list" "Liste issues"
time_command "gh pr list" "Liste PRs"
time_command "gh api user" "API user"

echo ""
echo "💡 Optimisations suggérées:"
echo "- Utiliser --limit pour limiter les résultats"
echo "- Utiliser --json pour des formats plus rapides"
echo "- Mettre en cache avec des scripts personnalisés"
EOF

chmod +x benchmark.sh
./benchmark.sh

# Script de cache simple
cat > cached-stats.sh << 'EOF'
#!/bin/bash
CACHE_DIR="$HOME/.cache/gh-stats"
CACHE_FILE="$CACHE_DIR/repos.json"
CACHE_DURATION=3600  # 1 heure en secondes

mkdir -p "$CACHE_DIR"

# Vérifier si cache valide
if [ -f "$CACHE_FILE" ]; then
    CACHE_AGE=$(($(date +%s) - $(stat -c %Y "$CACHE_FILE" 2>/dev/null || stat -f %m "$CACHE_FILE")))
    if [ $CACHE_AGE -lt $CACHE_DURATION ]; then
        echo "📋 Utilisation du cache (age: ${CACHE_AGE}s)"
        cat "$CACHE_FILE"
        exit 0
    fi
fi

echo "🔄 Mise à jour du cache..."
gh repo list --json name,description,updatedAt > "$CACHE_FILE"
cat "$CACHE_FILE"
EOF

chmod +x cached-stats.sh
./cached-stats.sh
```

### Exercice 12.3 - Sécurité et bonnes pratiques 🔴

**Objectif :** Implémenter les meilleures pratiques de sécurité

**Étapes :**
1. Auditer la configuration de sécurité
2. Configurer l'authentification 2FA
3. Gérer les tokens avec les bonnes permissions
4. Créer un guide de sécurité

**Solution :**
```bash
# Script d'audit sécurité
cat > security-audit.sh << 'EOF'
#!/bin/bash
echo "🔒 Audit de Sécurité GitHub CLI"
echo "================================"

# Vérifier le type d'authentification
AUTH_TYPE=$(gh auth status 2>&1 | grep -o 'Logged in.*' | head -1)
echo "🔐 Authentification: $AUTH_TYPE"

# Vérifier 2FA
if gh api user | jq -r '.two_factor_authentication' | grep -q true; then
    echo "✅ 2FA activé"
else
    echo "⚠️  2FA non activé - RECOMMANDÉ"
    echo "💡 Activez 2FA sur https://github.com/settings/security"
fi

# Vérifier les permissions des tokens
echo ""
echo "🎫 Permissions des tokens:"
gh auth status --show-token 2>/dev/null | grep -A 20 "Token:" || echo "Impossible d'afficher le token (normal)"

# Vérifier les clés SSH
echo ""
echo "🔑 Clés SSH:"
gh ssh-key list

# Repos avec accès admin
echo ""
echo "👑 Repositories avec accès admin:"
gh repo list --json name,permissions | jq -r '.[] | select(.permissions.admin == true) | .name' | head -5

# Dernières connexions (via API)
echo ""
echo "📊 Informations de sécurité du compte:"
gh api user | jq '{login, created_at, updated_at, two_factor_authentication}'

# Recommendations
echo ""
echo "📋 Recommandations de sécurité:"
echo "1. ✅ Activez 2FA si pas déjà fait"
echo "2. ✅ Utilisez des tokens avec permissions minimales"
echo "3. ✅ Révoquez les tokens inutilisés régulièrement"
echo "4. ✅ Utilisez SSH au lieu de HTTPS quand possible"
echo "5. ✅ Auditez régulièrement vos accès"
EOF

chmod +x security-audit.sh
./security-audit.sh

# Guide de bonnes pratiques
cat > SECURITY-BEST-PRACTICES.md << 'EOF'
# Guide de Sécurité - CLI GitHub

## 🔒 Authentification

### Tokens d'accès personnels
- **Principe du moindre privilège** : N'accordez que les permissions nécessaires
- **Rotation régulière** : Renouvelez vos tokens tous les 90 jours
- **Stockage sécurisé** : Ne stockez jamais les tokens en plain text

```bash
# Créer un token avec permissions limitées
gh auth login --scopes "repo,read:user"

# Vérifier les permissions
gh auth status
```

### Authentification à deux facteurs
```bash
# Vérifier le statut 2FA
gh api user | jq '.two_factor_authentication'
```

## 🔑 Gestion des clés SSH

### Génération de clés
```bash
# Générer nouvelle clé ED25519 (recommandé)
ssh-keygen -t ed25519 -C "votre.email@example.com"

# Ajouter à GitHub
gh ssh-key add ~/.ssh/id_ed25519.pub --title "Mon ordinateur"
```

### Audit des clés
```bash
# Lister les clés
gh ssh-key list

# Supprimer les clés inutilisées
gh ssh-key delete [KEY_ID]
```

## 🛡️ Bonnes pratiques de repository

### Branches protégées
```bash
# Protéger la branche main
gh api repos/:owner/:repo/branches/main/protection \
  --method PUT \
  --input protection.json
```

### Secrets et variables
- Utilisez GitHub Secrets pour les données sensibles
- Ne commitez jamais de tokens ou mots de passe
- Utilisez des outils comme git-secrets pour la détection

### Audit régulier
```bash
# Script d'audit mensuel
#!/bin/bash
echo "Audit de sécurité - $(date)"
gh ssh-key list
gh auth status
gh repo list --json name,private | jq '.[] | select(.private == false)'
```

## 🔍 Monitoring et alertes

### Surveillance des accès
- Activez les notifications pour les nouvelles connexions
- Surveillez l'activité inhabituelle
- Utilisez les logs d'audit GitHub

### Alertes de sécurité
```bash
# Vérifier les alertes de sécurité
gh api repos/:owner/:repo/vulnerability-alerts
```

## 🚨 En cas de compromission

1. **Changez immédiatement tous les mots de passe**
2. **Révoquez tous les tokens d'accès**
3. **Régénérez les clés SSH**
4. **Auditez l'historique des commits**
5. **Contactez le support GitHub si nécessaire**

```bash
# Révocation d'urgence
gh auth logout
gh ssh-key delete --all  # ⚠️ ATTENTION: supprime toutes les clés
```
EOF

echo "✅ Guide de sécurité créé: SECURITY-BEST-PRACTICES.md"
```

---

## 🎯 Projets Finaux Intégrés

### Projet Final 1 - Workflow Complet d'Équipe 🔴

**Objectif :** Mettre en place un workflow complet pour une équipe de développement

**Scenario :** Vous devez configurer un projet pour une équipe de 5 développeurs travaillant sur une application web.

**Étapes :**
1. Créer le repository avec structure complète
2. Configurer les templates et workflows
3. Mettre en place la CI/CD
4. Créer les scripts d'automatisation
5. Documenter le processus

**Solution complète :**
```bash
# 1. Setup initial du projet
gh repo create projet-equipe-final --public --description "Projet final - Workflow équipe complet"
gh repo clone projet-equipe-final
cd projet-equipe-final

# 2. Structure du projet
mkdir -p {src,tests,docs,.github/{ISSUE_TEMPLATE,workflows}}

# 3. Templates
cat > .github/ISSUE_TEMPLATE/bug_report.yml << 'EOF'
name: 🐛 Bug Report
description: Signaler un bug dans l'application
title: "[BUG] "
labels: ["bug", "needs-triage"]
body:
  - type: markdown
    attributes:
      value: |
        Merci de prendre le temps de signaler ce bug !

  - type: textarea
    id: description
    attributes:
      label: Description du bug
      description: Description claire et concise du problème
      placeholder: Le bug se produit quand...
    validations:
      required: true

  - type: textarea
    id: reproduction
    attributes:
      label: Étapes pour reproduire
      description: Comment reproduire le problème
      placeholder: |
        1. Aller à '...'
        2. Cliquer sur '...'
        3. Voir l'erreur
    validations:
      required: true

  - type: dropdown
    id: priority
    attributes:
      label: Priorité
      options:
        - Basse
        - Moyenne
        - Haute
        - Critique
    validations:
      required: true
EOF

# 4. Workflow CI/CD complet
cat > .github/workflows/ci-cd.yml << 'EOF'
name: CI/CD Pipeline

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
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Run linting
        run: npm run lint

      - name: Security audit
        run: npm audit

  build:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v3

      - name: Build application
        run: |
          echo "Building application..."
          mkdir -p dist
          echo "Built app $(date)" > dist/app.txt

      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-artifacts
          path: dist/

  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - name: Deploy to production
        run: echo "Deploying to production..."
EOF

# 5. Scripts d'automatisation équipe
cat > scripts/team-workflow.sh << 'EOF'
#!/bin/bash
# Script de workflow équipe

set -e

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
PROJECT_ROOT="$(dirname "$SCRIPT_DIR")"

# Couleurs
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# Fonctions utilitaires
log_info() { echo -e "${BLUE}ℹ️  $1${NC}"; }
log_success() { echo -e "${GREEN}✅ $1${NC}"; }
log_warning() { echo -e "${YELLOW}⚠️  $1${NC}"; }
log_error() { echo -e "${RED}❌ $1${NC}"; }

# Commande: nouvelle feature
cmd_new_feature() {
    local feature_name="$1"
    if [ -z "$feature_name" ]; then
        log_error "Usage: $0 new-feature <nom-feature>"
        exit 1
    fi

    log_info "Création de la feature: $feature_name"

    # Vérifier qu'on est sur main/develop
    current_branch=$(git branch --show-current)
    if [[ "$current_branch" != "main" && "$current_branch" != "develop" ]]; then
        log_warning "Pas sur main/develop, basculement..."
        git checkout develop 2>/dev/null || git checkout main
    fi

    # Mettre à jour
    git pull origin $(git branch --show-current)

    # Créer branche
    branch_name="feature/$feature_name"
    git checkout -b "$branch_name"

    # Créer issue
    log_info "Création de l'issue..."
    gh issue create \
        --title "Feature: $feature_name" \
        --body "## Description
Feature: $feature_name

## Tâches
- [ ] Implémentation
- [ ] Tests
- [ ] Documentation
- [ ] Review

## Critères d'acceptation
À définir..." \
        --label "enhancement,feature" \
        --assignee @me

    # Push branche
    git push -u origin "$branch_name"

    log_success "Feature $feature_name créée !"
    log_info "Branche: $branch_name"
    log_info "N'oubliez pas de créer une PR draft quand vous commencez le développement"
}

# Commande: finaliser feature
cmd_finish_feature() {
    local current_branch=$(git branch --show-current)

    if [[ "$current_branch" != feature/* ]]; then
        log_error "Vous devez être sur une branche feature/"
        exit 1
    fi

    log_info "Finalisation de la feature: $current_branch"

    # Vérifier que tout est commité
    if ! git diff-index --quiet HEAD --; then
        log_error "Vous avez des changements non commités"
        exit 1
    fi

    # Rebase sur develop/main
    target_branch="develop"
    git fetch origin "$target_branch" 2>/dev/null || target_branch="main"

    log_info "Rebase sur $target_branch..."
    git rebase "origin/$target_branch"

    # Push
    git push --force-with-lease

    # Créer/mettre à jour PR
    if gh pr view &>/dev/null; then
        log_info "Mise à jour de la PR existante..."
        gh pr edit --remove-label "draft" --add-label "ready-for-review"
        gh pr ready
    else
        log_info "Création de la PR..."
        gh pr create \
            --title "$(echo "$current_branch" | sed 's/feature\///' | tr '-' ' ' | sed 's/\b\w/\U&/g')" \
            --body "## Description
Cette PR implémente la fonctionnalité: $(echo "$current_branch" | sed 's/feature\///')

## Changes
- Nouvelle fonctionnalité implémentée
- Tests ajoutés
- Documentation mise à jour

## Review Checklist
- [ ] Code review
- [ ] Tests passent
- [ ] Documentation OK
- [ ] Pas de conflits" \
            --label "ready-for-review"
    fi

    log_success "Feature prête pour review !"
    gh pr view --web
}

# Commande: daily standup
cmd_daily_standup() {
    log_info "📊 Daily Standup Report - $(date +%Y-%m-%d)"
    echo "=================================="

    # Travail d'hier (commits)
    echo ""
    echo "🔨 Travail d'hier:"
    git log --oneline --since="yesterday" --author="$(git config user.email)" | head -5

    # Issues en cours
    echo ""
    echo "🎯 Issues assignées:"
    gh issue list --assignee @me --state open | head -5

    # PRs en cours
    echo ""
    echo "🔄 Pull Requests:"
    gh pr list --author @me --state open | head -3

    # PRs à reviewer
    echo ""
    echo "👀 PRs à reviewer:"
    gh pr list --search "review-requested:@me state:open" | head -3

    # Planning aujourd'hui
    echo ""
    echo "📅 Aujourd'hui:"
    current_branch=$(git branch --show-current)
    if [[ "$current_branch" != "main" && "$current_branch" != "develop" ]]; then
        echo "- Continuer le travail sur: $current_branch"
    else
        echo "- Pas de feature en cours"
    fi

    echo "- $(gh issue list --assignee @me --state open | wc -l) issues assignées"
    echo "- $(gh pr list --search "review-requested:@me state:open" | wc -l) reviews à faire"
}

# Commande: cleanup
cmd_cleanup() {
    log_info "🧹 Nettoyage du workspace..."

    # Nettoyer les branches mergées
    git branch --merged | grep -v "\*\|main\|develop" | xargs -n 1 git branch -d 2>/dev/null || true

    # Nettoyer les références distantes
    git remote prune origin

    # Nettoyer les fichiers temporaires
    find . -name "*.tmp" -o -name "*.log" -o -name ".DS_Store" | xargs rm -f 2>/dev/null || true

    log_success "Nettoyage terminé !"
}

# Commande: status équipe
cmd_team_status() {
    log_info "👥 Statut de l'équipe"
    echo "===================="

    # PRs ouvertes par membre
    echo ""
    echo "📊 PRs ouvertes par membre:"
    gh pr list --state open --json author | jq -r '.[] | .author.login' | sort | uniq -c | sort -nr

    # Issues par label
    echo ""
    echo "🏷️  Issues par priorité:"
    gh issue list --state open --json labels | jq -r '.[] | .labels[] | select(.name | test("priority|urgent|high|medium|low")) | .name' | sort | uniq -c

    # Activité récente
    echo ""
    echo "📈 Activité récente (7 jours):"
    since_date=$(date -d '7 days ago' +%Y-%m-%d 2>/dev/null || date -v-7d +%Y-%m-%d)
    echo "- Commits: $(git log --oneline --since="$since_date" | wc -l)"
    echo "- Issues créées: $(gh issue list --state all --search "created:>$since_date" --json number | jq length)"
    echo "- PRs créées: $(gh pr list --state all --search "created:>$since_date" --json number | jq length)"
}

# Menu principal
case "$1" in
    "new-feature")
        cmd_new_feature "$2"
        ;;
    "finish-feature")
        cmd_finish_feature
        ;;
    "daily")
        cmd_daily_standup
        ;;
    "cleanup")
        cmd_cleanup
        ;;
    "team-status")
        cmd_team_status
        ;;
    "" | "-h" | "--help")
        cat << EOF
🚀 Team Workflow Script

Usage: $0 <command> [options]

Commands:
  new-feature <name>    Créer une nouvelle feature
  finish-feature        Finaliser la feature courante
  daily                 Rapport daily standup
  cleanup               Nettoyer le workspace
  team-status           Statut de l'équipe

Examples:
  $0 new-feature "user-authentication"
  $0 finish-feature
  $0 daily
EOF
        ;;
    *)
        log_error "Commande inconnue: $1"
        log_info "Utilisez '$0 --help' pour voir les commandes disponibles"
        exit 1
        ;;
esac
EOF

chmod +x scripts/team-workflow.sh

# 6. Package.json pour les outils
cat > package.json << 'EOF'
{
  "name": "projet-equipe-final",
  "version": "1.0.0",
  "description": "Projet final - Workflow équipe complet",
  "scripts": {
    "test": "echo \"Tests OK\"",
    "lint": "echo \"Linting OK\"",
    "build": "echo \"Building...\"",
    "dev": "echo \"Dev server starting...\""
  },
  "keywords": ["github-cli", "workflow", "team"],
  "author": "Nicolas DEOUX"
}
EOF

# 7. Documentation complète
cat > docs/TEAM-WORKFLOW.md << 'EOF'
# Workflow Équipe - Guide Complet

## 🎯 Objectif
Ce guide décrit le workflow standardisé pour notre équipe de développement utilisant la CLI GitHub.

## 🔄 Workflow Feature

### 1. Nouvelle Feature
```bash
./scripts/team-workflow.sh new-feature "nom-feature"
```

Cette commande :
- ✅ Crée une branche `feature/nom-feature`
- ✅ Crée une issue associée
- ✅ Configure le tracking de branche

### 2. Développement
- Travaillez sur votre branche feature
- Commitez régulièrement avec des messages clairs
- Poussez fréquemment vos changements

### 3. Finalisation
```bash
./scripts/team-workflow.sh finish-feature
```

Cette commande :
- ✅ Rebase sur la branche principale
- ✅ Crée ou met à jour la PR
- ✅ Marque comme prête pour review

## 📊 Suivi Quotidien

### Daily Standup
```bash
./scripts/team-workflow.sh daily
```

Affiche :
- 🔨 Commits d'hier
- 🎯 Issues assignées
- 🔄 PRs en cours
- 👀 Reviews à faire

### Status Équipe
```bash
./scripts/team-workflow.sh team-status
```

## 🏷️ Convention de Labels

### Issues
- `bug` - Correction de bug
- `enhancement` - Nouvelle fonctionnalité
- `documentation` - Mise à jour documentation
- `priority:high/medium/low` - Priorité

### Pull Requests
- `ready-for-review` - Prêt pour review
- `work-in-progress` - En cours de développement
- `breaking-change` - Changement majeur

## 🔒 Règles de Protection

### Branche Main
- ✅ Review requise
- ✅ Tests CI obligatoires
- ✅ Branche à jour requise
- ❌ Push direct interdit

## 🧹 Maintenance

### Nettoyage Régulier
```bash
./scripts/team-workflow.sh cleanup
```

Nettoie :
- Branches mergées locales
- Références distantes obsolètes
- Fichiers temporaires

## 📋 Checklist PR

Avant de merger une PR :
- [ ] Code reviewé par au moins 1 personne
- [ ] Tests passent (CI verte)
- [ ] Documentation mise à jour
- [ ] Issue associée fermée
- [ ] Pas de conflits

## 🚨 Urgences

En cas de bug critique :
1. Créer une branche `hotfix/description`
2. Fix rapide
3. PR directe sur main
4. Merge immédiat après review express
5. Cherry-pick sur develop

## 📞 Support

Questions sur le workflow :
- 📚 Consultez la documentation
- 💬 Demandez dans le channel #dev
- 🎫 Créez une issue avec le label `question`
EOF

# 8. Commit et push initial
git add .
git commit -m "feat: setup complete team workflow

- Add issue templates with forms
- Add comprehensive CI/CD pipeline
- Add team workflow automation scripts
- Add complete documentation
- Configure team collaboration tools"

git push -u origin main

log_success "🎉 Projet équipe final créé avec succès !"
log_info "Repository: $(gh repo view --json url -q .url)"
log_info "Prochaines étapes :"
echo "  1. Inviter les membres de l'équipe"
echo "  2. Configurer les règles de protection de branche"
echo "  3. Tester le workflow avec une première feature"
echo "  4. Former l'équipe sur les nouveaux outils"
```

### Projet Final 2 - Système de Release Automatisé 🔴

**Objectif :** Créer un système complet de gestion de releases automatisées

**Solution :**
```bash
# Créer le projet de release automatisée
gh repo create release-automation-system --public
gh repo clone release-automation-system
cd release-automation-system

# Structure pour un projet avec releases complexes
mkdir -p {src,scripts,docs,.github/workflows}

# Workflow de release sémantique
cat > .github/workflows/semantic-release.yml << 'EOF'
name: Semantic Release

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  release:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      issues: write
      pull-requests: write
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Analyze commits
        id: analyze
        run: |
          # Script d'analyse des commits pour déterminer le type de release
          echo "Analyse des commits depuis la dernière release..."

          LAST_TAG=$(git describe --tags --abbrev=0 2>/dev/null || echo "")
          if [ -z "$LAST_TAG" ]; then
            echo "version_bump=minor" >> $GITHUB_OUTPUT
            echo "Première release - version minor"
          else
            # Analyser les commits depuis le dernier tag
            COMMITS=$(git log ${LAST_TAG}..HEAD --oneline)

            if echo "$COMMITS" | grep -E "^[a-f0-9]+ (feat!|BREAKING CHANGE)"; then
              echo "version_bump=major" >> $GITHUB_OUTPUT
              echo "Breaking change détecté - version major"
            elif echo "$COMMITS" | grep -E "^[a-f0-9]+ feat"; then
              echo "version_bump=minor" >> $GITHUB_OUTPUT
              echo "Nouvelle feature détectée - version minor"
            elif echo "$COMMITS" | grep -E "^[a-f0-9]+ (fix|perf)"; then
              echo "version_bump=patch" >> $GITHUB_OUTPUT
              echo "Fix détecté - version patch"
            else
              echo "version_bump=none" >> $GITHUB_OUTPUT
              echo "Aucun changement nécessitant une release"
            fi
          fi

      - name: Generate version
        id: version
        if: steps.analyze.outputs.version_bump != 'none'
        run: |
          LAST_TAG=$(git describe --tags --abbrev=0 2>/dev/null || echo "v0.0.0")
          LAST_VERSION=${LAST_TAG#v}

          IFS='.' read -ra VERSION_PARTS <<< "$LAST_VERSION"
          MAJOR=${VERSION_PARTS[0]:-0}
          MINOR=${VERSION_PARTS[1]:-0}
          PATCH=${VERSION_PARTS[2]:-0}

          case "${{ steps.analyze.outputs.version_bump }}" in
            "major")
              NEW_VERSION="$((MAJOR + 1)).0.0"
              ;;
            "minor")
              NEW_VERSION="${MAJOR}.$((MINOR + 1)).0"
              ;;
            "patch")
              NEW_VERSION="${MAJOR}.${MINOR}.$((PATCH + 1))"
              ;;
          esac

          echo "new_version=v$NEW_VERSION" >> $GITHUB_OUTPUT
          echo "Nouvelle version: v$NEW_VERSION"

      - name: Build artifacts
        if: steps.version.outputs.new_version
        run: |
          echo "🔨 Building artifacts for ${{ steps.version.outputs.new_version }}"
          mkdir -p dist/{linux,macos,windows}

          # Simuler la construction de binaires
          echo "Binary for Linux ${{ steps.version.outputs.new_version }}" > dist/linux/app-linux
          echo "Binary for macOS ${{ steps.version.outputs.new_version }}" > dist/macos/app-macos
          echo "Binary for Windows ${{ steps.version.outputs.new_version }}" > dist/windows/app-windows.exe

          # Créer les archives
          cd dist
          tar -czf app-linux-${{ steps.version.outputs.new_version }}.tar.gz linux/
          tar -czf app-macos-${{ steps.version.outputs.new_version }}.tar.gz macos/
          zip -r app-windows-${{ steps.version.outputs.new_version }}.zip windows/

      - name: Generate changelog
        if: steps.version.outputs.new_version
        id: changelog
        run: |
          echo "📝 Génération du changelog..."

          LAST_TAG=$(git describe --tags --abbrev=0 2>/dev/null || echo "")
          CHANGELOG_FILE="CHANGELOG-${{ steps.version.outputs.new_version }}.md"

          cat > $CHANGELOG_FILE << EOCHANGELOG
# Changelog ${{ steps.version.outputs.new_version }}

## 🚀 Nouvelles fonctionnalités
$(git log ${LAST_TAG}..HEAD --oneline | grep "feat:" | sed 's/^[a-f0-9]* feat: /- /' || echo "Aucune")

## 🐛 Corrections de bugs
$(git log ${LAST_TAG}..HEAD --oneline | grep "fix:" | sed 's/^[a-f0-9]* fix: /- /' || echo "Aucune")

## 🔧 Améliorations
$(git log ${LAST_TAG}..HEAD --oneline | grep "perf:" | sed 's/^[a-f0-9]* perf: /- /' || echo "Aucune")

## 📚 Documentation
$(git log ${LAST_TAG}..HEAD --oneline | grep "docs:" | sed 's/^[a-f0-9]* docs: /- /' || echo "Aucune")

## 🔨 Changements techniques
$(git log ${LAST_TAG}..HEAD --oneline | grep -E "(chore|refactor):" | sed 's/^[a-f0-9]* [^:]*: /- /' || echo "Aucun")

---
**Comparaison complète**: [\`${LAST_TAG}...${{ steps.version.outputs.new_version }}\`](https://github.com/${{ github.repository }}/compare/${LAST_TAG}...${{ steps.version.outputs.new_version }})
EOCHANGELOG

          echo "changelog_file=$CHANGELOG_FILE" >> $GITHUB_OUTPUT

      - name: Create Release
        if: steps.version.outputs.new_version
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          echo "🎉 Création de la release ${{ steps.version.outputs.new_version }}"

          gh release create "${{ steps.version.outputs.new_version }}" \
            dist/*.tar.gz dist/*.zip \
            --title "Release ${{ steps.version.outputs.new_version }}" \
            --notes-file "${{ steps.changelog.outputs.changelog_file }}" \
            --generate-notes

      - name: Update documentation
        if: steps.version.outputs.new_version
        run: |
          echo "📚 Mise à jour de la documentation..."

          # Mettre à jour le README avec la nouvelle version
          sed -i "s/Version: v[0-9]\+\.[0-9]\+\.[0-9]\+/Version: ${{ steps.version.outputs.new_version }}/g" README.md

          # Commit des changements
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add README.md
          git commit -m "docs: update version to ${{ steps.version.outputs.new_version }}" || exit 0
          git push

      - name: Notify team
        if: steps.version.outputs.new_version
        run: |
          echo "📢 Notification de l'équipe..."
          echo "✅ Release ${{ steps.version.outputs.new_version }} créée avec succès !"
          echo "🔗 Lien: ${{ github.server_url }}/${{ github.repository }}/releases/tag/${{ steps.version.outputs.new_version }}"
EOF

# Script de release manuelle
cat > scripts/manual-release.sh << 'EOF'
#!/bin/bash
set -e

# Couleurs
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m'

log_info() { echo -e "${BLUE}ℹ️  $1${NC}"; }
log_success() { echo -e "${GREEN}✅ $1${NC}"; }
log_warning() { echo -e "${YELLOW}⚠️  $1${NC}"; }
log_error() { echo -e "${RED}❌ $1${NC}"; }

# Fonction de release manuelle
create_manual_release() {
    local version_type="$1"

    if [[ ! "$version_type" =~ ^(major|minor|patch)$ ]]; then
        log_error "Type de version invalide. Utilisez: major, minor, ou patch"
        exit 1
    fi

    log_info "🚀 Création d'une release $version_type"

    # Vérifier que nous sommes sur main
    current_branch=$(git branch --show-current)
    if [ "$current_branch" != "main" ]; then
        log_error "Vous devez être sur la branche main pour créer une release"
        exit 1
    fi

    # Vérifier que tout est à jour
    git fetch origin main
    if [ "$(git rev-parse HEAD)" != "$(git rev-parse origin/main)" ]; then
        log_error "Votre branche locale n'est pas à jour avec origin/main"
        exit 1
    fi

    # Vérifier qu'il n'y a pas de changements non commités
    if ! git diff-index --quiet HEAD --; then
        log_error "Vous avez des changements non commités"
        exit 1
    fi

    # Calculer la nouvelle version
    LAST_TAG=$(git describe --tags --abbrev=0 2>/dev/null || echo "v0.0.0")
    LAST_VERSION=${LAST_TAG#v}

    IFS='.' read -ra VERSION_PARTS <<< "$LAST_VERSION"
    MAJOR=${VERSION_PARTS[0]:-0}
    MINOR=${VERSION_PARTS[1]:-0}
    PATCH=${VERSION_PARTS[2]:-0}

    case "$version_type" in
        "major")
            NEW_VERSION="$((MAJOR + 1)).0.0"
            ;;
        "minor")
            NEW_VERSION="${MAJOR}.$((MINOR + 1)).0"
            ;;
        "patch")
            NEW_VERSION="${MAJOR}.${MINOR}.$((PATCH + 1))"
            ;;
    esac

    NEW_TAG="v$NEW_VERSION"

    log_info "Dernière version: $LAST_TAG"
    log_info "Nouvelle version: $NEW_TAG"

    # Demander confirmation
    echo ""
    read -p "Confirmer la création de la release $NEW_TAG ? (y/N): " -n 1 -r
    echo
    if [[ ! $REPLY =~ ^[Yy]$ ]]; then
        log_warning "Release annulée"
        exit 0
    fi

    # Créer et pousser le tag
    log_info "Création du tag $NEW_TAG..."
    git tag -a "$NEW_TAG" -m "Release $NEW_TAG"
    git push origin "$NEW_TAG"

    log_success "Tag $NEW_TAG créé et poussé"
    log_info "Le workflow de release automatique va se déclencher..."

    # Attendre un peu et afficher le statut
    sleep 10
    log_info "Status du workflow:"
    gh run list --workflow="semantic-release.yml" --limit 1
}

# Fonction de rollback
rollback_release() {
    local tag="$1"

    if [ -z "$tag" ]; then
        log_error "Spécifiez le tag à rollback"
        exit 1
    fi

    log_warning "🔙 Rollback de la release $tag"

    # Vérifier que le tag existe
    if ! git tag -l | grep -q "^$tag$"; then
        log_error "Le tag $tag n'existe pas"
        exit 1
    fi

    # Demander confirmation
    read -p "Confirmer le rollback de $tag ? (y/N): " -n 1 -r
    echo
    if [[ ! $REPLY =~ ^[Yy]$ ]]; then
        log_warning "Rollback annulé"
        exit 0
    fi

    # Supprimer le tag local et distant
    git tag -d "$tag"
    git push origin ":refs/tags/$tag"

    # Supprimer la release GitHub
    gh release delete "$tag" --yes

    log_success "Release $tag supprimée"
}

# Fonction de prévisualisation
preview_release() {
    local version_type="$1"

    log_info "🔍 Prévisualisation de la release $version_type"

    # Calculer la version
    LAST_TAG=$(git describe --tags --abbrev=0 2>/dev/null || echo "v0.0.0")
    LAST_VERSION=${LAST_TAG#v}

    IFS='.' read -ra VERSION_PARTS <<< "$LAST_VERSION"
    MAJOR=${VERSION_PARTS[0]:-0}
    MINOR=${VERSION_PARTS[1]:-0}
    PATCH=${VERSION_PARTS[2]:-0}

    case "$version_type" in
        "major") NEW_VERSION="$((MAJOR + 1)).0.0" ;;
        "minor") NEW_VERSION="${MAJOR}.$((MINOR + 1)).0" ;;
        "patch") NEW_VERSION="${MAJOR}.${MINOR}.$((PATCH + 1))" ;;
    esac

    NEW_TAG="v$NEW_VERSION"

    echo "📊 Résumé de la release:"
    echo "  Dernière version: $LAST_TAG"
    echo "  Nouvelle version: $NEW_TAG"
    echo "  Type: $version_type"
    echo ""

    echo "📝 Commits inclus:"
    git log ${LAST_TAG}..HEAD --oneline --max-count=10
    echo ""

    echo "📈 Statistiques:"
    echo "  Commits: $(git log ${LAST_TAG}..HEAD --oneline | wc -l)"
    echo "  Fichiers modifiés: $(git diff ${LAST_TAG}..HEAD --name-only | wc -l)"
    echo "  Contributeurs: $(git log ${LAST_TAG}..HEAD --format='%an' | sort -u | wc -l)"
}

# Menu principal
case "$1" in
    "create")
        create_manual_release "$2"
        ;;
    "rollback")
        rollback_release "$2"
        ;;
    "preview")
        preview_release "$2"
        ;;
    "" | "-h" | "--help")
        cat << EOF
🚀 Manual Release Script

Usage: $0 <command> [options]

Commands:
  create <type>     Créer une nouvelle release (major|minor|patch)
  rollback <tag>    Rollback d'une release
  preview <type>    Prévisualiser une release

Examples:
  $0 create minor
  $0 preview patch
  $0 rollback v1.2.3
EOF
        ;;
    *)
        log_error "Commande inconnue: $1"
        exit 1
        ;;
esac
EOF

chmod +x scripts/manual-release.sh

# Configuration et documentation
cat > README.md << 'EOF'
# Release Automation System

Version: v0.1.0

Système automatisé de gestion des releases avec semantic versioning.

## 🚀 Features

- ✅ Release automatique basée sur les commits
- ✅ Semantic versioning automatique
- ✅ Génération de changelog
- ✅ Build et distribution d'artifacts
- ✅ Notifications automatiques

## 📋 Convention de Commits

- `feat:` - Nouvelle fonctionnalité (bump minor)
- `fix:` - Correction de bug (bump patch)
- `perf:` - Amélioration de performance (bump patch)
- `feat!:` ou `BREAKING CHANGE:` - Changement majeur (bump major)

## 🔧 Usage

### Release Automatique
Les releases sont créées automatiquement lors des push sur `main`.

### Release Manuelle
```bash
# Prévisualiser
./scripts/manual-release.sh preview minor

# Créer
./scripts/manual-release.sh create minor

# Rollback si nécessaire
./scripts/manual-release.sh rollback v1.2.3
```

## 📊 Workflow

1. **Développement** sur des branches feature
2. **PR** vers main avec review
3. **Merge** déclenche l'analyse automatique
4. **Release** créée si nécessaire
5. **Notification** de l'équipe

## 🏷️ Artifacts

Chaque release inclut :
- Binaires Linux (tar.gz)
- Binaires macOS (tar.gz)
- Binaires Windows (zip)
- Changelog automatique
- Notes de release
EOF

# Commit initial
git add .
git commit -m "feat: setup complete release automation system

- Add semantic release workflow
- Add manual release scripts
- Add commit convention documentation
- Configure automated changelog generation
- Setup cross-platform artifact building"

git push -u origin main

log_success "🎉 Système de release automatisé créé !"
log_info "Testez avec: ./scripts/manual-release.sh preview minor"
```

---

## 🏆 Certification et Évaluation

### Quiz Final - Maîtrise CLI GitHub 🔴

**Instructions :** Réalisez ce quiz pratique pour valider vos compétences

```bash
# Créer un repository de certification
gh repo create cli-github-certification --private
gh repo clone cli-github-certification
cd cli-github-certification

# Mission 1: Setup complet (5 points)
# - Configurez un repository avec tous les templates
# - Ajoutez un workflow CI/CD
# - Créez 3 issues avec labels différents
# - Invitez un collaborateur

# Mission 2: Workflow avancé (5 points)
# - Créez une branche feature
# - Faites 3 commits avec convention sémantique
# - Créez une PR draft puis finalisez
# - Mergez avec squash

# Mission 3: Automation (5 points)
# - Créez une extension CLI personnalisée
# - Automatisez une tâche répétitive
# - Configurez des notifications
# - Documentez votre solution

# Mission 4: Release et deployment (5 points)
# - Configurez un workflow de release
# - Créez une release avec assets
# - Automatisez le changelog
# - Testez le rollback

# Total: 20 points
# 18-20: Expert 🏆
# 15-17: Avancé 🥇
# 12-14: Intermédiaire 🥈
# 9-11: Débutant confirmé 🥉
```

## 🎓 Certificat de Réussite

Une fois tous les exercices terminés avec succès, vous aurez maîtrisé :

✅ **Installation et configuration** de la CLI GitHub
✅ **Gestion complète des repositories** et collaborations
✅ **Workflows avancés** avec issues et pull requests
✅ **Automatisation** avec GitHub Actions
✅ **Extensions et personnalisation** de la CLI
✅ **Bonnes pratiques** de sécurité et performance
✅ **Gestion d'équipe** et workflows professionnels

**Félicitations ! Vous êtes maintenant un expert de la CLI GitHub ! 🎉**

---

*Formation créée par Nicolas DEOUX - NDXdev@gmail.com*
*Dernière mise à jour : Juillet 2025*
