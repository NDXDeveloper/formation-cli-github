🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 7.2 Upload d'assets et de binaires

## Introduction

Les assets sont des fichiers additionnels attachés à vos releases : exécutables, archives, documentation, ou tout autre fichier que vos utilisateurs pourraient avoir besoin de télécharger. La gestion efficace des assets est cruciale pour offrir une expérience utilisateur optimale.

Dans cette section, nous allons apprendre à uploader, organiser et gérer les assets de vos releases avec la CLI GitHub, que ce soit lors de la création initiale ou en ajout ultérieur.

## Comprendre les assets de release

### Qu'est-ce qu'un asset ?

Un **asset** est un fichier binaire ou texte attaché à une release GitHub. Les assets sont automatiquement :

- 📦 **Stockés** sur les serveurs GitHub
- 🔗 **Accessibles** via des URLs directes de téléchargement
- 📊 **Comptabilisés** dans les statistiques de téléchargement
- 🔍 **Indexés** pour la recherche

### Types d'assets courants

**Exécutables compilés :**
```
myapp.exe          # Windows
myapp              # Linux/Unix
myapp.dmg          # macOS
myapp.msi          # Windows Installer
```

**Archives compressées :**
```
source-code.zip    # Code source
documentation.tar.gz # Documentation
assets.zip         # Ressources
```

**Packages spécialisés :**
```
myapp.deb          # Debian/Ubuntu
myapp.rpm          # Red Hat/CentOS
myapp.pkg          # macOS Package
```

**Documentation :**
```
user-guide.pdf     # Manuel utilisateur
api-docs.html      # Documentation API
CHANGELOG.md       # Notes de version détaillées
```

## Upload d'assets lors de la création

### Méthode directe

La façon la plus simple d'attacher des assets est de les spécifier lors de la création :

```bash
gh release create v1.0.0 \
  --title "Version 1.0.0" \
  dist/myapp.exe \
  dist/myapp-linux \
  dist/myapp.dmg
```

### Upload avec wildcards

Pour uploader plusieurs fichiers d'un coup :

```bash
# Tous les exécutables du dossier dist
gh release create v1.0.0 dist/*

# Tous les fichiers .zip
gh release create v1.0.0 build/*.zip

# Combinaison de patterns
gh release create v1.0.0 \
  dist/*.exe \
  dist/*.dmg \
  docs/*.pdf
```

### Organisation par dossiers

Structurez vos fichiers avant l'upload :

```bash
# Structure recommandée
releases/
├── v1.0.0/
│   ├── windows/
│   │   ├── myapp.exe
│   │   └── myapp-installer.msi
│   ├── linux/
│   │   ├── myapp-amd64.deb
│   │   └── myapp-x86_64.rpm
│   ├── macos/
│   │   └── myapp.dmg
│   └── documentation/
│       ├── user-guide.pdf
│       └── api-reference.pdf

# Upload organisé
gh release create v1.0.0 \
  releases/v1.0.0/windows/* \
  releases/v1.0.0/linux/* \
  releases/v1.0.0/macos/* \
  releases/v1.0.0/documentation/*
```

## Ajout d'assets à une release existante

### Commande `gh release upload`

Pour ajouter des assets à une release déjà créée :

```bash
gh release upload v1.0.0 new-file.zip
```

### Upload multiple

```bash
gh release upload v1.0.0 \
  additional-file1.exe \
  additional-file2.dmg \
  documentation-update.pdf
```

### Remplacement d'assets existants

Par défaut, l'upload échoue si un fichier avec le même nom existe déjà. Pour forcer le remplacement :

```bash
gh release upload v1.0.0 myapp.exe --clobber
```

**⚠️ Attention :** `--clobber` écrase définitivement l'ancien fichier.

## Gestion des noms de fichiers

### Nommage cohérent

Adoptez une convention de nommage claire :

```bash
# Format recommandé : nom-version-os-architecture.extension
myapp-1.0.0-windows-amd64.exe
myapp-1.0.0-linux-amd64
myapp-1.0.0-darwin-arm64.dmg
myapp-1.0.0-documentation.pdf
```

### Renommage lors de l'upload

GitHub utilise le nom de fichier local par défaut, mais vous pouvez personnaliser :

```bash
# Le fichier build/app sera uploadé comme myapp-1.0.0-linux
# (Cette fonctionnalité nécessite parfois de renommer le fichier avant upload)

# Méthode recommandée : renommer avant upload
cp build/app myapp-1.0.0-linux
gh release upload v1.0.0 myapp-1.0.0-linux
```

## Scripts d'automatisation pour assets

### Script de build et upload

```bash
#!/bin/bash
# build-and-upload.sh - Build multi-plateformes et upload

VERSION=$1
if [ -z "$VERSION" ]; then
    echo "Usage: ./build-and-upload.sh v1.0.0"
    exit 1
fi

echo "🔨 Build pour toutes les plateformes..."

# Création du dossier de release
mkdir -p "releases/$VERSION"

# Build Windows
echo "Building for Windows..."
GOOS=windows GOARCH=amd64 go build -o "releases/$VERSION/myapp-$VERSION-windows-amd64.exe"

# Build Linux
echo "Building for Linux..."
GOOS=linux GOARCH=amd64 go build -o "releases/$VERSION/myapp-$VERSION-linux-amd64"

# Build macOS Intel
echo "Building for macOS Intel..."
GOOS=darwin GOARCH=amd64 go build -o "releases/$VERSION/myapp-$VERSION-darwin-amd64"

# Build macOS ARM (M1/M2)
echo "Building for macOS ARM..."
GOOS=darwin GOARCH=arm64 go build -o "releases/$VERSION/myapp-$VERSION-darwin-arm64"

# Création des archives
echo "📦 Création des archives..."
cd "releases/$VERSION"

# Archive Windows
zip "myapp-$VERSION-windows-amd64.zip" "myapp-$VERSION-windows-amd64.exe"

# Archive Linux
tar -czf "myapp-$VERSION-linux-amd64.tar.gz" "myapp-$VERSION-linux-amd64"

# Archive macOS
zip "myapp-$VERSION-darwin.zip" "myapp-$VERSION-darwin-"*

cd ../..

# Upload de tous les assets
echo "🚀 Upload des assets..."
gh release create $VERSION \
  --title "Release $VERSION" \
  --generate-notes \
  releases/$VERSION/*

echo "✅ Release $VERSION créée avec tous les assets !"
```

### Script pour Node.js/npm

```bash
#!/bin/bash
# npm-release.sh - Release pour projet Node.js

VERSION=$1
if [ -z "$VERSION" ]; then
    echo "Usage: ./npm-release.sh v1.0.0"
    exit 1
fi

echo "📦 Build du projet Node.js..."

# Installation des dépendances
npm ci

# Build du projet
npm run build

# Création du package
npm pack

# Génération de la documentation
npm run docs

# Création des assets
mkdir -p "release-assets"

# Copie des fichiers importants
cp *.tgz release-assets/
cp -r docs/build/* release-assets/ 2>/dev/null || true
cp README.md release-assets/
cp CHANGELOG.md release-assets/ 2>/dev/null || true

# Upload
echo "🚀 Création de la release..."
gh release create $VERSION \
  --title "Release $VERSION" \
  --notes-file CHANGELOG.md \
  release-assets/*

# Nettoyage
rm -rf release-assets

echo "✅ Release créée avec succès !"
```

## Gestion des gros fichiers

### Limites de GitHub

GitHub a des limites pour les assets :
- **Taille maximale par fichier :** 2 GB
- **Taille maximale par release :** 20 GB
- **Bande passante :** Limitée selon votre plan

### Optimisation des fichiers

**Compression :**
```bash
# Compresser les exécutables avec UPX (optionnel)
upx --best myapp.exe

# Créer des archives optimisées
tar -czf myapp.tar.gz myapp
zip -9 myapp.zip myapp.exe
```

**Splitting pour gros fichiers :**
```bash
# Diviser un gros fichier en parties
split -b 1GB large-file.bin large-file.bin.part

# Upload des parties
gh release upload v1.0.0 large-file.bin.part*

# Les utilisateurs devront reconstituer avec :
# cat large-file.bin.part* > large-file.bin
```

## Bonnes pratiques pour assets

### Convention de nommage

**Pour applications :**
```
{nom}-{version}-{os}-{architecture}.{extension}

Exemples :
myapp-1.0.0-windows-amd64.exe
myapp-1.0.0-linux-amd64
myapp-1.0.0-darwin-arm64
```

**Pour documentation :**
```
{type}-{version}.{extension}

Exemples :
user-guide-1.0.0.pdf
api-reference-1.0.0.html
installation-guide-1.0.0.md
```

### Checksums pour sécurité

Générez et incluez des checksums pour vérifier l'intégrité :

```bash
#!/bin/bash
# generate-checksums.sh

RELEASE_DIR="releases/v1.0.0"

echo "🔐 Génération des checksums..."

# Générer SHA256 pour tous les fichiers
cd "$RELEASE_DIR"
sha256sum * > checksums.sha256

# Générer MD5 (optionnel, moins sécurisé)
md5sum * > checksums.md5

echo "📋 Checksums générés :"
cat checksums.sha256

# Upload avec les checksums
cd ../..
gh release upload v1.0.0 \
  "$RELEASE_DIR"/checksums.sha256 \
  "$RELEASE_DIR"/checksums.md5
```

**Exemple de fichier checksums.sha256 :**
```
a1b2c3d4e5f6... myapp-1.0.0-windows-amd64.exe
1a2b3c4d5e6f... myapp-1.0.0-linux-amd64
9z8y7x6w5v4u... myapp-1.0.0-darwin-arm64
```

### Documentation des assets

Incluez toujours des informations sur les assets dans vos notes de release :

```markdown
## 📦 Assets disponibles

### Exécutables
- `myapp-1.0.0-windows-amd64.exe` - Windows 64-bit
- `myapp-1.0.0-linux-amd64` - Linux 64-bit
- `myapp-1.0.0-darwin-arm64` - macOS Apple Silicon (M1/M2)
- `myapp-1.0.0-darwin-amd64` - macOS Intel

### Archives
- `myapp-1.0.0-source.zip` - Code source complet
- `myapp-1.0.0-documentation.tar.gz` - Documentation complète

### Vérification d'intégrité
- `checksums.sha256` - Checksums SHA256 de tous les fichiers

### Installation rapide
```bash
# Linux/macOS
curl -L https://github.com/user/repo/releases/download/v1.0.0/myapp-1.0.0-linux-amd64 -o myapp
chmod +x myapp

# Windows PowerShell
Invoke-WebRequest -Uri "https://github.com/user/repo/releases/download/v1.0.0/myapp-1.0.0-windows-amd64.exe" -OutFile "myapp.exe"
```
```

## Automatisation avec GitHub Actions

### Workflow complet de release

```yaml
# .github/workflows/release.yml
name: Create Release

on:
  push:
    tags: ['v*']

jobs:
  build-and-release:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        goos: [linux, windows, darwin]
        goarch: [amd64, arm64]
        exclude:
          # Windows ARM64 pas encore supporté partout
          - goos: windows
            goarch: arm64

    steps:
      - uses: actions/checkout@v3

      - name: Set up Go
        uses: actions/setup-go@v3
        with:
          go-version: '1.19'

      - name: Build
        env:
          GOOS: ${{ matrix.goos }}
          GOARCH: ${{ matrix.goarch }}
        run: |
          # Créer le nom de fichier
          EXT=""
          if [ "$GOOS" = "windows" ]; then EXT=".exe"; fi
          FILENAME="myapp-${GITHUB_REF_NAME}-${GOOS}-${GOARCH}${EXT}"

          # Build
          go build -o $FILENAME

          # Compresser
          if [ "$GOOS" = "windows" ]; then
            zip "${FILENAME%.exe}.zip" $FILENAME
          else
            tar -czf "${FILENAME}.tar.gz" $FILENAME
          fi

      - name: Upload artifacts
        uses: actions/upload-artifact@v3
        with:
          name: binaries
          path: |
            *.zip
            *.tar.gz

  create-release:
    needs: build-and-release
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Download artifacts
        uses: actions/download-artifact@v3
        with:
          name: binaries

      - name: Generate checksums
        run: |
          sha256sum * > checksums.sha256

      - name: Create Release
        run: |
          gh release create ${{ github.ref_name }} \
            --title "Release ${{ github.ref_name }}" \
            --generate-notes \
            * checksums.sha256
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Gestion des erreurs communes

### Fichier trop volumineux

**Erreur :**
```
file size too large: file.bin (2.1 GB exceeds 2.0 GB)
```

**Solutions :**
1. Compresser le fichier
2. Le diviser en parties plus petites
3. Utiliser un service externe (AWS S3, etc.) et fournir le lien

### Asset déjà existant

**Erreur :**
```
asset already exists: myapp.exe
```

**Solutions :**
```bash
# Supprimer l'asset existant
gh release delete-asset v1.0.0 myapp.exe

# Puis re-uploader
gh release upload v1.0.0 myapp.exe

# Ou forcer le remplacement
gh release upload v1.0.0 myapp.exe --clobber
```

### Permissions d'upload

**Erreur :**
```
Resource not accessible by integration
```

**Solutions :**
- Vérifier les permissions sur le repository
- S'assurer d'être authentifié correctement
- Vérifier que la release existe

## Points clés à retenir

✅ **Les assets** enrichissent vos releases avec des fichiers téléchargeables

✅ **Convention de nommage** claire facilite l'utilisation par vos utilisateurs

✅ **`gh release upload`** ajoute des assets à une release existante

✅ **`--clobber`** force le remplacement d'assets existants

✅ **Checksums** garantissent l'intégrité des téléchargements

✅ **Automatisation** avec scripts et GitHub Actions améliore la qualité

✅ **Documentation** des assets dans les notes de release est essentielle

## Prochaine étape

Maintenant que vous maîtrisez l'upload et la gestion des assets, nous allons apprendre à bien gérer les tags et le versioning sémantique pour structurer vos releases de manière professionnelle.


⏭️
