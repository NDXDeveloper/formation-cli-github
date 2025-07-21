🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 9.1 Combiner commandes Git et CLI GitHub

## Comprendre la complémentarité

Git et GitHub CLI sont deux outils distincts mais parfaitement complémentaires. Imaginez-les comme deux membres d'une équipe :

- **Git** = Le développeur qui gère le code localement
- **GitHub CLI** = Le chef de projet qui gère la collaboration et les processus

## Les bases : Git local + GitHub CLI distant

### Workflow typique sans intégration

Avant GitHub CLI, voici comment se déroulait un workflow classique :

```bash
# 1. Développement local (Git)
git checkout -b nouvelle-fonctionnalite
git add .
git commit -m "Ajout nouvelle fonctionnalité"
git push origin nouvelle-fonctionnalite

# 2. Ensuite... aller sur le navigateur web 🙄
# - Ouvrir GitHub.com
# - Cliquer sur "New Pull Request"
# - Remplir le formulaire
# - Attendre les reviews...
# - Revenir au navigateur pour merger
```

### Workflow moderne avec intégration

Maintenant avec GitHub CLI, tout se fait en ligne de commande :

```bash
# 1. Développement local (Git)
git checkout -b nouvelle-fonctionnalite
git add .
git commit -m "Ajout nouvelle fonctionnalité"
git push origin nouvelle-fonctionnalite

# 2. Collaboration (GitHub CLI) - directement dans le terminal !
gh pr create --title "Nouvelle fonctionnalité" --body "Description détaillée"
gh pr view  # Vérifier la PR créée
```

## Scénarios d'intégration courants

### Scénario 1 : Créer une feature branch et une PR

**Étape par étape pour débutants :**

```bash
# 1. Partir de la branche principale à jour
git checkout main
git pull origin main

# 2. Créer et basculer sur nouvelle branche
git checkout -b ajout-page-contact

# 3. Développer (exemple : créer un fichier)
echo "Page de contact" > contact.html
git add contact.html
git commit -m "Ajout page de contact"

# 4. Pousser la branche vers GitHub
git push origin ajout-page-contact

# 5. Créer immédiatement une PR avec GitHub CLI
gh pr create --title "Ajout d'une page de contact" \
             --body "Cette PR ajoute une page de contact basique au site"
```

**Ce qui se passe :** Git gère votre développement local, GitHub CLI crée instantanément la PR sans quitter le terminal.

### Scénario 2 : Récupérer et tester une PR de collègue

```bash
# 1. Lister les PRs ouvertes
gh pr list

# Résultat exemple :
# #23  Fix bug login     fix-login-bug    alice
# #24  Add dark mode     dark-mode        bob

# 2. Récupérer la PR #24 de Bob localement
gh pr checkout 24

# 3. Tester localement avec Git
git log --oneline -3  # Voir les commits de Bob
# Lancer l'application, tester...

# 4. Approuver directement via CLI
gh pr review 24 --approve --body "Fonctionne parfaitement, good job Bob!"
```

**Ce qui se passe :** GitHub CLI récupère le code distant, Git vous permet de l'examiner localement.

### Scénario 3 : Workflow de maintenance rapide

```bash
# 1. Créer une branche de hotfix
git checkout main
git pull origin main
git checkout -b hotfix-typo-homepage

# 2. Corriger rapidement
sed -i 's/Acceuil/Accueil/' index.html  # Corriger la faute de frappe
git add index.html
git commit -m "Fix: correction faute de frappe 'Acceuil'"

# 3. Push et PR en une fois
git push origin hotfix-typo-homepage
gh pr create --title "Hotfix: faute de frappe homepage" \
             --body "Correction rapide de 'Acceuil' → 'Accueil'" \
             --assignee @me

# 4. Merger directement si urgent
gh pr merge hotfix-typo-homepage --squash --delete-branch
```

## Commandes Git + GitHub CLI fréquemment utilisées ensemble

### Inspection et navigation

```bash
# Voir l'état local ET distant
git status                    # État local
gh pr status                  # État des PRs

# Explorer les branches
git branch -a                 # Branches locales et distantes
gh pr list                    # PRs ouvertes

# Voir l'historique
git log --oneline -10         # Commits récents locaux
gh pr view 15 --comments      # Historique de la PR #15
```

### Synchronisation intelligente

```bash
# Mettre à jour avant de commencer
git checkout main
git pull origin main          # Récupérer les derniers changements
gh repo sync                  # Synchroniser le fork (si applicable)

# Vérifier l'état avant de merger
git status                    # Rien en attente localement ?
gh pr checks 23               # Les tests passent-ils sur la PR #23 ?
```

### Gestion des releases

```bash
# Préparer une version
git checkout main
git pull origin main
git tag v1.2.0
git push origin v1.2.0

# Créer la release GitHub
gh release create v1.2.0 \
    --title "Version 1.2.0" \
    --notes "Nouvelles fonctionnalités et corrections"
```

## Bonnes pratiques d'intégration

### 1. Toujours synchroniser d'abord

```bash
# ✅ Bon réflexe avant tout travail
git checkout main
git pull origin main

# ❌ Éviter : travailler sur une base obsolète
git checkout -b nouvelle-feature  # Sans pull avant !
```

### 2. Vérifier le contexte GitHub CLI

```bash
# Vérifier dans quel repo vous êtes
gh repo view

# Vérifier votre authentification
gh auth status
```

### 3. Utiliser les informations de GitHub CLI pour Git

```bash
# Récupérer une branche mentionnée dans une issue
gh issue view 42  # Voir l'issue #42
# Si elle mentionne une branche "fix-memory-leak"
git checkout -b fix-memory-leak
```

### 4. Coordonner les messages

```bash
# Cohérence entre commit et PR
git commit -m "Add user authentication system"
gh pr create --title "Add user authentication system" \
             --body "Implements secure login/logout functionality"
```

## Messages d'erreur courants et solutions

### "No pull request found"

```bash
# Erreur typique
gh pr view
# → Error: no pull request found

# Solution : être sûr d'être sur la bonne branche
git branch  # Vérifier la branche courante
gh pr list  # Voir toutes les PRs disponibles
```

### "Branch not found on remote"

```bash
# Si vous oubliez de pusher avant de créer la PR
gh pr create
# → Error: branch not found on remote

# Solution : pusher d'abord
git push origin ma-branche
gh pr create  # Maintenant ça marche
```

### "Permission denied"

```bash
# Vérifier l'authentification
gh auth status

# Se reconnecter si nécessaire
gh auth login
```

## Résumé des avantages

En combinant Git et GitHub CLI, vous obtenez :

- **🚀 Rapidité** : Plus de va-et-vient entre terminal et navigateur
- **🔄 Fluidité** : Workflow continu du code à la collaboration
- **🎯 Précision** : Moins d'erreurs grâce à l'automatisation
- **📊 Visibilité** : Vue d'ensemble locale ET distante
- **⚡ Productivité** : Focus sur le code plutôt que sur les outils

La maîtrise de cette intégration transforme votre expérience de développement, rendant les tâches collaboratives aussi naturelles que les commandes Git de base.

⏭️
