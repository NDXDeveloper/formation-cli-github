🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 10.2 Variables d'environnement

## Qu'est-ce que les variables d'environnement ?

Les variables d'environnement sont comme des "réglages globaux" pour votre système et vos applications. Imaginez-les comme des post-it invisibles collés sur votre ordinateur que tous les programmes peuvent lire pour adapter leur comportement.

**Exemple simple :**
```bash
# Au lieu d'écrire votre nom à chaque fois
echo "Bonjour Jean Dupont"
echo "Bienvenue Jean Dupont"

# Vous stockez votre nom dans une variable d'environnement
export USER_NAME="Jean Dupont"
echo "Bonjour $USER_NAME"
echo "Bienvenue $USER_NAME"
```

## Pourquoi utiliser des variables d'environnement ?

### Avantages pour les scripts GitHub CLI

- **🔧 Configuration centralisée** : Modifier un seul endroit pour tout changer
- **🔒 Sécurité** : Éviter d'exposer des tokens dans le code
- **🌍 Portabilité** : Même script fonctionne sur différents environnements
- **👥 Partage d'équipe** : Configuration standard pour tous

### Problèmes résolus

**❌ Sans variables d'environnement :**
```bash
# Token exposé dans le script (DANGEREUX!)
gh auth login --token ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

# Configuration en dur
gh repo create mon-nom-utilisateur/mon-projet

# Répétition dans tous les scripts
REPO_OWNER="mon-nom-utilisateur"
```

**✅ Avec variables d'environnement :**
```bash
# Token sécurisé (invisible dans le code)
gh auth login --token "$GITHUB_TOKEN"

# Configuration flexible
gh repo create "$GITHUB_USERNAME/$PROJECT_NAME"

# Configuration centralisée et réutilisable
```

## Variables d'environnement pour GitHub CLI

### Variables essentielles

```bash
# Authentification GitHub
export GITHUB_TOKEN="your_personal_access_token"

# Informations utilisateur
export GITHUB_USERNAME="votre-nom-utilisateur"
export GITHUB_EMAIL="votre@email.com"

# Configuration de projet
export GITHUB_REPO="nom-du-repository"
export GITHUB_ORG="nom-organisation"  # Si applicable

# Éditeur préféré
export EDITOR="code"  # ou "nano", "vim", etc.

# Configuration GitHub CLI
export GH_PAGER=""  # Désactive la pagination automatique
```

### Variables spécifiques aux workflows

```bash
# Pour les équipes
export DEFAULT_REVIEWER="chef-equipe"
export TEAM_SLACK_WEBHOOK="https://hooks.slack.com/..."

# Pour les releases
export MAIN_BRANCH="main"  # ou "master"
export STAGING_BRANCH="develop"

# Pour les notifications
export NOTIFICATION_EMAIL="equipe@entreprise.com"
```

## Configuration des variables d'environnement

### Méthode 1 : Variables temporaires (session courante)

```bash
# Définir pour la session courante uniquement
export GITHUB_USERNAME="alice"
export PROJECT_NAME="mon-super-projet"

# Vérifier que c'est bien défini
echo "Utilisateur: $GITHUB_USERNAME"
echo "Projet: $PROJECT_NAME"

# Utiliser dans un script
gh repo create "$GITHUB_USERNAME/$PROJECT_NAME"
```

**💡 Note :** Ces variables disparaissent quand vous fermez le terminal.

### Méthode 2 : Variables permanentes (fichier .bashrc/.zshrc)

```bash
# Ouvrir le fichier de configuration de votre shell
# Sur Linux/macOS avec bash :
nano ~/.bashrc

# Sur macOS avec zsh :
nano ~/.zshrc

# Sur Windows Git Bash :
nano ~/.bashrc
```

**Ajouter ces lignes à la fin du fichier :**
```bash
# === Configuration GitHub CLI ===
export GITHUB_USERNAME="votre-nom-utilisateur"
export GITHUB_EMAIL="votre@email.com"
export EDITOR="code"
export GH_PAGER=""

# === Configuration projets ===
export DEFAULT_ORG="mon-organisation"
export MAIN_BRANCH="main"

# Affichage au démarrage (optionnel)
echo "🚀 Configuration GitHub CLI chargée pour $GITHUB_USERNAME"
```

**Recharger la configuration :**
```bash
# Recharger le fichier de configuration
source ~/.bashrc  # ou ~/.zshrc

# Ou redémarrer le terminal
```

### Méthode 3 : Fichier .env dédié

Créer un fichier `.env` dans votre dossier de projet :

```bash
# .env - Configuration du projet
GITHUB_USERNAME=alice
GITHUB_REPO=mon-projet-web
PROJECT_MAINTAINER=bob
SLACK_CHANNEL=#dev-team
MAIN_BRANCH=main
STAGING_BRANCH=develop
```

**Script pour charger le fichier .env :**
```bash
#!/bin/bash
# load-env.sh - Charger les variables d'environnement

if [ -f ".env" ]; then
    echo "📁 Chargement des variables depuis .env..."

    # Charger chaque ligne du fichier .env
    while IFS='=' read -r key value; do
        # Ignorer les lignes vides et les commentaires
        if [[ $key && ! $key =~ ^[[:space:]]*# ]]; then
            # Supprimer les espaces autour de la clé et valeur
            key=$(echo "$key" | xargs)
            value=$(echo "$value" | xargs)

            # Exporter la variable
            export "$key"="$value"
            echo "   $key=$value"
        fi
    done < .env

    echo "✅ Variables chargées!"
else
    echo "❌ Fichier .env non trouvé"
    exit 1
fi
```

**Utilisation :**
```bash
# Charger les variables depuis .env
source load-env.sh

# Ou dans vos scripts
#!/bin/bash
source load-env.sh  # Charger en première ligne

# Maintenant utiliser les variables
gh repo create "$GITHUB_USERNAME/$GITHUB_REPO"
```

## Gestion sécurisée des tokens

### Créer un token d'accès personnel GitHub

1. **Aller sur GitHub.com** → Settings → Developer settings → Personal access tokens → Tokens (classic)

2. **Générer un nouveau token** avec les permissions nécessaires :
   ```
   ✅ repo (accès complet aux repositories)
   ✅ workflow (si vous utilisez GitHub Actions)
   ✅ write:org (si vous gérez des organisations)
   ✅ read:user (informations utilisateur)
   ```

3. **Copier le token** (il ne sera affiché qu'une seule fois !)

### Stocker le token de manière sécurisée

**❌ JAMAIS faire ça :**
```bash
# Ne JAMAIS mettre le token directement dans le script
export GITHUB_TOKEN="ghp_1234567890abcdef"  # ❌ DANGEREUX!

# Ne JAMAIS commiter le token dans Git
git add .env  # Si .env contient des tokens ❌
```

**✅ Méthodes sécurisées :**

**Option 1 : Variable d'environnement système**
```bash
# Ajouter dans ~/.bashrc (JAMAIS dans le code!)
export GITHUB_TOKEN="your_token_here"

# Recharger
source ~/.bashrc

# Vérifier (sans afficher la valeur complète)
if [ -n "$GITHUB_TOKEN" ]; then
    echo "✅ Token GitHub configuré"
else
    echo "❌ Token GitHub manquant"
fi
```

**Option 2 : Fichier .env.local (non versionné)**
```bash
# Créer .env.local (ajouter à .gitignore!)
echo "GITHUB_TOKEN=your_token_here" > .env.local

# Ajouter à .gitignore
echo ".env.local" >> .gitignore

# Charger dans vos scripts
if [ -f ".env.local" ]; then
    source .env.local
fi
```

**Option 3 : Gestionnaire de mots de passe système**
```bash
# Sur macOS (Keychain)
security add-generic-password -a "$USER" -s "github-token" -w "your_token"

# Récupérer le token
GITHUB_TOKEN=$(security find-generic-password -a "$USER" -s "github-token" -w)
export GITHUB_TOKEN
```

## Utilisation dans les scripts

### Script avec variables d'environnement

```bash
#!/bin/bash
# create-repo.sh - Créer un repository avec configuration

# === CHARGEMENT DE LA CONFIGURATION ===

# Charger le fichier .env s'il existe
if [ -f ".env" ]; then
    source .env
fi

# Charger les secrets depuis .env.local
if [ -f ".env.local" ]; then
    source .env.local
fi

# === VARIABLES PAR DÉFAUT ===

# Utiliser les variables d'environnement ou des valeurs par défaut
REPO_NAME="${1:-$DEFAULT_REPO_NAME}"
REPO_DESCRIPTION="${2:-$DEFAULT_REPO_DESCRIPTION}"
REPO_VISIBILITY="${REPO_VISIBILITY:-public}"
MAIN_BRANCH="${MAIN_BRANCH:-main}"

# === VALIDATION ===

# Vérifier les variables obligatoires
if [ -z "$GITHUB_TOKEN" ]; then
    echo "❌ Variable GITHUB_TOKEN manquante"
    echo "   Configurez votre token d'accès personnel"
    exit 1
fi

if [ -z "$GITHUB_USERNAME" ]; then
    echo "❌ Variable GITHUB_USERNAME manquante"
    echo "   Ajoutez: export GITHUB_USERNAME='votre-nom'"
    exit 1
fi

if [ -z "$REPO_NAME" ]; then
    echo "❌ Usage: $0 <nom-repo> [description]"
    echo "   Ou définissez DEFAULT_REPO_NAME dans .env"
    exit 1
fi

# === CRÉATION DU REPOSITORY ===

echo "🚀 Création du repository: $REPO_NAME"
echo "👤 Propriétaire: $GITHUB_USERNAME"
echo "🔒 Visibilité: $REPO_VISIBILITY"

# Créer le repository
gh repo create "$GITHUB_USERNAME/$REPO_NAME" \
    --description "$REPO_DESCRIPTION" \
    --"$REPO_VISIBILITY" \
    --clone

# Entrer dans le dossier
cd "$REPO_NAME"

# Configuration Git locale avec les variables d'environnement
git config user.name "$GIT_USER_NAME"
git config user.email "$GITHUB_EMAIL"

# Créer un README avec template
cat > README.md << EOF
# $REPO_NAME

$REPO_DESCRIPTION

## Installation

\`\`\`bash
git clone https://github.com/$GITHUB_USERNAME/$REPO_NAME
cd $REPO_NAME
\`\`\`

## Auteur

**$GIT_USER_NAME** - [$GITHUB_USERNAME](https://github.com/$GITHUB_USERNAME)
EOF

# Premier commit
git add README.md
git commit -m "docs: add initial README"
git push origin "$MAIN_BRANCH"

echo "✅ Repository créé avec succès!"
echo "🔗 https://github.com/$GITHUB_USERNAME/$REPO_NAME"
```

### Script de configuration d'environnement

```bash
#!/bin/bash
# setup-env.sh - Configuration automatique de l'environnement

echo "⚙️  Configuration de l'environnement GitHub CLI"

# === COLLECTE DES INFORMATIONS ===

echo ""
echo "📝 Informations utilisateur:"

# Nom d'utilisateur GitHub
if [ -z "$GITHUB_USERNAME" ]; then
    read -p "Nom d'utilisateur GitHub: " GITHUB_USERNAME
fi

# Email
if [ -z "$GITHUB_EMAIL" ]; then
    read -p "Email GitHub: " GITHUB_EMAIL
fi

# Nom complet pour Git
if [ -z "$GIT_USER_NAME" ]; then
    read -p "Nom complet (pour Git): " GIT_USER_NAME
fi

# Éditeur préféré
if [ -z "$EDITOR" ]; then
    echo "Éditeurs disponibles: code (VS Code), nano, vim"
    read -p "Éditeur préféré (code): " EDITOR
    EDITOR="${EDITOR:-code}"
fi

# === CRÉATION DU FICHIER .env ===

echo ""
echo "📁 Création du fichier .env..."

cat > .env << EOF
# Configuration GitHub CLI - Projet $(basename "$PWD")
# Généré le $(date)

# === Informations utilisateur ===
GITHUB_USERNAME=$GITHUB_USERNAME
GITHUB_EMAIL=$GITHUB_EMAIL
GIT_USER_NAME=$GIT_USER_NAME

# === Configuration outils ===
EDITOR=$EDITOR
GH_PAGER=""

# === Configuration projet ===
MAIN_BRANCH=main
DEFAULT_REPO_VISIBILITY=public

# === Équipe (à personnaliser) ===
DEFAULT_REVIEWER=
TEAM_SLACK_CHANNEL=

# === Notes ===
# Ajoutez votre GITHUB_TOKEN dans .env.local (non versionné)
# Ne commitez JAMAIS de tokens dans Git!
EOF

# === CONFIGURATION .gitignore ===

echo "🔒 Configuration .gitignore pour la sécurité..."

# Créer ou mettre à jour .gitignore
if [ ! -f ".gitignore" ]; then
    touch .gitignore
fi

# Ajouter les fichiers sensibles s'ils n'y sont pas déjà
grep -q ".env.local" .gitignore || echo ".env.local" >> .gitignore
grep -q "*.token" .gitignore || echo "*.token" >> .gitignore
grep -q ".secrets" .gitignore || echo ".secrets" >> .gitignore

# === TOKEN D'ACCÈS ===

echo ""
echo "🔑 Configuration du token d'accès:"
echo "   1. Allez sur: https://github.com/settings/tokens"
echo "   2. Générez un token avec les permissions 'repo'"
echo "   3. Ajoutez-le dans .env.local:"
echo "      echo 'GITHUB_TOKEN=your_token_here' > .env.local"

# === FINALISATION ===

echo ""
echo "✅ Configuration terminée!"
echo ""
echo "📋 Fichiers créés:"
echo "   - .env (configuration du projet)"
echo "   - .gitignore (mis à jour pour la sécurité)"
echo ""
echo "🔧 Prochaines étapes:"
echo "   1. Configurez votre token dans .env.local"
echo "   2. Chargez la configuration: source .env"
echo "   3. Testez: gh auth status"
echo ""
echo "💡 Conseil: Ajoutez 'source .env' au début de vos scripts"
```

## Validation et debugging

### Script de vérification de l'environnement

```bash
#!/bin/bash
# check-env.sh - Vérifier la configuration de l'environnement

echo "🔍 Vérification de l'environnement GitHub CLI"
echo ""

# === FONCTION DE VALIDATION ===

check_var() {
    local var_name="$1"
    local var_value="${!var_name}"
    local is_secret="$2"

    if [ -n "$var_value" ]; then
        if [ "$is_secret" = "secret" ]; then
            echo "✅ $var_name: [DÉFINI]"
        else
            echo "✅ $var_name: $var_value"
        fi
        return 0
    else
        echo "❌ $var_name: [NON DÉFINI]"
        return 1
    fi
}

# === VÉRIFICATION DES VARIABLES ===

echo "📋 Variables utilisateur:"
check_var "GITHUB_USERNAME"
check_var "GITHUB_EMAIL"
check_var "GIT_USER_NAME"

echo ""
echo "🔑 Variables d'authentification:"
check_var "GITHUB_TOKEN" "secret"

echo ""
echo "⚙️  Variables de configuration:"
check_var "EDITOR"
check_var "MAIN_BRANCH"
check_var "GH_PAGER"

# === VÉRIFICATION DES OUTILS ===

echo ""
echo "🛠️  Outils disponibles:"

if command -v gh &> /dev/null; then
    GH_VERSION=$(gh version | head -n1)
    echo "✅ GitHub CLI: $GH_VERSION"
else
    echo "❌ GitHub CLI: non installé"
fi

if command -v git &> /dev/null; then
    GIT_VERSION=$(git --version)
    echo "✅ Git: $GIT_VERSION"
else
    echo "❌ Git: non installé"
fi

# === TEST D'AUTHENTIFICATION ===

echo ""
echo "🔐 Test d'authentification GitHub:"

if gh auth status &> /dev/null; then
    AUTHENTICATED_USER=$(gh api user --jq .login 2>/dev/null)
    echo "✅ Authentifié en tant que: $AUTHENTICATED_USER"

    # Vérifier la cohérence
    if [ "$AUTHENTICATED_USER" = "$GITHUB_USERNAME" ]; then
        echo "✅ Cohérence: variable GITHUB_USERNAME correspond"
    else
        echo "⚠️  Incohérence: GITHUB_USERNAME ($GITHUB_USERNAME) ≠ utilisateur authentifié ($AUTHENTICATED_USER)"
    fi
else
    echo "❌ Non authentifié ou token invalide"
fi

# === RÉSUMÉ ===

echo ""
echo "📊 Résumé:"

ISSUES=()

if [ -z "$GITHUB_TOKEN" ]; then
    ISSUES+=("Configurez GITHUB_TOKEN dans .env.local")
fi

if [ -z "$GITHUB_USERNAME" ]; then
    ISSUES+=("Définissez GITHUB_USERNAME dans .env")
fi

if ! gh auth status &> /dev/null; then
    ISSUES+=("Authentifiez-vous avec: gh auth login")
fi

if [ ${#ISSUES[@]} -eq 0 ]; then
    echo "🎉 Environnement correctement configuré!"
else
    echo "⚠️  Problèmes détectés:"
    for issue in "${ISSUES[@]}"; do
        echo "   - $issue"
    done
fi
```

## Variables d'environnement par contexte

### Configuration par environnement (dev/staging/prod)

```bash
# === Fichier: envs/dev.env ===
ENVIRONMENT=development
GITHUB_ORG=mon-org-dev
API_BASE_URL=https://api-dev.monapp.com
DEBUG=1
VERBOSE=1

# === Fichier: envs/staging.env ===
ENVIRONMENT=staging
GITHUB_ORG=mon-org-staging
API_BASE_URL=https://api-staging.monapp.com
DEBUG=0
VERBOSE=0

# === Fichier: envs/prod.env ===
ENVIRONMENT=production
GITHUB_ORG=mon-org
API_BASE_URL=https://api.monapp.com
DEBUG=0
VERBOSE=0
```

**Script pour charger l'environnement approprié :**
```bash
#!/bin/bash
# load-environment.sh

ENV="${1:-dev}"  # Par défaut: dev

case "$ENV" in
    "dev"|"development")
        ENV_FILE="envs/dev.env"
        ;;
    "staging"|"stage")
        ENV_FILE="envs/staging.env"
        ;;
    "prod"|"production")
        ENV_FILE="envs/prod.env"
        ;;
    *)
        echo "❌ Environnement invalide: $ENV"
        echo "   Utilisez: dev, staging, ou prod"
        exit 1
        ;;
esac

if [ -f "$ENV_FILE" ]; then
    echo "🔄 Chargement de l'environnement: $ENV"
    source "$ENV_FILE"
    echo "✅ Variables chargées depuis $ENV_FILE"
    echo "   ENVIRONMENT=$ENVIRONMENT"
    echo "   GITHUB_ORG=$GITHUB_ORG"
else
    echo "❌ Fichier d'environnement non trouvé: $ENV_FILE"
    exit 1
fi
```

**Utilisation :**
```bash
# Charger l'environnement de développement
source load-environment.sh dev

# Charger l'environnement de production
source load-environment.sh prod

# Dans un script
#!/bin/bash
source load-environment.sh "${DEPLOY_ENV:-dev}"
gh repo create "$GITHUB_ORG/nouveau-projet"
```

## Bonnes pratiques

### 1. Hiérarchie des variables

```bash
# Ordre de priorité (du plus faible au plus fort):
# 1. Valeurs par défaut dans le script
DEFAULT_BRANCH="main"

# 2. Fichier .env (configuration projet)
source .env 2>/dev/null

# 3. Fichier .env.local (configuration locale)
source .env.local 2>/dev/null

# 4. Variables d'environnement système
# (déjà chargées automatiquement)

# 5. Paramètres de ligne de commande
BRANCH="${1:-$DEFAULT_BRANCH}"
```

### 2. Validation robuste

```bash
#!/bin/bash

# Fonction de validation complète
validate_environment() {
    local errors=0

    # Variables obligatoires
    local required_vars=("GITHUB_TOKEN" "GITHUB_USERNAME")

    for var in "${required_vars[@]}"; do
        if [ -z "${!var}" ]; then
            echo "❌ Variable obligatoire manquante: $var"
            ((errors++))
        fi
    done

    # Format des variables
    if [[ -n "$GITHUB_USERNAME" && ! "$GITHUB_USERNAME" =~ ^[a-zA-Z0-9-]+$ ]]; then
        echo "❌ GITHUB_USERNAME contient des caractères invalides"
        ((errors++))
    fi

    # Longueur du token
    if [[ -n "$GITHUB_TOKEN" && ${#GITHUB_TOKEN} -lt 40 ]]; then
        echo "❌ GITHUB_TOKEN semble trop court (token invalide?)"
        ((errors++))
    fi

    return $errors
}

# Utiliser la validation
if ! validate_environment; then
    echo "❌ Configuration invalide, arrêt du script"
    exit 1
fi
```

### 3. Documentation des variables

```bash
# === Configuration requise ===
#
# Variables obligatoires:
#   GITHUB_TOKEN     - Token d'accès personnel GitHub
#   GITHUB_USERNAME  - Nom d'utilisateur GitHub
#
# Variables optionnelles:
#   GITHUB_EMAIL     - Email (défaut: git config user.email)
#   EDITOR          - Éditeur de texte (défaut: nano)
#   MAIN_BRANCH     - Branche principale (défaut: main)
#
# Exemple de configuration:
#   export GITHUB_USERNAME="alice"
#   export GITHUB_EMAIL="alice@example.com"
#   export EDITOR="code"
```

## Résumé des bonnes pratiques

**🔧 Configuration :**
- Utilisez des fichiers .env pour la configuration de projet
- Séparez les secrets dans .env.local (non versionné)
- Définissez des valeurs par défaut sensées

**🔒 Sécurité :**
- JAMAIS de tokens dans le code ou Git
- Ajoutez .env.local à .gitignore
- Validez le format des variables sensibles

**📋 Organisation :**
- Variables en MAJUSCULES pour les constantes
- Noms descriptifs et cohérents
- Documentez les variables requises

**🛡️ Robustesse :**
- Validez toujours les variables critiques
- Fournissez des messages d'erreur clairs
- Testez la configuration avec des scripts dédiés

Les variables d'environnement transforment vos scripts en outils flexibles et réutilisables, tout en maintenant la sécurité et la portabilité !

⏭️
