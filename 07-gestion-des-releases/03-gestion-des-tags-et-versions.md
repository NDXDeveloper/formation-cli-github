🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 7.3 Gestion des tags et versions

## Introduction

Les tags Git et le versioning sont les fondations d'une gestion de releases efficace. Un système de versioning cohérent permet à vos utilisateurs de comprendre l'évolution de votre projet et de choisir la version qui leur convient.

Dans cette section, nous allons explorer comment bien gérer les tags Git, adopter un système de versioning professionnel, et utiliser la CLI GitHub pour maintenir un historique de versions claire et utile.

## Comprendre les tags Git

### Qu'est-ce qu'un tag Git ?

Un **tag Git** est un pointeur vers un commit spécifique qui ne bouge jamais. Contrairement aux branches qui évoluent, un tag marque un point fixe dans l'historique de votre projet.

**Analogie simple :** Si votre historique Git est comme un livre, les tags sont comme des marque-pages qui marquent des chapitres importants (vos releases).

### Types de tags

**Tag léger (lightweight) :**
```bash
git tag v1.0.0
```
Simple pointeur vers un commit, sans métadonnées additionnelles.

**Tag annoté (annotated) :**
```bash
git tag -a v1.0.0 -m "Version 1.0.0 - Première release stable"
```
Contient des métadonnées : auteur, date, message, signature GPG possible.

**⭐ Recommandation :** Utilisez toujours des tags annotés pour les releases, ils sont plus riches en informations.

## Versioning sémantique (SemVer)

### Format de base

Le versioning sémantique suit le format `MAJOR.MINOR.PATCH` :

```
v1.2.3
 │ │ └── PATCH: Corrections de bugs (rétrocompatible)
 │ └──── MINOR: Nouvelles fonctionnalités (rétrocompatible)
 └────── MAJOR: Changements incompatibles
```

### Règles de versioning

**Incrément PATCH (1.0.0 → 1.0.1) :**
- Corrections de bugs
- Améliorations de sécurité
- Optimisations internes
- Corrections de documentation

**Incrément MINOR (1.0.1 → 1.1.0) :**
- Nouvelles fonctionnalités
- Améliorations d'API existantes
- Nouvelles options de configuration
- Fonctionnalités dépréciées (mais toujours fonctionnelles)

**Incrément MAJOR (1.1.0 → 2.0.0) :**
- Suppression de fonctionnalités
- Modifications incompatibles d'API
- Changements de comportement
- Nouvelles versions minimales requises

### Versions de pré-release

**Format :** `MAJOR.MINOR.PATCH-IDENTIFIANT.NUMERO`

```bash
v1.0.0-alpha.1    # Première version alpha
v1.0.0-alpha.2    # Deuxième version alpha
v1.0.0-beta.1     # Première version beta
v1.0.0-rc.1       # Release candidate 1
v1.0.0            # Version finale
```

**Types de pré-release :**
- **alpha** : Version très précoce, instable
- **beta** : Fonctionnalités complètes, mais bugs possibles
- **rc** (release candidate) : Potentiellement la version finale

## Création et gestion des tags

### Créer un tag local

```bash
# Tag annoté avec message
git tag -a v1.0.0 -m "Version 1.0.0 - Première release stable"

# Tag à partir d'un commit spécifique
git tag -a v1.0.0 9fceb02 -m "Version 1.0.0"

# Voir les tags locaux
git tag
git tag -l "v1.*"  # Filtrer les tags
```

### Pousser les tags vers GitHub

```bash
# Pousser un tag spécifique
git push origin v1.0.0

# Pousser tous les tags
git push origin --tags

# Pousser commits ET tags en une fois
git push origin main --follow-tags
```

### Supprimer des tags

```bash
# Supprimer un tag local
git tag -d v1.0.0

# Supprimer un tag distant
git push origin --delete v1.0.0

# Supprimer local ET distant
git tag -d v1.0.0
git push origin --delete v1.0.0
```

## Workflow de versioning avec CLI GitHub

### Workflow complet manuel

```bash
#!/bin/bash
# release-workflow.sh - Workflow complet de release

# 1. Vérifications préalables
echo "🔍 Vérifications préalables..."

# Vérifier qu'on est sur main et à jour
git checkout main
git pull origin main

# Vérifier qu'il n'y a pas de changements non commités
if [ -n "$(git status --porcelain)" ]; then
    echo "❌ Il y a des changements non commités"
    exit 1
fi

# 2. Demander la nouvelle version
echo "📋 Versions existantes :"
git tag | sort -V | tail -5

read -p "🏷️  Nouvelle version (ex: v1.2.3) : " NEW_VERSION

# Vérifier que le tag n'existe pas déjà
if git tag | grep -q "^$NEW_VERSION$"; then
    echo "❌ Le tag $NEW_VERSION existe déjà"
    exit 1
fi

# 3. Mise à jour du numéro de version dans les fichiers
echo "📝 Mise à jour des fichiers de version..."

# Exemple pour package.json (Node.js)
if [ -f "package.json" ]; then
    npm version ${NEW_VERSION#v} --no-git-tag-version
fi

# Exemple pour un fichier VERSION
echo "${NEW_VERSION#v}" > VERSION

# 4. Commit des changements de version
git add .
git commit -m "chore: bump version to $NEW_VERSION"

# 5. Créer le tag annoté
echo "🏷️  Création du tag $NEW_VERSION..."
git tag -a $NEW_VERSION -m "Release $NEW_VERSION"

# 6. Pousser tout
echo "🚀 Push vers GitHub..."
git push origin main
git push origin $NEW_VERSION

# 7. Créer la release GitHub
echo "📦 Création de la release GitHub..."
gh release create $NEW_VERSION \
    --title "Release $NEW_VERSION" \
    --generate-notes

echo "✅ Release $NEW_VERSION créée avec succès !"
```

### Automatisation du versioning

**Script de bump automatique :**
```bash
#!/bin/bash
# bump-version.sh - Incrément automatique de version

BUMP_TYPE=$1  # patch, minor, major

if [ -z "$BUMP_TYPE" ]; then
    echo "Usage: ./bump-version.sh [patch|minor|major]"
    exit 1
fi

# Obtenir la dernière version
LAST_VERSION=$(git tag | grep '^v' | sort -V | tail -1)
echo "📋 Dernière version : $LAST_VERSION"

# Extraire les numéros
LAST_VERSION_CLEAN=${LAST_VERSION#v}
IFS='.' read -r MAJOR MINOR PATCH <<< "$LAST_VERSION_CLEAN"

# Calculer la nouvelle version
case $BUMP_TYPE in
    "patch")
        PATCH=$((PATCH + 1))
        ;;
    "minor")
        MINOR=$((MINOR + 1))
        PATCH=0
        ;;
    "major")
        MAJOR=$((MAJOR + 1))
        MINOR=0
        PATCH=0
        ;;
    *)
        echo "❌ Type de bump invalide : $BUMP_TYPE"
        exit 1
        ;;
esac

NEW_VERSION="v$MAJOR.$MINOR.$PATCH"
echo "🚀 Nouvelle version : $NEW_VERSION"

# Créer le tag et la release
git tag -a $NEW_VERSION -m "Release $NEW_VERSION"
git push origin $NEW_VERSION

gh release create $NEW_VERSION \
    --title "Release $NEW_VERSION" \
    --generate-notes

echo "✅ Version $NEW_VERSION créée !"
```

## Gestion des branches et releases

### Stratégie Git Flow

**Branches principales :**
- `main` : Code en production
- `develop` : Code en développement

**Workflow de release :**
```bash
# 1. Créer une branche de release
git checkout develop
git pull origin develop
git checkout -b release/v1.2.0

# 2. Finaliser la release (tests, documentation)
# ... travail sur la branche release ...

# 3. Merger vers main
git checkout main
git merge --no-ff release/v1.2.0

# 4. Créer le tag
git tag -a v1.2.0 -m "Release v1.2.0"

# 5. Merger vers develop
git checkout develop
git merge --no-ff release/v1.2.0

# 6. Nettoyer
git branch -d release/v1.2.0

# 7. Pousser tout
git push origin main develop --tags

# 8. Créer la release GitHub
gh release create v1.2.0 --title "Release v1.2.0" --generate-notes
```

### Stratégie GitHub Flow (simplifiée)

**Workflow simplifié :**
```bash
# 1. Développement sur main avec protection
git checkout main
git pull origin main

# 2. Quand prêt pour release
NEW_VERSION="v1.2.0"
git tag -a $NEW_VERSION -m "Release $NEW_VERSION"
git push origin $NEW_VERSION

# 3. Créer la release
gh release create $NEW_VERSION \
    --title "Release $NEW_VERSION" \
    --generate-notes
```

## Gestion des releases avec la CLI GitHub

### Lister les releases existantes

```bash
# Voir toutes les releases
gh release list

# Voir seulement les 5 dernières
gh release list --limit 5

# Filtrer par état
gh release list --exclude-drafts
gh release list --exclude-pre-releases
```

### Examiner une release spécifique

```bash
# Voir les détails d'une release
gh release view v1.0.0

# Voir au format JSON (pour scripts)
gh release view v1.0.0 --json tagName,name,publishedAt,assets
```

### Modifier une release existante

```bash
# Changer le titre
gh release edit v1.0.0 --title "Nouveau titre"

# Modifier les notes
gh release edit v1.0.0 --notes "Nouvelles notes de version"

# Marquer comme pre-release
gh release edit v1.0.0 --prerelease

# Marquer comme draft
gh release edit v1.0.0 --draft
```

### Supprimer une release

```bash
# Supprimer une release (garde le tag)
gh release delete v1.0.0

# Supprimer release ET tag
gh release delete v1.0.0 --yes
git push origin --delete v1.0.0  # Supprimer le tag distant
```

## Bonnes pratiques de versioning

### Règles de cohérence

**1. Toujours utiliser le même format :**
```bash
✅ Bon : v1.0.0, v1.1.0, v2.0.0
❌ Mauvais : v1.0.0, 1.1, version-2.0
```

**2. Être prévisible dans les incréments :**
```bash
✅ Bon : v1.0.0 → v1.0.1 → v1.1.0 → v2.0.0
❌ Mauvais : v1.0.0 → v1.5.0 → v1.4.1 → v3.0.0
```

**3. Utiliser des tags annotés :**
```bash
✅ Bon : git tag -a v1.0.0 -m "Release v1.0.0"
❌ Mauvais : git tag v1.0.0
```

### Automatisation avec outils

**conventional-commits et semantic-release :**
```json
// package.json
{
  "scripts": {
    "release": "semantic-release"
  },
  "release": {
    "plugins": [
      "@semantic-release/commit-analyzer",
      "@semantic-release/release-notes-generator",
      "@semantic-release/github"
    ]
  }
}
```

**standard-version :**
```bash
# Installation
npm install -g standard-version

# Utilisation
standard-version          # Bump automatique basé sur les commits
standard-version --release-as major  # Force un bump major
```

## Gestion des versions de développement

### Versions snapshot/nightly

Pour les versions de développement régulières :

```bash
#!/bin/bash
# nightly-release.sh

DATE=$(date +%Y%m%d)
COMMIT=$(git rev-parse --short HEAD)
VERSION="v0.0.0-nightly.$DATE.$COMMIT"

echo "🌙 Création de la version nightly : $VERSION"

gh release create $VERSION \
    --title "Nightly Build $DATE" \
    --prerelease \
    --notes "🌙 Build automatique du $(date)

⚠️ Cette version est automatiquement générée pour les tests.
Ne pas utiliser en production.

Commit: $COMMIT"

echo "✅ Version nightly créée : $VERSION"
```

### Versions de hotfix

Workflow pour correctifs urgents :

```bash
#!/bin/bash
# hotfix-release.sh

# Obtenir la dernière version
LAST_VERSION=$(git tag | grep '^v' | sort -V | tail -1)
LAST_VERSION_CLEAN=${LAST_VERSION#v}

# Incrémenter le patch
IFS='.' read -r MAJOR MINOR PATCH <<< "$LAST_VERSION_CLEAN"
PATCH=$((PATCH + 1))
HOTFIX_VERSION="v$MAJOR.$MINOR.$PATCH"

echo "🚨 Création du hotfix : $HOTFIX_VERSION"

# Créer depuis main
git checkout main
git pull origin main

# Créer le tag
git tag -a $HOTFIX_VERSION -m "Hotfix $HOTFIX_VERSION"
git push origin $HOTFIX_VERSION

# Créer la release
gh release create $HOTFIX_VERSION \
    --title "Hotfix $HOTFIX_VERSION" \
    --notes "🚨 Correction urgente

Cette version corrige des problèmes critiques identifiés dans $LAST_VERSION."

echo "✅ Hotfix $HOTFIX_VERSION déployé !"
```

## Migration et nettoyage des tags

### Renommer un tag

```bash
# Méthode sûre pour renommer un tag
OLD_TAG="v1.0.0-beta"
NEW_TAG="v1.0.0-beta.1"

# Créer le nouveau tag au même endroit
git tag $NEW_TAG $OLD_TAG

# Supprimer l'ancien
git tag -d $OLD_TAG
git push origin --delete $OLD_TAG

# Pousser le nouveau
git push origin $NEW_TAG

# Mettre à jour la release GitHub
gh release edit $OLD_TAG --tag $NEW_TAG
```

### Nettoyer les anciens tags

```bash
#!/bin/bash
# cleanup-old-tags.sh

echo "🧹 Nettoyage des anciens tags..."

# Supprimer les tags alpha/beta anciens (garde les 5 derniers)
git tag | grep -E 'alpha|beta' | head -n -5 | while read tag; do
    echo "Suppression de $tag"
    git tag -d $tag
    git push origin --delete $tag 2>/dev/null || true
done

echo "✅ Nettoyage terminé"
```

## Points clés à retenir

✅ **Tags annotés** sont recommandés pour les releases officielles

✅ **Versioning sémantique** apporte clarté et prévisibilité

✅ **Workflow cohérent** entre tags Git et releases GitHub

✅ **Automatisation** réduit les erreurs et améliore la productivité

✅ **Branches de release** permettent de finaliser sans bloquer le développement

✅ **Nettoyage régulier** maintient un historique propre

✅ **Scripts réutilisables** standardisent le processus dans l'équipe

## Prochaine étape

Maintenant que vous maîtrisez la gestion des tags et versions, nous allons apprendre à automatiser complètement le processus de release pour gagner en efficacité et en fiabilité.


⏭️
