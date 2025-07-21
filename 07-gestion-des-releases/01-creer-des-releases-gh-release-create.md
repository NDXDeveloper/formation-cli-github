🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 7.1 Créer des releases

## Introduction

La création d'une release est l'étape qui transforme votre code en un produit distributable. Avec la commande `gh release create`, vous pouvez créer des releases complètes en quelques secondes, incluant tags, notes de version, et assets.

Dans cette section, nous allons explorer toutes les possibilités de cette commande puissante, des cas d'usage les plus simples aux configurations les plus avancées.

## Syntaxe de base

### Commande minimale

La forme la plus simple de création d'une release :

```bash
gh release create v1.0.0
```

Cette commande :
- ✅ Crée le tag `v1.0.0` s'il n'existe pas
- ✅ Crée une release publique
- ✅ Génère des notes automatiques basées sur les commits
- ✅ Utilise le tag comme titre de la release

### Anatomie de la commande complète

```bash
gh release create [TAG] [FILES...] [OPTIONS]
```

- **TAG** : Nom du tag/version (obligatoire)
- **FILES** : Fichiers à attacher comme assets (optionnel)
- **OPTIONS** : Paramètres de configuration (optionnel)

## Création d'une release simple

### Release avec titre personnalisé

```bash
gh release create v1.0.0 --title "Première version stable"
```

**Résultat :**
- Tag : `v1.0.0`
- Titre : "Première version stable"
- Notes : Générées automatiquement

### Release avec notes personnalisées

```bash
gh release create v1.0.0 \
  --title "Version 1.0.0 - Lancement officiel" \
  --notes "🎉 Première version stable de notre application !

## Fonctionnalités principales
- Interface utilisateur intuitive
- Système d'authentification sécurisé
- API REST complète

## Installation
Téléchargez le fichier correspondant à votre système d'exploitation."
```

**Conseil pour débutants :** Utilisez des guillemets doubles pour encadrer des notes multilignes, et échappez les retours à la ligne avec `\`.

## Gestion des notes de version

### Notes depuis un fichier

Plutôt que d'écrire les notes directement dans la commande, utilisez un fichier :

```bash
# Créer le fichier de notes
cat > release-notes.md << 'EOF'
# Version 1.0.0 - Lancement officiel

## 🎉 Nouveautés
- Interface utilisateur repensée
- Performance améliorée de 50%
- Support multi-langues

## 🐛 Corrections
- Correction du bug de connexion
- Résolution des problèmes de mémoire

## 📚 Documentation
- Guide d'installation mis à jour
- Exemples d'API ajoutés
EOF

# Créer la release avec ces notes
gh release create v1.0.0 \
  --title "Version 1.0.0" \
  --notes-file release-notes.md
```

### Génération automatique des notes

GitHub peut générer automatiquement des notes basées sur les commits et pull requests :

```bash
gh release create v1.0.0 \
  --title "Version 1.0.0" \
  --generate-notes
```

**Ce que génère `--generate-notes` :**
- Liste des pull requests mergées
- Liste des nouveaux contributeurs
- Liens vers les commits importants
- Catégorisation automatique des changements

**Exemple de notes générées :**
```markdown
## What's Changed
* Add user authentication by @john-doe in #123
* Improve performance by @jane-smith in #124
* Fix memory leak by @dev-team in #125

## New Contributors
* @john-doe made their first contribution in #123

**Full Changelog**: https://github.com/user/repo/compare/v0.9.0...v1.0.0
```

## Types de releases

### Release stable (par défaut)

```bash
gh release create v1.0.0 --title "Version stable"
```

Une release stable est visible par tous et recommandée pour les utilisateurs finaux.

### Pre-release (version de test)

```bash
gh release create v1.1.0-beta.1 \
  --title "Version 1.1.0 Beta 1" \
  --prerelease \
  --notes "⚠️ Version de test - Ne pas utiliser en production"
```

**Caractéristiques d'une pre-release :**
- Marquée comme "Pre-release" sur GitHub
- Non incluse dans la "Latest release"
- Idéale pour les versions alpha, beta, RC

### Draft (brouillon)

```bash
gh release create v1.0.0 \
  --title "Version 1.0.0" \
  --draft \
  --notes "Brouillon - En cours de préparation"
```

**Utilité des drafts :**
- Visible seulement par les collaborateurs
- Permet de préparer la release avant publication
- Possibilité de modifier avant de rendre publique

## Attachement d'assets

### Attacher des fichiers lors de la création

```bash
gh release create v1.0.0 \
  --title "Version 1.0.0" \
  app.exe app-linux app-macos.dmg
```

Cette commande attache trois fichiers binaires à la release.

### Types d'assets courants

**Applications compilées :**
```bash
gh release create v1.0.0 \
  dist/myapp-windows.exe \
  dist/myapp-linux \
  dist/myapp-macos.dmg
```

**Archives compressées :**
```bash
gh release create v1.0.0 \
  dist/source-code.zip \
  dist/documentation.tar.gz
```

**Documentation :**
```bash
gh release create v1.0.0 \
  docs/user-guide.pdf \
  docs/api-reference.pdf
```

### Utilisation de wildcards

```bash
# Attacher tous les fichiers .zip du dossier dist
gh release create v1.0.0 dist/*.zip

# Attacher tous les binaires
gh release create v1.0.0 build/bin/*
```

## Options avancées

### Spécifier une branche ou commit

Par défaut, la release est créée sur la branche courante. Pour spécifier un autre point :

```bash
# Créer depuis une branche spécifique
gh release create v1.0.0 --target main

# Créer depuis un commit spécifique
gh release create v1.0.0 --target a1b2c3d4
```

### Release avec discussion

Activer les discussions pour recueillir des commentaires :

```bash
gh release create v1.0.0 \
  --title "Version 1.0.0" \
  --discussion-category "Releases"
```

### Release avec checksum automatique

Pour les releases nécessitant une vérification d'intégrité :

```bash
# Générer les checksums
sha256sum dist/* > checksums.txt

# Inclure dans la release
gh release create v1.0.0 \
  dist/* \
  checksums.txt
```

## Gestion des erreurs courantes

### Tag déjà existant

**Erreur :**
```
tag already exists
```

**Solutions :**
```bash
# Supprimer le tag local et distant
git tag -d v1.0.0
git push origin --delete v1.0.0

# Ou utiliser un nouveau numéro de version
gh release create v1.0.1
```

### Fichier non trouvé

**Erreur :**
```
file not found: app.exe
```

**Solutions :**
```bash
# Vérifier l'existence du fichier
ls -la app.exe

# Utiliser le chemin complet
gh release create v1.0.0 /path/to/app.exe

# Vérifier le répertoire de travail
pwd
```

### Permissions insuffisantes

**Erreur :**
```
Resource not accessible by integration
```

**Solutions :**
- Vérifier vos permissions sur le repository
- S'assurer d'être authentifié : `gh auth status`
- Demander les droits de création de releases

## Workflows pratiques

### Release rapide pour un hotfix

```bash
#!/bin/bash
# quick-release.sh - Script pour release rapide

VERSION=$1
if [ -z "$VERSION" ]; then
    echo "Usage: ./quick-release.sh v1.0.1"
    exit 1
fi

echo "🚀 Création de la release $VERSION"

# Build du projet
echo "📦 Build en cours..."
npm run build

# Création de la release
echo "🎉 Création de la release..."
gh release create $VERSION \
  --title "Hotfix $VERSION" \
  --generate-notes \
  dist/*

echo "✅ Release $VERSION créée avec succès !"
```

### Release avec validation

```bash
#!/bin/bash
# validated-release.sh - Release avec validations

VERSION=$1

# Validations préalables
echo "🔍 Validations..."

# Vérifier que nous sommes sur main
BRANCH=$(git rev-parse --abbrev-ref HEAD)
if [ "$BRANCH" != "main" ]; then
    echo "❌ Erreur: Vous devez être sur la branche main"
    exit 1
fi

# Vérifier que tout est commité
if [ -n "$(git status --porcelain)" ]; then
    echo "❌ Erreur: Il y a des changements non commités"
    exit 1
fi

# Vérifier que les tests passent
echo "🧪 Exécution des tests..."
npm test
if [ $? -ne 0 ]; then
    echo "❌ Erreur: Les tests échouent"
    exit 1
fi

# Build et release
echo "📦 Build..."
npm run build

echo "🎉 Création de la release $VERSION..."
gh release create $VERSION \
  --title "Release $VERSION" \
  --generate-notes \
  dist/*

echo "✅ Release créée avec succès !"
```

## Personnalisation avancée

### Template de notes personnalisé

Créez un template réutilisable pour vos notes :

```bash
# templates/release-notes.md
# Version {{VERSION}}

## 🎯 Résumé
<!-- Description brève des changements principaux -->

## ✨ Nouvelles fonctionnalités
<!-- Liste des nouvelles features -->

## 🐛 Corrections de bugs
<!-- Liste des bugs corrigés -->

## 🔧 Améliorations techniques
<!-- Changements techniques, refactoring, etc. -->

## 📚 Documentation
<!-- Mises à jour de documentation -->

## ⚠️ Breaking Changes
<!-- Changements incompatibles (si applicable) -->

## 🙏 Remerciements
<!-- Contributeurs, testeurs, etc. -->
```

Utilisation :
```bash
# Copier et personnaliser le template
cp templates/release-notes.md release-v1.0.0.md
# Éditer le fichier...
gh release create v1.0.0 --notes-file release-v1.0.0.md
```

### Integration avec les milestones

Si vous utilisez les milestones GitHub pour organiser vos releases :

```bash
# Créer la release en référençant le milestone
gh release create v1.0.0 \
  --title "Version 1.0.0 - Milestone Q1 2024" \
  --notes "Toutes les fonctionnalités du milestone Q1 2024 sont incluses.

Voir les détails : https://github.com/user/repo/milestone/1"
```

## Vérification après création

### Confirmer la création

Après création, vérifiez que tout s'est bien passé :

```bash
# Lister les releases récentes
gh release list --limit 5

# Voir les détails de la release créée
gh release view v1.0.0
```

### Tester les téléchargements

```bash
# Télécharger un asset pour vérifier
gh release download v1.0.0 --pattern "*.exe"

# Vérifier la taille et l'intégrité
ls -lh *.exe
```

## Points clés à retenir

✅ **`gh release create TAG`** est la commande de base pour créer une release

✅ **`--title`** et **`--notes`** personnalisent la présentation

✅ **`--notes-file`** utilise un fichier externe pour les notes

✅ **`--generate-notes`** crée automatiquement des notes basées sur les commits

✅ **`--prerelease`** et **`--draft`** contrôlent la visibilité

✅ **Les assets** sont attachés en listant les fichiers après le tag

✅ **La validation préalable** évite les erreurs et améliore la qualité

## Prochaine étape

Maintenant que vous savez créer des releases de base, nous allons apprendre à gérer efficacement les assets : upload, organisation, et bonnes pratiques de distribution.


⏭️
