🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 3.4 - Paramètres et configuration des repos

## 🎯 Introduction

Créer un repository n'est que le début ! Pour qu'il soit vraiment efficace, sécurisé et adapté à vos besoins, vous devez le configurer correctement. La CLI GitHub vous permet de modifier tous les paramètres importants de vos repositories directement depuis votre terminal, sans jamais ouvrir l'interface web.

**Analogie** : Si créer un repository c'est comme acheter une maison, la configuration c'est comme l'aménager, installer la sécurité, choisir qui peut entrer, et optimiser chaque pièce selon vos besoins !

## ⚙️ Vue d'ensemble de `gh repo edit`

### Commande principale

La commande `gh repo edit` est votre outil central pour modifier tous les paramètres d'un repository.

```bash
# Syntaxe de base
gh repo edit [repository] [options]
```

### Paramètres configurables

**Informations de base** :
- Description du projet
- Page d'accueil (homepage)
- Visibilité (public/privé)
- Branche par défaut

**Fonctionnalités** :
- Issues activées/désactivées
- Wiki activé/désactivé
- Projets activés/désactivés
- Discussions activées/désactivées

**Sécurité et collaboration** :
- Protection de branches
- Gestion des collaborateurs
- Paramètres de merge

**Organisation** :
- Topics (tags)
- Template de repository

## 📝 Configuration des informations de base

### Modifier la description

```bash
# Description simple
gh repo edit --description "Une application web moderne en React"

# Description longue
gh repo edit --description "Application de gestion de projets avec interface moderne, développée en React et TypeScript, incluant authentification et API REST"
```

**Pourquoi c'est important** :
- ✅ **Première impression** : Visible sur la page principale
- ✅ **Référencement** : Aide à la découverte
- ✅ **Documentation** : Explique rapidement le projet

### Configurer la page d'accueil

```bash
# Site web du projet
gh repo edit --homepage "https://mon-super-projet.com"

# Documentation
gh repo edit --homepage "https://mon-projet.readthedocs.io"

# Démo en ligne
gh repo edit --homepage "https://mon-projet.netlify.app"
```

### Changer la visibilité

```bash
# Rendre public
gh repo edit --visibility public

# Rendre privé
gh repo edit --visibility private

# Interne (pour GitHub Enterprise)
gh repo edit --visibility internal
```

**⚠️ Attention** : Changer de privé à public expose tout l'historique !

### Modifier la branche par défaut

```bash
# Changer la branche par défaut vers 'develop'
gh repo edit --default-branch develop

# Changer vers 'main' (moderne)
gh repo edit --default-branch main
```

**Bonnes pratiques** :
- `main` est maintenant la convention moderne
- `develop` pour les projets avec workflow Git Flow
- Évitez `master` (terminologie obsolète)

## 🎯 Gestion des fonctionnalités

### Issues et discussions

```bash
# Activer les issues
gh repo edit --enable-issues

# Désactiver les issues
gh repo edit --disable-issues

# Activer les discussions (communauté)
gh repo edit --enable-discussions

# Désactiver les discussions
gh repo edit --disable-discussions
```

**Quand utiliser les discussions** :
- ✅ **Projets open source** avec communauté
- ✅ **Support utilisateur** et FAQ
- ✅ **Annonces** et nouvelles
- ❌ **Projets privés** simples

### Wiki du projet

```bash
# Activer le wiki
gh repo edit --enable-wiki

# Désactiver le wiki
gh repo edit --disable-wiki
```

**Alternatives au wiki** :
- Documentation dans `docs/` folder
- README.md détaillé
- Site de documentation externe (GitBook, etc.)

### Projets GitHub

```bash
# Activer les projets
gh repo edit --enable-projects

# Désactiver les projets
gh repo edit --disable-projects
```

**Projets GitHub utiles pour** :
- 📋 **Gestion de tâches** type Kanban
- 🎯 **Planification** de releases
- 👥 **Coordination d'équipe**

## 🏷️ Organisation avec les topics

### Ajouter des topics

```bash
# Ajouter un topic
gh repo edit --add-topic javascript

# Ajouter plusieurs topics
gh repo edit --add-topic react --add-topic typescript --add-topic webapp

# Topics avec espaces (utiliser des tirets)
gh repo edit --add-topic machine-learning --add-topic artificial-intelligence
```

### Supprimer des topics

```bash
# Supprimer un topic
gh repo edit --remove-topic old-tag

# Voir les topics actuels
gh repo view --json topics -q '.topics[]'
```

### Topics recommandés par domaine

#### Applications web
```bash
gh repo edit --add-topic webapp --add-topic frontend --add-topic react --add-topic typescript
```

#### APIs et backends
```bash
gh repo edit --add-topic api --add-topic backend --add-topic rest --add-topic nodejs
```

#### Outils CLI
```bash
gh repo edit --add-topic cli --add-topic tool --add-topic productivity --add-topic automation
```

#### Bibliothèques
```bash
gh repo edit --add-topic library --add-topic npm --add-topic package --add-topic javascript
```

#### Machine Learning
```bash
gh repo edit --add-topic machine-learning --add-topic ai --add-topic python --add-topic tensorflow
```

## 👥 Gestion des collaborateurs

### Ajouter des collaborateurs

```bash
# Ajouter un collaborateur
gh repo edit --add-collaborator alice

# Ajouter plusieurs collaborateurs
gh repo edit --add-collaborator bob --add-collaborator charlie
```

### Supprimer des collaborateurs

```bash
# Supprimer un collaborateur
gh repo edit --remove-collaborator old-member
```

### Voir les collaborateurs actuels

```bash
# Lister les collaborateurs
gh api repos/:owner/:repo/collaborators | jq -r '.[].login'

# Voir avec leurs permissions
gh api repos/:owner/:repo/collaborators | jq -r '.[] | "\(.login): \(.permissions.admin // false | if . then "admin" else "write" end)"'
```

## 🔒 Configuration de sécurité

### Templates de repository

```bash
# Activer comme template
gh repo edit --enable-template

# Désactiver le template
gh repo edit --disable-template
```

**Repository template utile pour** :
- 🏗️ **Structure** de projet standardisée
- ⚙️ **Configuration** pré-définie
- 📋 **Workflow** GitHub Actions inclus
- 📚 **Documentation** de base

### Paramètres de merge

```bash
# Permettre les squash merges (recommandé)
gh repo edit --enable-squash-merge

# Permettre les merge commits classiques
gh repo edit --enable-merge-commits

# Permettre les rebase merges
gh repo edit --enable-rebase-merge

# Désactiver un type de merge
gh repo edit --disable-squash-merge
```

**Types de merge expliqués** :

#### Squash Merge (recommandé)
- ✅ **Historique propre** : Un commit par feature
- ✅ **Lisibilité** : Messages de commit clairs
- ✅ **Rollback facile** : Annuler une feature complète

#### Merge Commit
- ✅ **Historique complet** : Conserve tous les commits
- ❌ **Historique complexe** : Peut devenir difficile à lire

#### Rebase Merge
- ✅ **Historique linéaire** : Pas de commits de merge
- ⚠️ **Plus technique** : Nécessite compréhension du rebase

### Suppression automatique des branches

```bash
# Activer la suppression auto des branches après merge
gh repo edit --delete-branch-on-merge

# Désactiver
gh repo edit --delete-branch-on-merge=false
```

**Avantages** :
- ✅ **Repository propre** : Pas d'accumulation de branches
- ✅ **Maintenance réduite** : Nettoyage automatique
- ✅ **Focus** : Seules les branches actives visibles

## 🛡️ Protection de branches

### Configuration de base

```bash
# Protéger la branche main
gh api repos/:owner/:repo/branches/main/protection \
  --method PUT \
  --field required_status_checks='{"strict":true,"contexts":[]}' \
  --field enforce_admins=true \
  --field required_pull_request_reviews='{"required_approving_review_count":1}' \
  --field restrictions=null
```

### Règles de protection courantes

#### Projet personnel
```bash
# Protection minimale : juste les PRs
gh api repos/:owner/:repo/branches/main/protection \
  --method PUT \
  --field required_pull_request_reviews='{"required_approving_review_count":1}' \
  --field enforce_admins=false
```

#### Projet d'équipe
```bash
# Protection renforcée avec CI
gh api repos/:owner/:repo/branches/main/protection \
  --method PUT \
  --field required_status_checks='{"strict":true,"contexts":["ci/build","ci/test"]}' \
  --field required_pull_request_reviews='{"required_approving_review_count":2,"dismiss_stale_reviews":true}' \
  --field enforce_admins=true
```

#### Projet open source
```bash
# Protection maximale
gh api repos/:owner/:repo/branches/main/protection \
  --method PUT \
  --field required_status_checks='{"strict":true,"contexts":["ci/build","ci/test","ci/security"]}' \
  --field required_pull_request_reviews='{"required_approving_review_count":1,"require_code_owner_reviews":true}' \
  --field enforce_admins=true \
  --field required_linear_history=true
```

## 🎨 Configurations par type de projet

### Projet personnel/expérimental

```bash
# Configuration légère et flexible
gh repo edit \
  --description "Projet expérimental pour tester de nouvelles idées" \
  --enable-issues \
  --disable-wiki \
  --disable-projects \
  --add-topic experiment \
  --add-topic personal \
  --enable-squash-merge \
  --disable-merge-commits \
  --delete-branch-on-merge
```

### Bibliothèque/Package open source

```bash
# Configuration pour bibliothèque publique
gh repo edit \
  --description "Bibliothèque JavaScript moderne pour [fonction]" \
  --homepage "https://mon-package.npmjs.com" \
  --enable-issues \
  --enable-discussions \
  --enable-wiki \
  --enable-template \
  --add-topic library \
  --add-topic npm \
  --add-topic javascript \
  --add-topic opensource \
  --enable-squash-merge \
  --delete-branch-on-merge
```

### Application web/mobile

```bash
# Configuration pour application
gh repo edit \
  --description "Application web moderne avec React et TypeScript" \
  --homepage "https://mon-app.vercel.app" \
  --enable-issues \
  --enable-projects \
  --disable-wiki \
  --add-topic webapp \
  --add-topic react \
  --add-topic typescript \
  --add-topic frontend \
  --enable-squash-merge \
  --delete-branch-on-merge
```

### Projet d'équipe/entreprise

```bash
# Configuration pour travail collaboratif
gh repo edit \
  --description "Projet interne - Plateforme de gestion clients" \
  --visibility private \
  --enable-issues \
  --enable-projects \
  --enable-discussions \
  --add-topic internal \
  --add-topic enterprise \
  --add-topic crm \
  --enable-squash-merge \
  --delete-branch-on-merge \
  --add-collaborator alice \
  --add-collaborator bob
```

### Documentation/Blog

```bash
# Configuration pour site de documentation
gh repo edit \
  --description "Documentation technique du projet X" \
  --homepage "https://docs.projet-x.com" \
  --enable-issues \
  --disable-projects \
  --enable-wiki \
  --add-topic documentation \
  --add-topic markdown \
  --add-topic static-site \
  --enable-squash-merge \
  --delete-branch-on-merge
```

## 🔧 Scripts de configuration automatisée

### Script de configuration standard

```bash
#!/bin/bash
# setup-repo.sh

REPO_NAME=$1
REPO_TYPE=$2

if [ -z "$REPO_NAME" ] || [ -z "$REPO_TYPE" ]; then
    echo "Usage: $0 <repo-name> <type>"
    echo "Types: webapp, library, tool, docs, personal"
    exit 1
fi

echo "⚙️ Configuration du repository $REPO_NAME (type: $REPO_TYPE)"

case "$REPO_TYPE" in
    "webapp")
        echo "🌐 Configuration pour application web..."
        gh repo edit "$REPO_NAME" \
          --enable-issues \
          --enable-projects \
          --disable-wiki \
          --add-topic webapp \
          --add-topic frontend \
          --enable-squash-merge \
          --delete-branch-on-merge
        ;;

    "library")
        echo "📚 Configuration pour bibliothèque..."
        gh repo edit "$REPO_NAME" \
          --enable-issues \
          --enable-discussions \
          --enable-wiki \
          --enable-template \
          --add-topic library \
          --add-topic opensource \
          --enable-squash-merge \
          --delete-branch-on-merge
        ;;

    "tool")
        echo "🛠️ Configuration pour outil CLI..."
        gh repo edit "$REPO_NAME" \
          --enable-issues \
          --disable-projects \
          --disable-wiki \
          --add-topic cli \
          --add-topic tool \
          --add-topic productivity \
          --enable-squash-merge \
          --delete-branch-on-merge
        ;;

    "docs")
        echo "📖 Configuration pour documentation..."
        gh repo edit "$REPO_NAME" \
          --enable-issues \
          --disable-projects \
          --enable-wiki \
          --add-topic documentation \
          --add-topic markdown \
          --enable-squash-merge \
          --delete-branch-on-merge
        ;;

    "personal")
        echo "👤 Configuration pour projet personnel..."
        gh repo edit "$REPO_NAME" \
          --enable-issues \
          --disable-projects \
          --disable-wiki \
          --add-topic personal \
          --add-topic experiment \
          --enable-squash-merge \
          --delete-branch-on-merge
        ;;

    *)
        echo "❌ Type de projet non reconnu: $REPO_TYPE"
        exit 1
        ;;
esac

echo "✅ Configuration terminée pour $REPO_NAME"
```

### Script de migration de configuration

```bash
#!/bin/bash
# migrate-config.sh

SOURCE_REPO=$1
TARGET_REPO=$2

if [ -z "$SOURCE_REPO" ] || [ -z "$TARGET_REPO" ]; then
    echo "Usage: $0 <source-repo> <target-repo>"
    exit 1
fi

echo "🔄 Migration de configuration de $SOURCE_REPO vers $TARGET_REPO"

# Récupérer la configuration source
CONFIG=$(gh repo view "$SOURCE_REPO" --json description,homepageUrl,topics,hasIssuesEnabled,hasProjectsEnabled,hasWikiEnabled)

DESCRIPTION=$(echo "$CONFIG" | jq -r '.description // empty')
HOMEPAGE=$(echo "$CONFIG" | jq -r '.homepageUrl // empty')
TOPICS=$(echo "$CONFIG" | jq -r '.topics[]?' | tr '\n' ' ')
HAS_ISSUES=$(echo "$CONFIG" | jq -r '.hasIssuesEnabled')
HAS_PROJECTS=$(echo "$CONFIG" | jq -r '.hasProjectsEnabled')
HAS_WIKI=$(echo "$CONFIG" | jq -r '.hasWikiEnabled')

# Appliquer la configuration
CMD="gh repo edit $TARGET_REPO"

[ -n "$DESCRIPTION" ] && CMD="$CMD --description \"$DESCRIPTION\""
[ -n "$HOMEPAGE" ] && CMD="$CMD --homepage \"$HOMEPAGE\""

for topic in $TOPICS; do
    CMD="$CMD --add-topic $topic"
done

[ "$HAS_ISSUES" = "true" ] && CMD="$CMD --enable-issues" || CMD="$CMD --disable-issues"
[ "$HAS_PROJECTS" = "true" ] && CMD="$CMD --enable-projects" || CMD="$CMD --disable-projects"
[ "$HAS_WIKI" = "true" ] && CMD="$CMD --enable-wiki" || CMD="$CMD --disable-wiki"

echo "Exécution: $CMD"
eval "$CMD"

echo "✅ Migration terminée"
```

## 📊 Audit et validation

### Script de vérification de configuration

```bash
#!/bin/bash
# audit-repo.sh

REPO=${1:-$(basename $(pwd))}

echo "🔍 Audit de configuration pour $REPO"
echo "===================================="

# Récupérer toutes les informations
INFO=$(gh repo view "$REPO" --json name,description,homepageUrl,isPrivate,topics,hasIssuesEnabled,hasProjectsEnabled,hasWikiEnabled,defaultBranch)

echo ""
echo "📝 Informations de base:"
echo "  Nom: $(echo "$INFO" | jq -r '.name')"
echo "  Description: $(echo "$INFO" | jq -r '.description // "❌ Aucune description"')"
echo "  Homepage: $(echo "$INFO" | jq -r '.homepageUrl // "❌ Aucune homepage"')"
echo "  Visibilité: $(echo "$INFO" | jq -r 'if .isPrivate then "Privé" else "Public" end')"
echo "  Branche par défaut: $(echo "$INFO" | jq -r '.defaultBranch')"

echo ""
echo "🎯 Fonctionnalités:"
echo "  Issues: $(echo "$INFO" | jq -r 'if .hasIssuesEnabled then "✅ Activées" else "❌ Désactivées" end')"
echo "  Projets: $(echo "$INFO" | jq -r 'if .hasProjectsEnabled then "✅ Activés" else "❌ Désactivés" end')"
echo "  Wiki: $(echo "$INFO" | jq -r 'if .hasWikiEnabled then "✅ Activé" else "❌ Désactivé" end')"

echo ""
echo "🏷️ Topics:"
TOPICS=$(echo "$INFO" | jq -r '.topics[]?' | tr '\n' ', ' | sed 's/,$//')
if [ -n "$TOPICS" ]; then
    echo "  $TOPICS"
else
    echo "  ❌ Aucun topic défini"
fi

echo ""
echo "🔒 Protection de branches:"
PROTECTION=$(gh api repos/:owner/:repo/branches/$(echo "$INFO" | jq -r '.defaultBranch')/protection 2>/dev/null)
if [ $? -eq 0 ]; then
    echo "  ✅ Branche protégée"
    echo "  PRs requises: $(echo "$PROTECTION" | jq -r '.required_pull_request_reviews.required_approving_review_count // 0')"
else
    echo "  ❌ Branche non protégée"
fi
```

### Checklist de configuration

```bash
#!/bin/bash
# config-checklist.sh

REPO=${1:-$(basename $(pwd))}

echo "📋 Checklist de configuration pour $REPO"
echo "========================================"

INFO=$(gh repo view "$REPO" --json description,homepageUrl,topics,hasIssuesEnabled,defaultBranch)

# Vérifications essentielles
echo ""
echo "✅ Vérifications essentielles:"

# Description
if [ "$(echo "$INFO" | jq -r '.description')" != "null" ]; then
    echo "  ✅ Description définie"
else
    echo "  ❌ Description manquante"
    echo "     → gh repo edit --description \"Description de votre projet\""
fi

# Topics
TOPIC_COUNT=$(echo "$INFO" | jq '.topics | length')
if [ "$TOPIC_COUNT" -gt 0 ]; then
    echo "  ✅ Topics définis ($TOPIC_COUNT)"
else
    echo "  ❌ Aucun topic défini"
    echo "     → gh repo edit --add-topic votre-langage --add-topic votre-domaine"
fi

# Branche par défaut moderne
DEFAULT_BRANCH=$(echo "$INFO" | jq -r '.defaultBranch')
if [ "$DEFAULT_BRANCH" = "main" ]; then
    echo "  ✅ Branche par défaut moderne (main)"
elif [ "$DEFAULT_BRANCH" = "master" ]; then
    echo "  ⚠️  Branche par défaut obsolète (master)"
    echo "     → gh repo edit --default-branch main"
else
    echo "  ℹ️  Branche par défaut: $DEFAULT_BRANCH"
fi

# Issues
if [ "$(echo "$INFO" | jq -r '.hasIssuesEnabled')" = "true" ]; then
    echo "  ✅ Issues activées"
else
    echo "  ⚠️  Issues désactivées"
    echo "     → gh repo edit --enable-issues"
fi

echo ""
echo "🎯 Recommandations avancées:"
echo "  → Configurer la protection de branche"
echo "  → Ajouter une homepage si applicable"
echo "  → Définir des templates d'issues/PRs"
echo "  → Configurer les paramètres de merge"
```

## 📋 Récapitulatif des commandes essentielles

### Informations de base

```bash
# Modifier description et homepage
gh repo edit --description "Description du projet" --homepage "https://site.com"

# Changer visibilité
gh repo edit --visibility public

# Changer branche par défaut
gh repo edit --default-branch main
```

### Fonctionnalités

```bash
# Activer/désactiver les issues
gh repo edit --enable-issues
gh repo edit --disable-issues

# Activer/désactiver les projets
gh repo edit --enable-projects
gh repo edit --disable-projects

# Activer/désactiver le wiki
gh repo edit --enable-wiki
gh repo edit --disable-wiki
```

### Topics et collaboration

```bash
# Ajouter/supprimer des topics
gh repo edit --add-topic javascript --add-topic webapp
gh repo edit --remove-topic old-topic

# Gérer les collaborateurs
gh repo edit --add-collaborator username
gh repo edit --remove-collaborator username
```

### Paramètres de merge

```bash
# Configurer les types de merge
gh repo edit --enable-squash-merge --disable-merge-commits
gh repo edit --delete-branch-on-merge

# Activer comme template
gh repo edit --enable-template
```

## 🎯 Prochaine étape

Félicitations ! Vous avez terminé la section complète sur la gestion des repositories. Vous maîtrisez maintenant la création, le clonage, l'exploration et la configuration de repositories GitHub comme un expert !

Il est temps de passer au niveau supérieur avec la gestion des issues - l'outil de suivi et d'organisation de vos projets.

👉 **Continuez avec** : [Section 4 - Workflow avec les Issues](../04-workflow-avec-les-issues/README.md)

---

*Vous avez terminé la section 3.4 et toute la section 3 ! Vous maîtrisez maintenant la gestion complète des repositories GitHub. Dans la prochaine section, nous explorerons le workflow avec les issues pour organiser et suivre vos projets.*

## 🎉 Récapitulatif de la Section 3

Vous venez de terminer toute la section "Gestion des Repositories" ! Voici ce que vous maîtrisez maintenant :

✅ **3.1** - Création de repositories avec toutes les options avancées
✅ **3.2** - Clonage et fork pour travailler avec des projets existants
✅ **3.3** - Exploration et découverte de l'écosystème GitHub
✅ **3.4** - Configuration et paramétrage avancés des repositories

**Vous avez maintenant** : Une maîtrise complète du cycle de vie des repositories GitHub ! 🏆

⏭️
