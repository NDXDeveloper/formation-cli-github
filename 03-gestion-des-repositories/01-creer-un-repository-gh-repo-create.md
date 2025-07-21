🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 3.1 - Créer un repository (`gh repo create`)

## 🎯 Introduction

Créer un repository est souvent la première étape de tout projet de développement. Avec `gh repo create`, vous pouvez créer un repository GitHub complet en quelques secondes, directement depuis votre terminal, sans jamais ouvrir un navigateur !

**Analogie** : Si créer un repository sur GitHub.com, c'est comme construire une maison en passant par un architecte, `gh repo create` c'est comme avoir une baguette magique qui fait apparaître la maison parfaite instantanément !

## 🏗️ Qu'est-ce que `gh repo create` ?

### Définition simple

`gh repo create` est la commande qui **crée un nouveau repository GitHub** et peut optionnellement le configurer, l'initialiser et même le cloner sur votre machine locale en une seule opération.

### Ce qu'elle fait concrètement

Quand vous utilisez `gh repo create`, voici ce qui se passe :

1. **Création** du repository sur GitHub.com
2. **Configuration** des paramètres (visibilité, description, etc.)
3. **Initialisation** optionnelle (README, .gitignore, licence)
4. **Clonage** optionnel sur votre machine
5. **Configuration** automatique des remotes Git

**Tout cela en une seule commande !**

## 🚀 Syntaxe de base

### Forme la plus simple

```bash
gh repo create nom-du-repository
```

**Cette commande** :
- Crée un repository public nommé "nom-du-repository"
- Ne le clone pas localement
- Vous demande interactivement les options

### Forme complète

```bash
gh repo create [nom] [options]
```

**Le nom** peut être :
- `mon-projet` → Crée `votre-username/mon-projet`
- `organisation/projet` → Crée `organisation/projet` (si vous avez les droits)

## 🎨 Options principales

### Visibilité du repository

#### Repository public
```bash
gh repo create mon-projet --public
```

**Avantages public** :
- ✅ Visible par tous
- ✅ Indexé par les moteurs de recherche
- ✅ Peut recevoir des contributions externes
- ✅ Gratuit sans limitation

#### Repository privé
```bash
gh repo create mon-projet --private
```

**Avantages privé** :
- ✅ Visible seulement par vous et vos collaborateurs
- ✅ Idéal pour les projets commerciaux
- ✅ Code source protégé
- ✅ Contrôle total de l'accès

### Clonage automatique

```bash
# Créer et cloner immédiatement
gh repo create mon-projet --public --clone

# Équivaut à :
gh repo create mon-projet --public
gh repo clone mon-projet
```

**Très pratique** car après la commande, vous êtes déjà dans le dossier du projet !

### Description du projet

```bash
gh repo create mon-projet --description "Un projet génial qui fait des choses incroyables"
```

**Bonne pratique** : Toujours ajouter une description claire !

### Page d'accueil (Homepage)

```bash
gh repo create mon-projet --homepage "https://mon-site.com"
```

Utile pour lier votre repository à :
- Site web du projet
- Documentation en ligne
- Démo live

## 🎯 Initialisation automatique

### README automatique

```bash
gh repo create mon-projet --public --add-readme
```

**Crée automatiquement** un fichier README.md avec :
- Titre du projet
- Description de base
- Structure initiale

### .gitignore adapté

```bash
gh repo create mon-projet --gitignore Node
```

**Types de .gitignore disponibles** :
- `Node` → Pour projets JavaScript/Node.js
- `Python` → Pour projets Python
- `Java` → Pour projets Java
- `VisualStudio` → Pour projets .NET
- `macOS` → Pour exclure les fichiers macOS
- `Windows` → Pour exclure les fichiers Windows

**Liste complète** :
```bash
# Voir tous les templates disponibles
gh api gitignore/templates
```

### Licence automatique

```bash
gh repo create mon-projet --license MIT
```

**Licences populaires** :
- `MIT` → Très permissive, populaire pour l'open source
- `Apache-2.0` → Permissive avec protection patents
- `GPL-3.0` → Copyleft, redistribution sous même licence
- `BSD-3-Clause` → Permissive, utilisée par beaucoup d'universités
- `Unlicense` → Domaine public

## 🎨 Exemples concrets par cas d'usage

### Projet personnel simple

**Besoin** : Créer rapidement un projet pour tester une idée.

```bash
gh repo create test-idee --public --clone --add-readme
cd test-idee
echo "console.log('Hello World!');" > index.js
git add .
git commit -m "Premier test"
git push
```

**Résultat** : Projet créé, cloné et prêt en 30 secondes !

### Projet JavaScript/Node.js

**Besoin** : Nouveau projet web avec configuration standard.

```bash
gh repo create mon-app-web \
  --public \
  --clone \
  --description "Application web moderne" \
  --gitignore Node \
  --license MIT \
  --add-readme
```

**Ce qui est créé** :
- ✅ Repository public
- ✅ README.md initial
- ✅ .gitignore pour Node.js
- ✅ Licence MIT
- ✅ Cloné localement

### Projet privé d'entreprise

**Besoin** : Projet confidentiel pour une entreprise.

```bash
gh repo create entreprise/projet-secret \
  --private \
  --description "Projet confidentiel - NDA requis" \
  --clone \
  --gitignore VisualStudio \
  --homepage "https://intranet.entreprise.com/projets/secret"
```

### Contribution open source

**Besoin** : Créer un outil open source pour la communauté.

```bash
gh repo create mon-outil-utile \
  --public \
  --description "Outil utile pour automatiser les tâches répétitives" \
  --license Apache-2.0 \
  --gitignore Python \
  --add-readme \
  --clone \
  --homepage "https://mon-outil-utile.readthedocs.io"
```

## 🔧 Options avancées

### Templates de repository

**Utiliser un template existant** :

```bash
gh repo create mon-nouveau-projet --template mon-username/mon-template
```

**Avantages des templates** :
- ✅ Structure de projet pré-configurée
- ✅ Fichiers de configuration standards
- ✅ Workflows GitHub Actions inclus
- ✅ Documentation de base

**Templates populaires** :
- `github/gitignore` → Templates .gitignore
- `github/choosealicense.com` → Aide au choix de licence
- Templates de frameworks (React, Vue, etc.)

### Création dans une organisation

```bash
# Si vous êtes membre de l'organisation
gh repo create mon-organisation/nouveau-projet --public

# Vérifier vos organisations
gh api user/orgs --jq '.[].login'
```

### Repository avec équipe

```bash
gh repo create projet-equipe \
  --private \
  --description "Projet collaboratif équipe" \
  --clone \
  --add-readme

# Ajouter des collaborateurs après création
cd projet-equipe
gh repo edit --add-collaborator alice
gh repo edit --add-collaborateur bob
```

## 🔄 Workflow complet de création

### Workflow recommandé pour débutants

```bash
# 1. Créer le repository avec les bases
gh repo create mon-premier-projet \
  --public \
  --description "Mon premier projet avec GitHub CLI" \
  --add-readme \
  --gitignore Node \
  --license MIT \
  --clone

# 2. Aller dans le dossier
cd mon-premier-projet

# 3. Vérifier que tout est en place
ls -la
# Vous devriez voir : README.md, .gitignore, LICENSE

# 4. Ajouter votre code
echo "console.log('Hello GitHub CLI!');" > index.js

# 5. Premier commit
git add .
git commit -m "Initial commit with basic setup"
git push

# 6. Vérifier le résultat
gh repo view --web
```

### Workflow avancé pour projets complexes

```bash
# 1. Créer depuis un template
gh repo create mon-projet-avance \
  --template facebook/create-react-app \
  --private \
  --description "Application React avancée" \
  --clone

# 2. Configuration supplémentaire
cd mon-projet-avance
gh repo edit --homepage "https://mon-app.netlify.app"
gh repo edit --add-topic react
gh repo edit --add-topic webapp

# 3. Ajouter des collaborateurs
gh repo edit --add-collaborator teammate1
gh repo edit --add-collaborator teammate2

# 4. Configurer la protection de branche
gh api repos/:owner/:repo/branches/main/protection \
  --method PUT \
  --field required_status_checks='{"strict":true,"contexts":[]}' \
  --field enforce_admins=true
```

## 🎯 Bonnes pratiques

### Nommage des repositories

#### ✅ Bon nommage
```bash
# Descriptif et clair
gh repo create calculatrice-scientifique
gh repo create api-gestion-utilisateurs
gh repo create blog-personnel
gh repo create dotfiles
```

#### ❌ Mauvais nommage
```bash
# Trop vague
gh repo create truc
gh repo create test123
gh repo create nouveau-projet

# Avec espaces (transformés en tirets)
gh repo create "mon projet"  # Devient mon-projet
```

### Description efficace

#### ✅ Bonnes descriptions
```bash
--description "Calculatrice scientifique en React avec support des fonctions avancées"
--description "API REST pour la gestion d'utilisateurs avec authentification JWT"
--description "Blog personnel développé avec Gatsby et déployé sur Netlify"
```

#### ❌ Descriptions inutiles
```bash
--description "Mon projet"
--description "Code source"
--description "Repository"
```

### Choix de la visibilité

#### Choisir public quand :
- ✅ Projet open source
- ✅ Portfolio personnel
- ✅ Outil pour la communauté
- ✅ Apprentissage et partage

#### Choisir privé quand :
- ✅ Projet commercial
- ✅ Code confidentiel
- ✅ Travail en cours non prêt
- ✅ Données sensibles

### Licence appropriée

#### MIT : Pour la plupart des projets open source
```bash
--license MIT
```

#### Apache-2.0 : Pour les projets avec considérations de brevets
```bash
--license Apache-2.0
```

#### Pas de licence : Pour les projets privés
```bash
# Pas d'option --license
```

## 🔧 Gestion des erreurs

### Erreurs courantes et solutions

#### Nom de repository déjà existant

**Erreur** :
```
Error: name already exists on this account
```

**Solutions** :
```bash
# Vérifier vos repositories existants
gh repo list

# Choisir un nom différent
gh repo create mon-projet-v2

# Ou utiliser un préfixe
gh repo create 2024-mon-projet
```

#### Permissions insuffisantes pour une organisation

**Erreur** :
```
Error: you don't have permission to create repositories in this organization
```

**Solutions** :
```bash
# Vérifier vos organisations et permissions
gh api user/orgs

# Créer dans votre compte personnel
gh repo create mon-projet  # Au lieu de org/mon-projet

# Demander les permissions à l'admin de l'org
```

#### Token sans les bonnes permissions

**Erreur** :
```
Error: token needs the 'repo' scope
```

**Solution** :
```bash
# Renouveler l'authentification avec les bonnes permissions
gh auth refresh --scopes "repo,workflow,user"
```

## 🎨 Personnalisation et automatisation

### Scripts de création personnalisés

#### Script pour projets JavaScript

```bash
#!/bin/bash
# create-js-project.sh

PROJECT_NAME=$1
DESCRIPTION=$2

if [ -z "$PROJECT_NAME" ]; then
    echo "Usage: $0 <nom-projet> [description]"
    exit 1
fi

echo "🚀 Création du projet JavaScript: $PROJECT_NAME"

gh repo create "$PROJECT_NAME" \
  --public \
  --description "$DESCRIPTION" \
  --gitignore Node \
  --license MIT \
  --add-readme \
  --clone

cd "$PROJECT_NAME"

# Créer structure standard
mkdir -p src tests docs
echo "console.log('Hello $PROJECT_NAME!');" > src/index.js

# Package.json basique
cat > package.json << EOF
{
  "name": "$PROJECT_NAME",
  "version": "1.0.0",
  "description": "$DESCRIPTION",
  "main": "src/index.js",
  "scripts": {
    "start": "node src/index.js",
    "test": "echo \"No tests yet\""
  },
  "license": "MIT"
}
EOF

# Premier commit
git add .
git commit -m "Initial project structure"
git push

echo "✅ Projet $PROJECT_NAME créé et configuré !"
gh repo view --web
```

#### Script pour projets Python

```bash
#!/bin/bash
# create-python-project.sh

PROJECT_NAME=$1
DESCRIPTION=$2

gh repo create "$PROJECT_NAME" \
  --public \
  --description "$DESCRIPTION" \
  --gitignore Python \
  --license MIT \
  --add-readme \
  --clone

cd "$PROJECT_NAME"

# Structure Python standard
mkdir -p src tests docs
echo "#!/usr/bin/env python3" > src/main.py
echo "print('Hello $PROJECT_NAME!')" >> src/main.py

# Requirements.txt
touch requirements.txt

# Setup.py basique
cat > setup.py << EOF
from setuptools import setup, find_packages

setup(
    name="$PROJECT_NAME",
    version="0.1.0",
    description="$DESCRIPTION",
    packages=find_packages(),
    python_requires='>=3.6',
)
EOF

git add .
git commit -m "Initial Python project structure"
git push

echo "✅ Projet Python $PROJECT_NAME prêt !"
```

### Templates personnalisés

#### Créer votre propre template

```bash
# 1. Créer un repository template
gh repo create mon-template \
  --public \
  --description "Template pour mes projets" \
  --add-readme \
  --clone

cd mon-template

# 2. Ajouter votre structure standard
mkdir -p src tests docs .github/workflows
echo "# Mon Template" > README.md
echo "Template pour démarrer rapidement mes projets" >> README.md

# 3. Configurer comme template sur GitHub
gh repo edit --enable-template

# 4. Utiliser votre template
gh repo create nouveau-projet --template mon-username/mon-template
```

## 🔍 Vérification et validation

### Vérifier la création

```bash
# Vérifier que le repository existe
gh repo view mon-projet

# Vérifier les paramètres
gh repo view mon-projet --json name,description,isPrivate,url

# Ouvrir dans le navigateur
gh repo view mon-projet --web

# Vérifier localement (si cloné)
cd mon-projet
git remote -v
ls -la
```

### Validation du contenu

```bash
# Vérifier les fichiers créés
ls -la

# Vérifier le contenu du README
cat README.md

# Vérifier la licence
cat LICENSE

# Vérifier le .gitignore
cat .gitignore

# Vérifier l'historique Git
git log --oneline
```

## 📋 Récapitulatif des commandes essentielles

### Création de base

```bash
# Création simple
gh repo create mon-projet

# Création complète
gh repo create mon-projet --public --clone --add-readme

# Avec configuration
gh repo create mon-projet \
  --public \
  --description "Description du projet" \
  --gitignore Node \
  --license MIT \
  --clone
```

### Options principales

| Option | Description | Exemple |
|--------|-------------|---------|
| `--public` | Repository public | `gh repo create test --public` |
| `--private` | Repository privé | `gh repo create test --private` |
| `--clone` | Clone après création | `gh repo create test --clone` |
| `--description` | Description du projet | `--description "Mon projet"` |
| `--homepage` | Page d'accueil | `--homepage "https://monsite.com"` |
| `--gitignore` | Template .gitignore | `--gitignore Node` |
| `--license` | Licence du projet | `--license MIT` |
| `--add-readme` | Créer README.md | `--add-readme` |
| `--template` | Utiliser un template | `--template owner/template` |

### Vérification

```bash
# Voir le repository
gh repo view

# Voir dans le navigateur
gh repo view --web

# Lister vos repositories
gh repo list
```

## 🎯 Prochaine étape

Félicitations ! Vous savez maintenant créer des repositories GitHub comme un pro. Il est temps d'apprendre à récupérer et contribuer aux projets existants avec le clonage et le fork.

👉 **Continuez avec** : [Cloner et forker (`gh repo clone`, `gh repo fork`)](02-cloner-et-forker-gh-repo-clone-fork.md)

---

*Vous avez terminé la section 3.1 ! Vous maîtrisez maintenant la création de repositories avec toutes les options. Dans la prochaine section, nous explorerons le clonage et le fork pour travailler avec des projets existants.*

⏭️
