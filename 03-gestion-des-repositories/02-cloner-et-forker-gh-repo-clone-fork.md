🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 3.2 - Cloner et forker (`gh repo clone`, `gh repo fork`)

## 🎯 Introduction

Maintenant que vous savez créer vos propres repositories, il est temps d'apprendre à travailler avec les projets existants ! Cloner et forker sont deux actions fondamentales qui vous permettent de récupérer du code et de contribuer à des projets, que ce soit les vôtres ou ceux d'autres développeurs.

**Analogie** : Si créer un repository c'est comme construire une maison, cloner c'est comme obtenir une copie exacte de la maison de quelqu'un, et forker c'est comme obtenir votre propre version que vous pouvez modifier librement !

## 🔄 Comprendre Clone vs Fork

### Qu'est-ce que le clonage ?

**Le clonage** crée une **copie locale** d'un repository distant sur votre machine.

**Analogie** : C'est comme photocopier un livre pour pouvoir le lire et l'annoter chez vous.

#### Quand cloner ?
- ✅ **Vos propres repositories** pour travailler dessus
- ✅ **Repositories publics** pour les étudier
- ✅ **Projets d'équipe** auxquels vous avez accès
- ✅ **Déploiement** de code sur un serveur

### Qu'est-ce que le fork ?

**Le fork** crée une **copie complète** d'un repository dans votre compte GitHub, que vous pouvez ensuite modifier librement.

**Analogie** : C'est comme prendre la recette de quelqu'un et en faire votre propre version dans votre livre de cuisine.

#### Quand forker ?
- ✅ **Contribuer** à un projet open source
- ✅ **Personnaliser** un projet existant
- ✅ **Expérimenter** sans affecter l'original
- ✅ **Apprendre** en modifiant du code existant

### Différences clés

| Aspect | Clone | Fork |
|--------|-------|------|
| **Où** | Sur votre machine | Sur votre compte GitHub |
| **Propriété** | Repository reste chez le propriétaire | Devient votre repository |
| **Modifications** | Locales uniquement | Peuvent être partagées |
| **Contribution** | Limitée selon permissions | Illimitée sur votre fork |
| **Synchronisation** | Pull/Push directement | Via Pull Request |

## 🔧 Cloner avec `gh repo clone`

### Syntaxe de base

```bash
gh repo clone <repository>
```

**Le repository peut être spécifié de plusieurs façons** :
- `owner/repo` → Clone depuis GitHub
- `https://github.com/owner/repo` → URL complète
- Juste `repo` → Si c'est votre repository

### Clonage simple

```bash
# Cloner un repository public
gh repo clone microsoft/vscode

# Cloner votre propre repository
gh repo clone mon-projet

# Cloner avec URL complète
gh repo clone https://github.com/facebook/react
```

**Après clonage** :
- Le dossier est créé avec le nom du repository
- Vous pouvez naviguer dedans : `cd vscode`
- Les remotes Git sont configurés automatiquement

### Options de clonage

#### Cloner dans un dossier spécifique

```bash
# Cloner dans un dossier nommé différemment
gh repo clone microsoft/vscode mon-editeur

# Résultat : le code sera dans ./mon-editeur/
```

#### Cloner une branche spécifique

```bash
# Cloner seulement la branche 'develop'
git clone -b develop https://github.com/owner/repo
# Note : Cette option nécessite git classique
```

### Clonage intelligent avec configuration

**La CLI GitHub configure automatiquement** :

```bash
gh repo clone facebook/react
cd react

# Vérifier la configuration automatique
git remote -v
```

**Résultat** :
```
origin  https://github.com/facebook/react (fetch)
origin  https://github.com/facebook/react (push)
```

**Avantages** :
- ✅ Configuration automatique des remotes
- ✅ Authentification GitHub intégrée
- ✅ Protocole optimal (HTTPS/SSH selon votre config)

## 🍴 Forker avec `gh repo fork`

### Syntaxe de base

```bash
gh repo fork <repository>
```

### Fork simple

```bash
# Forker un repository public
gh repo fork microsoft/vscode

# Résultat : Crée 'votre-username/vscode' sur votre compte GitHub
```

**Ce qui se passe** :
1. **Fork créé** sur votre compte GitHub
2. **Lien maintenu** avec le repository original (upstream)
3. **Repository prêt** pour vos modifications

### Fork avec clonage automatique

```bash
# Forker ET cloner en une commande
gh repo fork microsoft/vscode --clone

# Équivaut à :
gh repo fork microsoft/vscode
gh repo clone votre-username/vscode
```

**Très pratique** car vous avez immédiatement une copie locale !

### Fork avec nom personnalisé

```bash
# Forker avec un nom différent
gh repo fork microsoft/vscode --fork-name mon-editeur-perso

# Résultat : 'votre-username/mon-editeur-perso'
```

### Configuration automatique de l'upstream

Quand vous forkez avec `--clone`, la CLI configure automatiquement :

```bash
gh repo fork microsoft/vscode --clone
cd vscode
git remote -v
```

**Résultat** :
```
origin     https://github.com/votre-username/vscode (fetch)
origin     https://github.com/votre-username/vscode (push)
upstream   https://github.com/microsoft/vscode (fetch)
upstream   https://github.com/microsoft/vscode (push)
```

**Explication** :
- **origin** : Votre fork (pour vos modifications)
- **upstream** : Repository original (pour les mises à jour)

## 🔄 Workflow complet de contribution

### Contribuer à un projet open source

**Scénario** : Vous voulez corriger un bug dans React.

#### Étape 1 : Fork et clone
```bash
gh repo fork facebook/react --clone
cd react
```

#### Étape 2 : Créer une branche pour votre modification
```bash
git checkout -b fix-bug-header
```

#### Étape 3 : Faire vos modifications
```bash
# Éditer les fichiers nécessaires
echo "// Fix applied" >> src/header.js
git add .
git commit -m "Fix header display bug"
```

#### Étape 4 : Pousser vers votre fork
```bash
git push origin fix-bug-header
```

#### Étape 5 : Créer une Pull Request
```bash
gh pr create \
  --title "Fix header display bug" \
  --body "This fixes the issue where header was not displayed correctly on mobile devices"
```

**Résultat** : Votre contribution est prête à être reviewée !

### Maintenir votre fork à jour

#### Synchroniser avec l'upstream

```bash
# Récupérer les dernières modifications de l'original
git fetch upstream

# Se positionner sur votre branche principale
git checkout main

# Intégrer les modifications de l'upstream
git merge upstream/main

# Mettre à jour votre fork sur GitHub
git push origin main
```

#### Commande de synchronisation rapide

```bash
# Synchronisation automatique (si disponible)
gh repo sync owner/repo
```

## 🎯 Cas d'usage pratiques

### Développeur débutant

**Objectif** : Apprendre en étudiant du code de qualité.

```bash
# Cloner des projets pour apprendre
gh repo clone microsoft/typescript    # Apprendre TypeScript
gh repo clone facebook/react          # Comprendre React
gh repo clone nodejs/node             # Étudier Node.js

# Explorer le code
cd typescript
ls -la
cat README.md
```

### Contributeur open source

**Objectif** : Contribuer à des projets existants.

```bash
# Forker un projet pour contribuer
gh repo fork awesome-selfhosted/awesome-selfhosted --clone
cd awesome-selfhosted

# Ajouter votre contribution
echo "- [Mon Super Outil](https://mon-outil.com) - Description de l'outil" >> README.md

# Proposer la modification
git add README.md
git commit -m "Add Mon Super Outil to the list"
git push origin main
gh pr create --title "Add new self-hosted tool"
```

### Développeur d'équipe

**Objectif** : Travailler sur des projets partagés.

```bash
# Cloner le projet de l'équipe
gh repo clone mon-entreprise/projet-equipe
cd projet-equipe

# Créer une branche pour votre feature
git checkout -b feature/nouvelle-fonctionnalite

# Travailler et pousser
# ... développement ...
git push origin feature/nouvelle-fonctionnalite

# Créer une PR pour review
gh pr create --title "Nouvelle fonctionnalité X"
```

### Expérimentateur

**Objectif** : Tester et modifier des projets existants.

```bash
# Forker pour expérimenter librement
gh repo fork popular-project/awesome-tool --fork-name my-experiments

# Cloner votre version
gh repo clone votre-username/my-experiments
cd my-experiments

# Expérimenter sans contraintes
git checkout -b experiment-crazy-idea
# ... modifications expérimentales ...
```

## 🔧 Options avancées

### Clonage avec options Git

#### Clonage superficiel (pour gros repositories)

```bash
# Clone seulement le dernier commit (plus rapide)
git clone --depth 1 https://github.com/microsoft/vscode
```

#### Clonage partiel

```bash
# Clone seulement certains dossiers (Git 2.19+)
git clone --filter=blob:none --sparse-checkout https://github.com/microsoft/vscode
cd vscode
git sparse-checkout set src/vs/editor
```

### Fork avec options

#### Fork dans une organisation

```bash
# Forker dans votre organisation (si vous avez les droits)
gh repo fork microsoft/vscode --org mon-organisation
```

#### Fork privé

```bash
# Créer un fork privé (nécessite GitHub Pro/Team)
gh repo fork public-project/repo --private
```

### Gestion des remotes

#### Ajouter des remotes supplémentaires

```bash
# Ajouter un autre fork comme remote
git remote add contributor https://github.com/autre-user/projet

# Récupérer leurs modifications
git fetch contributor

# Voir toutes les remotes
git remote -v
```

#### Changer l'URL d'un remote

```bash
# Passer de HTTPS à SSH
git remote set-url origin git@github.com:username/repo.git

# Vérifier le changement
git remote -v
```

## 🛠️ Résolution de problèmes

### Problèmes de clonage

#### Repository non trouvé

**Erreur** :
```
Error: repository not found
```

**Solutions** :
```bash
# Vérifier l'orthographe
gh repo clone microsoft/vscode  # Pas microsft/vscode

# Vérifier que le repository existe
gh repo view microsoft/vscode

# Vérifier vos permissions (pour repos privés)
gh auth status
```

#### Permissions insuffisantes

**Erreur** :
```
Error: permission denied
```

**Solutions** :
```bash
# Pour repository privé : vérifier l'authentification
gh auth status

# Forker d'abord si pas d'accès direct
gh repo fork owner/private-repo

# Cloner votre fork
gh repo clone votre-username/private-repo
```

### Problèmes de fork

#### Fork déjà existant

**Erreur** :
```
Error: repository already exists
```

**Solutions** :
```bash
# Utiliser un nom différent
gh repo fork owner/repo --fork-name nouveau-nom

# Ou cloner le fork existant
gh repo clone votre-username/repo
```

#### Upstream non configuré

**Problème** : Pas de remote upstream après fork.

**Solution** :
```bash
# Ajouter manuellement l'upstream
git remote add upstream https://github.com/owner/original-repo

# Vérifier
git remote -v
```

## 🔄 Synchronisation et maintenance

### Garder votre fork à jour

#### Méthode manuelle

```bash
# 1. Récupérer les modifications de l'upstream
git fetch upstream

# 2. Se positionner sur main
git checkout main

# 3. Intégrer les modifications
git rebase upstream/main  # Ou git merge upstream/main

# 4. Mettre à jour votre fork
git push origin main
```

#### Méthode automatique (GitHub CLI)

```bash
# Synchronisation en une commande
gh repo sync

# Ou spécifier explicitement
gh repo sync votre-username/repo
```

### Nettoyer les branches

```bash
# Lister toutes les branches
git branch -a

# Supprimer les branches mergées localement
git branch --merged | grep -v main | xargs git branch -d

# Supprimer les branches distantes supprimées
git remote prune origin
```

### Script de maintenance automatique

```bash
#!/bin/bash
# maintain-fork.sh

echo "🔄 Maintenance du fork..."

# Récupérer toutes les modifications
git fetch --all --prune

# Se positionner sur main
git checkout main

# Synchroniser avec upstream
git rebase upstream/main

# Pousser les modifications
git push origin main

# Nettoyer les branches mergées
git branch --merged | grep -v main | xargs -n 1 git branch -d

echo "✅ Fork mis à jour et nettoyé !"
```

## 🎨 Workflows automatisés

### Script de contribution rapide

```bash
#!/bin/bash
# quick-contribute.sh

REPO=$1
BRANCH_NAME=$2
COMMIT_MESSAGE=$3

if [ -z "$REPO" ] || [ -z "$BRANCH_NAME" ] || [ -z "$COMMIT_MESSAGE" ]; then
    echo "Usage: $0 <owner/repo> <branch-name> <commit-message>"
    exit 1
fi

echo "🍴 Fork et contribution rapide à $REPO"

# Fork et clone
gh repo fork "$REPO" --clone

# Extraire le nom du repository
REPO_NAME=$(echo "$REPO" | cut -d'/' -f2)
cd "$REPO_NAME"

# Créer nouvelle branche
git checkout -b "$BRANCH_NAME"

echo "✅ Fork cloné et branche '$BRANCH_NAME' créée"
echo "📝 Faites vos modifications, puis utilisez :"
echo "   git add ."
echo "   git commit -m \"$COMMIT_MESSAGE\""
echo "   git push origin $BRANCH_NAME"
echo "   gh pr create"
```

### Script de clone intelligent

```bash
#!/bin/bash
# smart-clone.sh

REPO=$1
DIR_NAME=$2

if [ -z "$REPO" ]; then
    echo "Usage: $0 <owner/repo> [directory-name]"
    exit 1
fi

echo "📥 Clone intelligent de $REPO"

# Cloner
if [ -n "$DIR_NAME" ]; then
    gh repo clone "$REPO" "$DIR_NAME"
    cd "$DIR_NAME"
else
    gh repo clone "$REPO"
    REPO_NAME=$(echo "$REPO" | cut -d'/' -f2)
    cd "$REPO_NAME"
fi

# Afficher des informations utiles
echo ""
echo "📊 Informations du repository :"
gh repo view --json name,description,stargazerCount,language

echo ""
echo "📁 Structure du projet :"
find . -maxdepth 2 -type f -name "README*" -o -name "package.json" -o -name "requirements.txt" -o -name "Makefile"

echo ""
echo "🌟 Prêt à explorer !"
```

## 📊 Monitoring et statistiques

### Statistiques de vos forks

```bash
#!/bin/bash
# fork-stats.sh

echo "📊 Statistiques de vos forks"
echo "============================"

# Lister vos forks
gh repo list --fork --json name,parent,stargazerCount | \
jq -r '.[] | "\(.name) (fork de \(.parent.full_name)) - ⭐ \(.stargazerCount)"'

echo ""
echo "🔄 Forks nécessitant une synchronisation :"
gh repo list --fork --json name,pushedAt,parent | \
jq -r '.[] | select(.pushedAt < .parent.pushedAt) | .name'
```

### Vérification de la santé des clones

```bash
#!/bin/bash
# check-clones.sh

echo "🔍 Vérification des repositories locaux"
echo "======================================="

for dir in */; do
    if [ -d "$dir/.git" ]; then
        cd "$dir"
        echo "📁 $dir"

        # Vérifier s'il y a des modifications non commitées
        if ! git diff-index --quiet HEAD --; then
            echo "  ⚠️  Modifications non commitées"
        fi

        # Vérifier s'il faut pousser
        if [ $(git rev-list --count @{u}..HEAD 2>/dev/null || echo 0) -gt 0 ]; then
            echo "  📤 Commits à pousser"
        fi

        # Vérifier s'il faut tirer
        git fetch --dry-run 2>&1 | grep -q "up to date" || echo "  📥 Mises à jour disponibles"

        cd ..
    fi
done
```

## 📋 Récapitulatif des commandes essentielles

### Clonage

```bash
# Clone simple
gh repo clone owner/repo

# Clone dans un dossier spécifique
gh repo clone owner/repo mon-dossier

# Clone de votre repository
gh repo clone mon-repo
```

### Fork

```bash
# Fork simple
gh repo fork owner/repo

# Fork avec clone
gh repo fork owner/repo --clone

# Fork avec nom personnalisé
gh repo fork owner/repo --fork-name nouveau-nom
```

### Synchronisation

```bash
# Synchroniser un fork
gh repo sync

# Mettre à jour manuellement
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

### Vérification

```bash
# Voir les remotes configurés
git remote -v

# Voir les informations du repository
gh repo view

# Ouvrir dans le navigateur
gh browse
```

## 🎯 Prochaine étape

Excellent ! Vous savez maintenant récupérer et contribuer à tous les projets GitHub. Il est temps d'apprendre à explorer et découvrir l'immense écosystème de repositories disponibles.

---

*Vous avez terminé la section 3.2 ! Vous maîtrisez maintenant le clonage et le fork avec toutes leurs subtilités. Dans la prochaine section, nous explorerons comment naviguer efficacement dans l'écosystème GitHub.*

⏭️
