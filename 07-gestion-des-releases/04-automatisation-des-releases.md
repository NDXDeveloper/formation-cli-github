🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 7.4 Automatisation des releases

## Introduction

L'automatisation des releases est le saint graal de la gestion de versions : créer des releases cohérentes, fiables et reproductibles sans intervention manuelle. Cette approche élimine les erreurs humaines, accélère le processus et garantit que chaque release suit exactement les mêmes étapes.

Dans cette section, nous allons explorer comment automatiser complètement vos releases avec GitHub Actions, des scripts intelligents et la CLI GitHub, du simple déclenchement automatique aux pipelines sophistiqués de release.

## Pourquoi automatiser les releases ?

### Avantages de l'automatisation

**🎯 Consistance :**
- Chaque release suit exactement les mêmes étapes
- Élimination des variations dues aux erreurs humaines
- Process standardisé pour toute l'équipe

**⚡ Rapidité :**
- Release en quelques minutes au lieu d'heures
- Déploiement possible à tout moment
- Réduction du temps entre développement et livraison

**🔒 Fiabilité :**
- Tests automatiques avant chaque release
- Validation des prérequis
- Rollback automatique en cas de problème

**📊 Traçabilité :**
- Logs complets de chaque étape
- Historique des releases et de leurs composants
- Audit trail pour la conformité

### Cas d'usage typiques

- **Applications web :** Déploiement automatique après merge sur main
- **Bibliothèques :** Publication NPM/PyPI synchronisée avec GitHub
- **Applications mobiles :** Build et distribution automatiques
- **Outils CLI :** Compilation multi-plateformes et distribution

## Stratégies d'automatisation

### 1. Déclenchement par tag

**Principe :** Créer un tag déclenche automatiquement la release.

```yaml
# .github/workflows/release-on-tag.yml
name: Release on Tag

on:
  push:
    tags: ['v*']

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Create Release
        run: |
          gh release create ${{ github.ref_name }} \
            --title "Release ${{ github.ref_name }}" \
            --generate-notes
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**Workflow utilisateur :**
```bash
# Développeur crée et pousse un tag
git tag v1.2.3
git push origin v1.2.3

# → GitHub Actions crée automatiquement la release
```

### 2. Déclenchement par merge sur main

**Principe :** Chaque merge sur la branche principale crée une release automatique.

```yaml
# .github/workflows/auto-release.yml
name: Auto Release

on:
  push:
    branches: [main]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0  # Nécessaire pour l'historique des tags

      - name: Calculate next version
        id: version
        run: |
          # Obtenir la dernière version
          LAST_TAG=$(git tag | grep '^v' | sort -V | tail -1)
          if [ -z "$LAST_TAG" ]; then
            NEW_VERSION="v1.0.0"
          else
            # Incrémenter le patch
            LAST_VERSION=${LAST_TAG#v}
            IFS='.' read -r MAJOR MINOR PATCH <<< "$LAST_VERSION"
            PATCH=$((PATCH + 1))
            NEW_VERSION="v$MAJOR.$MINOR.$PATCH"
          fi
          echo "version=$NEW_VERSION" >> $GITHUB_OUTPUT
          echo "Nouvelle version: $NEW_VERSION"

      - name: Create Release
        run: |
          gh release create ${{ steps.version.outputs.version }} \
            --title "Release ${{ steps.version.outputs.version }}" \
            --generate-notes
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### 3. Déclenchement manuel avec paramètres

**Principe :** Release manuelle avec contrôle du type de version.

```yaml
# .github/workflows/manual-release.yml
name: Manual Release

on:
  workflow_dispatch:
    inputs:
      version_type:
        description: 'Type de version'
        required: true
        default: 'patch'
        type: choice
        options:
          - patch
          - minor
          - major
      prerelease:
        description: 'Pre-release?'
        required: false
        default: false
        type: boolean
      custom_version:
        description: 'Version personnalisée (optionnel)'
        required: false
        type: string

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: Calculate version
        id: version
        run: |
          if [ -n "${{ github.event.inputs.custom_version }}" ]; then
            NEW_VERSION="${{ github.event.inputs.custom_version }}"
          else
            # Calcul automatique basé sur le type
            LAST_TAG=$(git tag | grep '^v' | sort -V | tail -1)
            if [ -z "$LAST_TAG" ]; then
              NEW_VERSION="v1.0.0"
            else
              LAST_VERSION=${LAST_TAG#v}
              IFS='.' read -r MAJOR MINOR PATCH <<< "$LAST_VERSION"

              case "${{ github.event.inputs.version_type }}" in
                "major")
                  MAJOR=$((MAJOR + 1))
                  MINOR=0
                  PATCH=0
                  ;;
                "minor")
                  MINOR=$((MINOR + 1))
                  PATCH=0
                  ;;
                "patch")
                  PATCH=$((PATCH + 1))
                  ;;
              esac
              NEW_VERSION="v$MAJOR.$MINOR.$PATCH"
            fi
          fi

          echo "version=$NEW_VERSION" >> $GITHUB_OUTPUT
          echo "Nouvelle version: $NEW_VERSION"

      - name: Create Release
        run: |
          PRERELEASE_FLAG=""
          if [ "${{ github.event.inputs.prerelease }}" = "true" ]; then
            PRERELEASE_FLAG="--prerelease"
          fi

          gh release create ${{ steps.version.outputs.version }} \
            --title "Release ${{ steps.version.outputs.version }}" \
            --generate-notes \
            $PRERELEASE_FLAG
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Automatisation complète avec build

### Workflow multi-plateformes

```yaml
# .github/workflows/complete-release.yml
name: Complete Release Pipeline

on:
  push:
    tags: ['v*']

jobs:
  # Job 1: Tests et validations
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

  # Job 2: Build multi-plateformes
  build:
    needs: test
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        include:
          - os: ubuntu-latest
            platform: linux
            ext: ''
          - os: windows-latest
            platform: windows
            ext: '.exe'
          - os: macos-latest
            platform: darwin
            ext: ''
    runs-on: ${{ matrix.os }}

    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Build application
        run: npm run build

      - name: Package application
        run: |
          # Créer l'exécutable avec pkg ou electron-builder
          npm run package:${{ matrix.platform }}

      - name: Create archive
        run: |
          APP_NAME="myapp"
          VERSION="${{ github.ref_name }}"
          PLATFORM="${{ matrix.platform }}"

          if [ "${{ matrix.platform }}" = "windows" ]; then
            zip -r "${APP_NAME}-${VERSION}-${PLATFORM}.zip" dist/
          else
            tar -czf "${APP_NAME}-${VERSION}-${PLATFORM}.tar.gz" dist/
          fi
        shell: bash

      - name: Upload artifacts
        uses: actions/upload-artifact@v3
        with:
          name: ${{ matrix.platform }}-build
          path: |
            *.zip
            *.tar.gz

  # Job 3: Création de la release
  release:
    needs: [test, build]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Download all artifacts
        uses: actions/download-artifact@v3

      - name: Generate checksums
        run: |
          find . -name "*.zip" -o -name "*.tar.gz" | while read file; do
            sha256sum "$file" >> checksums.sha256
          done

      - name: Create Release
        run: |
          gh release create ${{ github.ref_name }} \
            --title "Release ${{ github.ref_name }}" \
            --generate-notes \
            linux-build/* \
            windows-build/* \
            macos-build/* \
            checksums.sha256
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      - name: Notify success
        run: |
          echo "🎉 Release ${{ github.ref_name }} créée avec succès!"
          echo "📦 Assets inclus:"
          gh release view ${{ github.ref_name }} --json assets --jq '.assets[].name'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Automatisation avec semantic-release

### Configuration semantic-release

**Installation :**
```bash
npm install --save-dev semantic-release @semantic-release/github
```

**Configuration :** `.releaserc.json`
```json
{
  "branches": ["main"],
  "plugins": [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    "@semantic-release/npm",
    "@semantic-release/github",
    "@semantic-release/git"
  ]
}
```

**Workflow GitHub Actions :**
```yaml
# .github/workflows/semantic-release.yml
name: Semantic Release

on:
  push:
    branches: [main]

jobs:
  release:
    runs-on: ubuntu-latest
    if: "!contains(github.event.head_commit.message, 'skip ci')"

    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
          persist-credentials: false

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Build application
        run: npm run build

      - name: Semantic Release
        run: npx semantic-release
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

### Format des commits pour semantic-release

**Conventional Commits :**
```bash
# Patch release (1.0.0 → 1.0.1)
fix: correction du bug de connexion
docs: mise à jour de la documentation

# Minor release (1.0.1 → 1.1.0)
feat: ajout de l'authentification à deux facteurs
feat(api): nouvelle endpoint de recherche

# Major release (1.1.0 → 2.0.0)
feat!: nouvelle API incompatible
feat: suppression de l'ancienne API

BREAKING CHANGE: l'ancienne API /v1 n'est plus supportée
```

## Scripts d'automatisation locaux

### Script de release intelligent

```bash
#!/bin/bash
# smart-release.sh - Release intelligente avec validations

set -e  # Arrêt sur erreur

# Configuration
REPO_URL=$(git remote get-url origin)
BRANCH=$(git rev-parse --abbrev-ref HEAD)
MAIN_BRANCH="main"

echo "🚀 Smart Release Script"
echo "======================="

# Fonction de validation
validate_environment() {
    echo "🔍 Validation de l'environnement..."

    # Vérifier qu'on est sur la bonne branche
    if [ "$BRANCH" != "$MAIN_BRANCH" ]; then
        echo "❌ Erreur: Vous devez être sur la branche $MAIN_BRANCH"
        echo "   Branche actuelle: $BRANCH"
        exit 1
    fi

    # Vérifier que le repo est propre
    if [ -n "$(git status --porcelain)" ]; then
        echo "❌ Erreur: Le repository contient des changements non commités"
        git status --short
        exit 1
    fi

    # Vérifier la connexion GitHub
    if ! gh auth status &>/dev/null; then
        echo "❌ Erreur: Non authentifié avec GitHub CLI"
        echo "   Exécutez: gh auth login"
        exit 1
    fi

    # Vérifier que le repo est à jour
    git fetch origin
    LOCAL=$(git rev-parse HEAD)
    REMOTE=$(git rev-parse origin/$MAIN_BRANCH)
    if [ "$LOCAL" != "$REMOTE" ]; then
        echo "❌ Erreur: Le repository local n'est pas à jour"
        echo "   Exécutez: git pull origin $MAIN_BRANCH"
        exit 1
    fi

    echo "✅ Environnement validé"
}

# Fonction de test
run_tests() {
    echo "🧪 Exécution des tests..."

    # Tests Node.js
    if [ -f "package.json" ]; then
        npm test
    fi

    # Tests Python
    if [ -f "requirements.txt" ] || [ -f "pyproject.toml" ]; then
        python -m pytest
    fi

    # Tests Go
    if [ -f "go.mod" ]; then
        go test ./...
    fi

    echo "✅ Tests réussis"
}

# Fonction de build
build_project() {
    echo "📦 Build du projet..."

    # Build Node.js
    if [ -f "package.json" ]; then
        npm run build 2>/dev/null || echo "Pas de script build défini"
    fi

    # Build Go
    if [ -f "go.mod" ]; then
        go build ./...
    fi

    echo "✅ Build réussi"
}

# Fonction de calcul de version
calculate_version() {
    local bump_type=$1

    # Obtenir la dernière version
    LAST_TAG=$(git tag | grep '^v' | sort -V | tail -1)

    if [ -z "$LAST_TAG" ]; then
        NEW_VERSION="v1.0.0"
    else
        LAST_VERSION=${LAST_TAG#v}
        IFS='.' read -r MAJOR MINOR PATCH <<< "$LAST_VERSION"

        case $bump_type in
            "major")
                MAJOR=$((MAJOR + 1))
                MINOR=0
                PATCH=0
                ;;
            "minor")
                MINOR=$((MINOR + 1))
                PATCH=0
                ;;
            "patch")
                PATCH=$((PATCH + 1))
                ;;
            *)
                echo "❌ Type de bump invalide: $bump_type"
                echo "   Utilisez: major, minor, ou patch"
                exit 1
                ;;
        esac
        NEW_VERSION="v$MAJOR.$MINOR.$PATCH"
    fi

    echo "$NEW_VERSION"
}

# Fonction principale
main() {
    # Demander le type de release
    echo "📋 Dernières versions:"
    git tag | grep '^v' | sort -V | tail -5
    echo ""

    echo "🏷️  Quel type de release?"
    echo "1) patch (correctifs)"
    echo "2) minor (nouvelles fonctionnalités)"
    echo "3) major (changements incompatibles)"
    read -p "Votre choix (1-3): " choice

    case $choice in
        1) BUMP_TYPE="patch" ;;
        2) BUMP_TYPE="minor" ;;
        3) BUMP_TYPE="major" ;;
        *) echo "❌ Choix invalide"; exit 1 ;;
    esac

    NEW_VERSION=$(calculate_version $BUMP_TYPE)
    echo "🎯 Nouvelle version: $NEW_VERSION"

    # Confirmation
    read -p "🤔 Continuer avec la release $NEW_VERSION? (y/N): " confirm
    if [ "$confirm" != "y" ] && [ "$confirm" != "Y" ]; then
        echo "❌ Release annulée"
        exit 0
    fi

    # Exécution du pipeline
    validate_environment
    run_tests
    build_project

    # Créer le tag
    echo "🏷️  Création du tag $NEW_VERSION..."
    git tag -a $NEW_VERSION -m "Release $NEW_VERSION"
    git push origin $NEW_VERSION

    # Créer la release GitHub
    echo "📦 Création de la release GitHub..."
    gh release create $NEW_VERSION \
        --title "Release $NEW_VERSION" \
        --generate-notes

    echo ""
    echo "🎉 Release $NEW_VERSION créée avec succès!"
    echo "🔗 Voir sur GitHub: $(gh release view $NEW_VERSION --json url --jq '.url')"
}

# Vérifier les arguments
if [ "$1" = "--help" ] || [ "$1" = "-h" ]; then
    echo "Usage: $0"
    echo "Script de release intelligent avec validations automatiques"
    exit 0
fi

# Exécuter
main
```

## Intégration avec des services externes

### Notification Slack

```yaml
# Ajout aux workflows pour notifier Slack
- name: Notify Slack
  if: always()  # Même en cas d'échec
  uses: 8398a7/action-slack@v3
  with:
    status: ${{ job.status }}
    channel: '#releases'
    text: |
      Release ${{ github.ref_name }} : ${{ job.status }}
      Repository: ${{ github.repository }}
      Actor: ${{ github.actor }}
  env:
    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

### Publication NPM automatique

```yaml
- name: Publish to NPM
  if: startsWith(github.ref, 'refs/tags/v')
  run: |
    npm config set //registry.npmjs.org/:_authToken $NPM_TOKEN
    npm publish
  env:
    NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

### Déploiement automatique

```yaml
- name: Deploy to Production
  if: startsWith(github.ref, 'refs/tags/v') && !contains(github.ref, 'beta')
  run: |
    # Script de déploiement
    ./scripts/deploy.sh production ${{ github.ref_name }}
  env:
    DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}
```

## Monitoring et rollback

### Surveillance des releases

```bash
#!/bin/bash
# monitor-release.sh - Surveillance post-release

VERSION=$1
if [ -z "$VERSION" ]; then
    echo "Usage: $0 v1.2.3"
    exit 1
fi

echo "📊 Monitoring de la release $VERSION"

# Vérifier les téléchargements
echo "📥 Statistiques de téléchargement:"
gh release view $VERSION --json assets --jq '.assets[] | "\(.name): \(.download_count) téléchargements"'

# Vérifier les issues ouvertes depuis la release
RELEASE_DATE=$(gh release view $VERSION --json publishedAt --jq '.publishedAt')
echo ""
echo "🐛 Issues ouvertes depuis la release:"
gh issue list --state open --search "created:>$RELEASE_DATE" --limit 10

# Vérifier les discussions
echo ""
echo "💬 Discussions récentes:"
gh api repos/:owner/:repo/discussions --jq '.[] | select(.created_at > "'$RELEASE_DATE'") | "- \(.title)"' | head -5
```

### Rollback automatique

```bash
#!/bin/bash
# rollback-release.sh - Rollback d'une release

VERSION=$1
REASON=${2:-"Problème critique détecté"}

if [ -z "$VERSION" ]; then
    echo "Usage: $0 v1.2.3 [raison]"
    exit 1
fi

echo "🚨 Rollback de la release $VERSION"
echo "Raison: $REASON"

# Marquer comme pre-release pour la cacher
gh release edit $VERSION --prerelease

# Créer une issue de tracking
gh issue create \
    --title "🚨 Rollback de la release $VERSION" \
    --body "La release $VERSION a été mise en rollback.

**Raison:** $REASON

**Actions effectuées:**
- [x] Release marquée comme pre-release
- [ ] Investigation en cours
- [ ] Correctif en développement

**Prochaines étapes:**
1. Identifier la cause racine
2. Développer un correctif
3. Créer une nouvelle release" \
    --label "bug,critical,rollback"

echo "✅ Rollback effectué pour $VERSION"
echo "📋 Issue de tracking créée"
```

## Points clés à retenir

✅ **GitHub Actions** permettent une automatisation complète des releases

✅ **Différentes stratégies** (tag, merge, manuel) pour différents besoins

✅ **Validation automatique** (tests, linting) avant release

✅ **Build multi-plateformes** automatisé avec artifacts

✅ **Semantic-release** pour releases entièrement automatisées

✅ **Scripts locaux** pour les validations et releases manuelles

✅ **Monitoring** et rollback pour gérer les problèmes post-release

## Conclusion de la section Gestion des Releases

Félicitations ! Vous maîtrisez maintenant tous les aspects de la gestion des releases avec GitHub :

- 🎯 **Création** de releases complètes avec la CLI
- 📦 **Gestion des assets** et distribution de binaires
- 🏷️ **Versioning** professionnel avec tags et SemVer
- 🤖 **Automatisation** complète des pipelines de release

Ces compétences vous permettront de mettre en place un processus de release moderne, fiable et scalable pour tous vos projets.


⏭️
