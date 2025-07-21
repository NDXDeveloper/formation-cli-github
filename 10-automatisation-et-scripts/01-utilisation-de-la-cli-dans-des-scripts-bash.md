🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 10.1 Utilisation de la CLI dans des scripts bash

## Introduction aux scripts bash avec GitHub CLI

Un script bash est comme une recette de cuisine automatisée : vous écrivez une fois la séquence d'instructions, puis vous l'exécutez autant de fois que nécessaire. Combiner bash avec GitHub CLI vous permet de créer des outils puissants qui automatisent vos workflows GitHub.

## Premiers pas avec bash et GitHub CLI

### Structure de base d'un script

Créons notre premier script simple :

```bash
#!/bin/bash
# mon-premier-script.sh - Script de démonstration

echo "🚀 Démarrage du script GitHub CLI"

# Vérifier l'authentification
gh auth status

echo "✅ Script terminé"
```

**Explication ligne par ligne :**
- `#!/bin/bash` : Indique que c'est un script bash (shebang)
- `# commentaire` : Les lignes avec # sont des commentaires
- `echo` : Affiche du texte à l'écran
- `gh auth status` : Commande GitHub CLI pour vérifier l'authentification

### Rendre le script exécutable

```bash
# Donner les permissions d'exécution
chmod +x mon-premier-script.sh

# Exécuter le script
./mon-premier-script.sh
```

## Variables dans les scripts

### Variables simples

```bash
#!/bin/bash
# Variables de base

# Définir des variables
REPO_NAME="mon-projet"
BRANCH_NAME="feature/nouvelle-fonctionnalite"
ASSIGNEE="@me"

# Utiliser les variables (avec $)
echo "Création de la branche : $BRANCH_NAME"
echo "Dans le repository : $REPO_NAME"
echo "Assigné à : $ASSIGNEE"

# Utiliser les variables dans les commandes GitHub CLI
gh issue create --title "Nouvelle tâche" --assignee "$ASSIGNEE"
```

**💡 Bonnes pratiques pour les variables :**
- Noms en MAJUSCULES pour les variables "globales"
- Toujours encadrer avec des guillemets : `"$VARIABLE"`
- Noms descriptifs : `USER_EMAIL` plutôt que `EMAIL`

### Variables depuis la ligne de commande

```bash
#!/bin/bash
# script-avec-parametres.sh

# Récupérer les paramètres passés au script
FEATURE_NAME="$1"    # Premier paramètre
ASSIGNEE="$2"        # Deuxième paramètre

# Vérifier qu'on a les paramètres nécessaires
if [ -z "$FEATURE_NAME" ]; then
    echo "❌ Usage: $0 <nom-feature> [assignee]"
    echo "   Exemple: $0 user-login @alice"
    exit 1
fi

# Utiliser une valeur par défaut si pas d'assignee
if [ -z "$ASSIGNEE" ]; then
    ASSIGNEE="@me"
fi

echo "🔧 Création de la feature: $FEATURE_NAME"
echo "👤 Assignée à: $ASSIGNEE"

# Créer la branche et l'issue
git checkout -b "feature/$FEATURE_NAME"
gh issue create --title "Implémentation: $FEATURE_NAME" --assignee "$ASSIGNEE"
```

**Utilisation :**
```bash
./script-avec-parametres.sh user-login @bob
./script-avec-parametres.sh shopping-cart     # Utilisera @me par défaut
```

## Gestion des erreurs et vérifications

### Vérifications de base

```bash
#!/bin/bash
# script-avec-verifications.sh

echo "🔍 Vérifications préliminaires..."

# Vérifier que GitHub CLI est installé
if ! command -v gh &> /dev/null; then
    echo "❌ GitHub CLI n'est pas installé"
    echo "   Installez-le depuis: https://cli.github.com"
    exit 1
fi

# Vérifier l'authentification GitHub
if ! gh auth status &> /dev/null; then
    echo "❌ Non authentifié à GitHub"
    echo "   Exécutez: gh auth login"
    exit 1
fi

# Vérifier qu'on est dans un repository Git
if ! git rev-parse --git-dir &> /dev/null; then
    echo "❌ Pas dans un repository Git"
    echo "   Naviguez dans un dossier Git ou initialisez avec: git init"
    exit 1
fi

echo "✅ Toutes les vérifications sont passées"
echo "🚀 Prêt à continuer..."
```

### Gestion des erreurs GitHub CLI

```bash
#!/bin/bash
# script-gestion-erreurs.sh

ISSUE_TITLE="$1"

if [ -z "$ISSUE_TITLE" ]; then
    echo "❌ Veuillez fournir un titre pour l'issue"
    exit 1
fi

echo "📝 Création de l'issue: $ISSUE_TITLE"

# Essayer de créer l'issue et capturer le résultat
if gh issue create --title "$ISSUE_TITLE" --body "Issue créée automatiquement"; then
    echo "✅ Issue créée avec succès"

    # Récupérer le numéro de la dernière issue créée
    ISSUE_NUMBER=$(gh issue list --limit 1 --json number --jq '.[0].number')
    echo "🔗 Numéro de l'issue: #$ISSUE_NUMBER"
else
    echo "❌ Erreur lors de la création de l'issue"
    echo "   Vérifiez vos permissions sur le repository"
    exit 1
fi
```

## Scripts de workflow complets

### Script de création de feature branch

```bash
#!/bin/bash
# new-feature.sh - Créer une nouvelle feature complète

set -e  # Arrêter le script en cas d'erreur

FEATURE_NAME="$1"
DESCRIPTION="$2"

# Validation des paramètres
if [ -z "$FEATURE_NAME" ]; then
    echo "❌ Usage: $0 <nom-feature> [description]"
    echo "   Exemple: $0 user-authentication 'Système de connexion utilisateur'"
    exit 1
fi

if [ -z "$DESCRIPTION" ]; then
    DESCRIPTION="Implémentation de la feature: $FEATURE_NAME"
fi

echo "🚀 Création de la feature: $FEATURE_NAME"

# 1. Synchroniser avec main
echo "🔄 Synchronisation avec main..."
git checkout main
git pull origin main

# 2. Créer la branche feature
echo "🌿 Création de la branche..."
BRANCH_NAME="feature/$FEATURE_NAME"
git checkout -b "$BRANCH_NAME"

# 3. Créer l'issue GitHub
echo "📝 Création de l'issue GitHub..."
ISSUE_BODY="## Description
$DESCRIPTION

## Tâches
- [ ] Analyse et conception
- [ ] Implémentation
- [ ] Tests
- [ ] Documentation

## Critères d'acceptation
À définir...

---
*Issue créée automatiquement par le script new-feature.sh*"

gh issue create \
    --title "Feature: $FEATURE_NAME" \
    --body "$ISSUE_BODY" \
    --assignee @me \
    --label "enhancement"

# 4. Faire un premier commit vide pour initialiser la branche
echo "📌 Commit initial..."
git commit --allow-empty -m "feat: initialize $FEATURE_NAME feature branch

Related to: $(gh issue list --limit 1 --json number --jq '.[0].number')"

# 5. Pousser la branche
echo "📤 Push de la branche..."
git push -u origin "$BRANCH_NAME"

echo ""
echo "✅ Feature branch créée avec succès!"
echo "🌿 Branche: $BRANCH_NAME"
echo "📝 Issue: $(gh issue list --limit 1 --json url --jq '.[0].url')"
echo ""
echo "🔧 Prochaines étapes:"
echo "   1. Développer la fonctionnalité"
echo "   2. Faire des commits réguliers"
echo "   3. Créer une PR avec: gh pr create"
```

### Script de workflow PR

```bash
#!/bin/bash
# create-pr.sh - Créer une PR avec template

set -e

# Récupérer la branche courante
CURRENT_BRANCH=$(git branch --show-current)

# Vérifier qu'on n'est pas sur main
if [ "$CURRENT_BRANCH" = "main" ] || [ "$CURRENT_BRANCH" = "master" ]; then
    echo "❌ Ne peut pas créer une PR depuis la branche principale"
    echo "   Basculez sur une feature branch d'abord"
    exit 1
fi

# Vérifier qu'il y a des commits à pusher
if git diff --quiet origin/"$CURRENT_BRANCH" 2>/dev/null; then
    echo "❌ Aucun changement à pusher"
    echo "   Faites des commits d'abord"
    exit 1
fi

echo "📤 Création de la PR pour la branche: $CURRENT_BRANCH"

# Pusher les derniers changements
echo "🔄 Push des derniers changements..."
git push origin "$CURRENT_BRANCH"

# Extraire le nom de la feature depuis la branche
FEATURE_NAME=$(echo "$CURRENT_BRANCH" | sed 's/feature\///' | sed 's/-/ /g')

# Construire le titre et le corps de la PR
PR_TITLE="Add: $FEATURE_NAME"
PR_BODY="## Description
Cette PR implémente la fonctionnalité: $FEATURE_NAME

## Changements
- Ajout de [décrire les changements]
- Modification de [décrire les modifications]

## Type de changement
- [x] Nouvelle fonctionnalité (feature)
- [ ] Correction de bug (bugfix)
- [ ] Amélioration (enhancement)

## Tests
- [ ] Tests unitaires ajoutés/mis à jour
- [ ] Tests d'intégration validés
- [ ] Tests manuels effectués

## Checklist
- [ ] Code self-reviewed
- [ ] Documentation mise à jour si nécessaire
- [ ] Pas de conflits avec la branche main

---
*PR créée automatiquement par le script create-pr.sh*"

# Créer la PR
echo "📝 Création de la Pull Request..."
gh pr create \
    --title "$PR_TITLE" \
    --body "$PR_BODY" \
    --assignee @me

# Récupérer l'URL de la PR créée
PR_URL=$(gh pr view --json url --jq .url)

echo ""
echo "✅ Pull Request créée avec succès!"
echo "🔗 URL: $PR_URL"
echo ""
echo "🔧 Prochaines étapes:"
echo "   1. Demander une review: gh pr edit --add-reviewer @teammate"
echo "   2. Suivre les tests: gh pr checks"
echo "   3. Merger quand prêt: gh pr merge"
```

## Boucles et opérations en masse

### Traiter plusieurs PRs

```bash
#!/bin/bash
# check-all-prs.sh - Vérifier l'état de toutes les PRs ouvertes

echo "📋 Vérification de toutes les PRs ouvertes..."

# Récupérer la liste des PRs (format JSON)
PR_LIST=$(gh pr list --json number,title,author,url,statusCheckRollupState)

# Vérifier si on a des PRs
PR_COUNT=$(echo "$PR_LIST" | jq length)

if [ "$PR_COUNT" -eq 0 ]; then
    echo "✅ Aucune PR ouverte"
    exit 0
fi

echo "📊 $PR_COUNT PR(s) trouvée(s):"
echo ""

# Boucle pour traiter chaque PR
echo "$PR_LIST" | jq -r '.[] | @base64' | while read -r pr_data; do
    # Décoder les données de la PR
    PR_INFO=$(echo "$pr_data" | base64 --decode)

    PR_NUMBER=$(echo "$PR_INFO" | jq -r '.number')
    PR_TITLE=$(echo "$PR_INFO" | jq -r '.title')
    PR_AUTHOR=$(echo "$PR_INFO" | jq -r '.author.login')
    PR_STATUS=$(echo "$PR_INFO" | jq -r '.statusCheckRollupState // "PENDING"')

    # Affichage coloré selon le statut
    case "$PR_STATUS" in
        "SUCCESS")
            STATUS_ICON="✅"
            ;;
        "FAILURE")
            STATUS_ICON="❌"
            ;;
        "PENDING"|"null")
            STATUS_ICON="⏳"
            ;;
        *)
            STATUS_ICON="❓"
            ;;
    esac

    echo "$STATUS_ICON PR #$PR_NUMBER - $PR_TITLE (@$PR_AUTHOR)"
done

echo ""
echo "🔧 Commandes utiles:"
echo "   Voir une PR: gh pr view <number>"
echo "   Voir les checks: gh pr checks <number>"
echo "   Merger une PR: gh pr merge <number>"
```

### Nettoyage automatique des branches

```bash
#!/bin/bash
# cleanup-branches.sh - Nettoyer les branches mergées

echo "🧹 Nettoyage des branches obsolètes..."

# Synchroniser avec origin
git fetch origin --prune

# Basculer sur main pour être sûr
git checkout main
git pull origin main

echo "🔍 Recherche des branches mergées..."

# Lister les branches mergées (sauf main/master)
MERGED_BRANCHES=$(git branch --merged | grep -v -E "(main|master|\*)" | tr -d ' ')

if [ -z "$MERGED_BRANCHES" ]; then
    echo "✅ Aucune branche à nettoyer"
    exit 0
fi

echo "📋 Branches mergées trouvées:"
echo "$MERGED_BRANCHES"
echo ""

# Demander confirmation
read -p "🗑️  Supprimer ces branches? (y/N): " -n 1 -r
echo ""

if [[ $REPLY =~ ^[Yy]$ ]]; then
    echo "🗑️  Suppression des branches..."

    # Supprimer chaque branche
    echo "$MERGED_BRANCHES" | while read -r branch; do
        if [ -n "$branch" ]; then
            echo "   Suppression de: $branch"
            git branch -d "$branch"
        fi
    done

    echo "✅ Nettoyage terminé!"
else
    echo "❌ Nettoyage annulé"
fi
```

## Fonctions réutilisables

### Créer des fonctions dans vos scripts

```bash
#!/bin/bash
# script-avec-fonctions.sh

# === FONCTIONS ===

# Fonction pour afficher des messages colorés
log_info() {
    echo "ℹ️  $1"
}

log_success() {
    echo "✅ $1"
}

log_error() {
    echo "❌ $1" >&2
}

log_warning() {
    echo "⚠️  $1"
}

# Fonction pour vérifier les prérequis
check_prerequisites() {
    log_info "Vérification des prérequis..."

    if ! command -v gh &> /dev/null; then
        log_error "GitHub CLI non installé"
        return 1
    fi

    if ! gh auth status &> /dev/null; then
        log_error "Non authentifié à GitHub"
        return 1
    fi

    if ! git rev-parse --git-dir &> /dev/null; then
        log_error "Pas dans un repository Git"
        return 1
    fi

    log_success "Tous les prérequis sont satisfaits"
    return 0
}

# Fonction pour créer une issue avec template
create_issue_with_template() {
    local title="$1"
    local type="$2"

    case "$type" in
        "bug")
            local body="## 🐛 Description du bug
Décrivez le problème...

## 🔄 Étapes pour reproduire
1. Aller à...
2. Cliquer sur...
3. Voir l'erreur

## ✅ Comportement attendu
Décrivez ce qui devrait se passer...

## 📱 Environnement
- OS: [ex: macOS 12]
- Navigateur: [ex: Chrome 91]
- Version: [ex: v1.2.0]"
            ;;
        "feature")
            local body="## 🚀 Nouvelle fonctionnalité
Décrivez la fonctionnalité demandée...

## 💡 Motivation
Pourquoi cette fonctionnalité est-elle nécessaire?

## 📝 Solution proposée
Comment imaginez-vous cette fonctionnalité?

## 🔄 Alternatives considérées
Y a-t-il d'autres approches possibles?"
            ;;
        *)
            local body="Description de l'issue..."
            ;;
    esac

    gh issue create --title "$title" --body "$body"
}

# === SCRIPT PRINCIPAL ===

# Vérifier les prérequis
if ! check_prerequisites; then
    exit 1
fi

# Utiliser les fonctions
ISSUE_TITLE="$1"
ISSUE_TYPE="$2"

if [ -z "$ISSUE_TITLE" ]; then
    log_error "Usage: $0 <titre> [bug|feature]"
    exit 1
fi

if [ -z "$ISSUE_TYPE" ]; then
    ISSUE_TYPE="feature"
fi

log_info "Création d'une issue de type: $ISSUE_TYPE"
create_issue_with_template "$ISSUE_TITLE" "$ISSUE_TYPE"
log_success "Issue créée avec succès!"
```

## Debugging et logging

### Script avec logging avancé

```bash
#!/bin/bash
# script-avec-logging.sh

# === CONFIGURATION DU LOGGING ===

# Créer un dossier de logs s'il n'existe pas
LOG_DIR="./logs"
mkdir -p "$LOG_DIR"

# Fichier de log avec timestamp
LOG_FILE="$LOG_DIR/github-script-$(date +%Y%m%d-%H%M%S).log"

# Fonction de logging
log() {
    local level="$1"
    shift
    local message="$*"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')

    # Écrire dans le fichier de log
    echo "[$timestamp] [$level] $message" >> "$LOG_FILE"

    # Afficher aussi à l'écran avec couleurs
    case "$level" in
        "INFO")
            echo "ℹ️  $message"
            ;;
        "SUCCESS")
            echo "✅ $message"
            ;;
        "WARNING")
            echo "⚠️  $message"
            ;;
        "ERROR")
            echo "❌ $message" >&2
            ;;
        "DEBUG")
            if [ "$DEBUG" = "1" ]; then
                echo "🔍 $message"
            fi
            ;;
    esac
}

# === SCRIPT AVEC LOGGING ===

log "INFO" "Démarrage du script"
log "DEBUG" "Variables d'environnement: USER=$USER, PWD=$PWD"

# Exemple d'opération avec logging
REPO_NAME=$(gh repo view --json name --jq .name 2>/dev/null)

if [ $? -eq 0 ]; then
    log "SUCCESS" "Repository détecté: $REPO_NAME"
else
    log "ERROR" "Impossible de détecter le repository"
    exit 1
fi

# Opération GitHub CLI avec logging
log "INFO" "Récupération de la liste des PRs..."
PR_COUNT=$(gh pr list --json number | jq length)
log "INFO" "Nombre de PRs ouvertes: $PR_COUNT"

log "SUCCESS" "Script terminé avec succès"
echo ""
echo "📄 Log complet disponible dans: $LOG_FILE"
```

**Usage avec debug :**
```bash
# Exécution normale
./script-avec-logging.sh

# Exécution avec debug
DEBUG=1 ./script-avec-logging.sh
```

## Conseils pour des scripts robustes

### 1. Toujours valider les entrées

```bash
#!/bin/bash

validate_input() {
    local repo_name="$1"

    # Vérifier que le nom n'est pas vide
    if [ -z "$repo_name" ]; then
        echo "❌ Le nom du repository ne peut pas être vide"
        return 1
    fi

    # Vérifier le format (lettres, chiffres, tirets)
    if ! [[ "$repo_name" =~ ^[a-zA-Z0-9-]+$ ]]; then
        echo "❌ Le nom du repository contient des caractères invalides"
        echo "   Utilisez seulement des lettres, chiffres et tirets"
        return 1
    fi

    return 0
}

# Utilisation
REPO_NAME="$1"
if ! validate_input "$REPO_NAME"; then
    exit 1
fi
```

### 2. Gérer les timeouts et interruptions

```bash
#!/bin/bash

# Gérer l'interruption proprement (Ctrl+C)
cleanup() {
    echo ""
    echo "🛑 Script interrompu par l'utilisateur"
    echo "🧹 Nettoyage en cours..."
    # Ici, ajouter le code de nettoyage si nécessaire
    exit 130
}

# Capturer le signal d'interruption
trap cleanup INT

# Timeout pour les commandes longues
timeout 30 gh repo clone large-repository || {
    echo "❌ Timeout: la commande a pris trop de temps"
    exit 1
}
```

### 3. Mode dry-run pour tester

```bash
#!/bin/bash
# Script avec mode dry-run

DRY_RUN="$1"

execute() {
    local command="$*"

    if [ "$DRY_RUN" = "--dry-run" ]; then
        echo "🔍 [DRY-RUN] Exécuterait: $command"
    else
        echo "▶️  Exécution: $command"
        eval "$command"
    fi
}

# Utilisation
execute "gh issue create --title 'Test issue' --body 'Corps du test'"
execute "git commit -m 'Test commit'"
```

**Usage :**
```bash
./script.sh --dry-run  # Voir ce qui serait fait
./script.sh           # Exécution réelle
```

## Résumé des bonnes pratiques

**🔧 Structure du script :**
- Shebang en première ligne : `#!/bin/bash`
- Commentaires explicatifs
- Validation des paramètres
- Gestion d'erreurs avec `set -e`

**📝 Lisibilité :**
- Variables en MAJUSCULES pour les constantes
- Fonctions pour le code réutilisable
- Messages informatifs pour l'utilisateur
- Logging pour le debugging

**🛡️ Robustesse :**
- Vérifications des prérequis
- Validation des entrées utilisateur
- Gestion des erreurs GitHub CLI
- Mode dry-run pour tester

**🎯 Réutilisabilité :**
- Paramètres configurables
- Documentation intégrée (usage)
- Fonctions modulaires
- Logs pour le debugging

Ces scripts bash avec GitHub CLI vous permettront d'automatiser efficacement vos workflows et de gagner un temps considérable dans vos tâches quotidiennes !

⏭️
