🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 10.4 Exemples de workflows automatisés

## Introduction aux workflows automatisés

Un workflow automatisé est comme un assistant personnel qui exécute une série de tâches répétitives à votre place. Dans cette section, nous allons voir des exemples concrets de workflows complets que vous pouvez adapter à vos projets. Chaque workflow combine GitHub CLI avec d'autres outils pour créer des processus puissants et entièrement automatisés.

## Workflow 1 : Onboarding automatique de nouveaux développeurs

### Problème résolu
Quand un nouveau développeur rejoint l'équipe, il faut lui donner accès aux repositories, l'ajouter aux équipes, créer des issues d'introduction, etc. Ce processus manuel prend du temps et on peut oublier des étapes.

### Solution automatisée

```bash
#!/bin/bash
# onboard-developer.sh - Onboarding automatique d'un nouveau développeur

set -e

# === Configuration ===
NEW_DEVELOPER="$1"
TEAM_NAME="$2"
MENTOR="$3"

if [ -z "$NEW_DEVELOPER" ] || [ -z "$TEAM_NAME" ]; then
    echo "❌ Usage: $0 <username> <team> [mentor]"
    echo "   Exemple: $0 alice frontend @bob"
    exit 1
fi

if [ -z "$MENTOR" ]; then
    MENTOR="@team-lead"
fi

echo "👋 Onboarding automatique de $NEW_DEVELOPER dans l'équipe $TEAM_NAME"

# === Étape 1 : Ajouter aux équipes GitHub ===
echo "👥 Ajout aux équipes GitHub..."

# Ajouter à l'équipe principale
if gh api orgs/"$GITHUB_ORG"/teams/"$TEAM_NAME"/memberships/"$NEW_DEVELOPER" \
    --method PUT \
    --field role=member; then
    echo "✅ $NEW_DEVELOPER ajouté à l'équipe $TEAM_NAME"
else
    echo "❌ Erreur lors de l'ajout à l'équipe"
fi

# Ajouter à l'équipe "developers" (équipe générale)
gh api orgs/"$GITHUB_ORG"/teams/developers/memberships/"$NEW_DEVELOPER" \
    --method PUT \
    --field role=member 2>/dev/null || echo "⚠️  Équipe 'developers' non trouvée"

# === Étape 2 : Donner accès aux repositories ===
echo "📚 Configuration des accès aux repositories..."

# Liste des repos essentiels pour l'équipe
case "$TEAM_NAME" in
    "frontend")
        REPOS=("web-app" "design-system" "docs-frontend")
        ;;
    "backend")
        REPOS=("api-server" "database-schemas" "docs-backend")
        ;;
    "devops")
        REPOS=("infrastructure" "ci-cd-scripts" "monitoring")
        ;;
    *)
        REPOS=("main-project" "documentation")
        ;;
esac

for repo in "${REPOS[@]}"; do
    echo "  📁 Accès au repository: $repo"

    gh api repos/"$GITHUB_ORG"/"$repo"/collaborators/"$NEW_DEVELOPER" \
        --method PUT \
        --field permission=push 2>/dev/null || echo "    ⚠️  Repository $repo non trouvé"
done

# === Étape 3 : Créer des issues d'onboarding ===
echo "📝 Création des issues d'onboarding..."

# Issue principale d'accueil
WELCOME_ISSUE=$(gh issue create \
    --repo "$GITHUB_ORG/main-project" \
    --title "👋 Welcome $NEW_DEVELOPER to the $TEAM_NAME team!" \
    --body "# Welcome to the team! 🎉

Hi @$NEW_DEVELOPER! Welcome to our $TEAM_NAME team. This issue will help you get started.

## Your onboarding checklist

### Account Setup
- [ ] Complete your GitHub profile
- [ ] Enable 2FA on your GitHub account
- [ ] Join our Slack workspace
- [ ] Set up your development environment

### Code Access
- [ ] Clone the main repositories
- [ ] Set up local development environment
- [ ] Run the test suite successfully
- [ ] Make your first small contribution

### Team Integration
- [ ] Meet with your mentor: $MENTOR
- [ ] Attend the next team standup
- [ ] Review our coding standards
- [ ] Read the team documentation

## Resources
- [Team Documentation](https://github.com/$GITHUB_ORG/docs)
- [Development Setup Guide](https://github.com/$GITHUB_ORG/docs/blob/main/setup.md)
- [Coding Standards](https://github.com/$GITHUB_ORG/docs/blob/main/standards.md)

## Questions?
Don't hesitate to ask questions! Tag $MENTOR or any team member.

Welcome aboard! 🚀" \
    --assignee "$NEW_DEVELOPER" \
    --label "onboarding,welcome")

echo "✅ Issue d'accueil créée: #$WELCOME_ISSUE"

# Issue spécifique à l'équipe
TEAM_ISSUE=$(gh issue create \
    --repo "$GITHUB_ORG/main-project" \
    --title "📚 $TEAM_NAME team specific onboarding for $NEW_DEVELOPER" \
    --body "# $TEAM_NAME Team Onboarding

Hi @$NEW_DEVELOPER! Here are the specific tasks for the $TEAM_NAME team:

## Team-specific setup
- [ ] Install team-specific tools and dependencies
- [ ] Join $TEAM_NAME channels in Slack
- [ ] Access team documentation
- [ ] Set up team development workflow

## Your first tasks
- [ ] Review current sprint backlog
- [ ] Pick up a 'good first issue'
- [ ] Pair with $MENTOR on a task
- [ ] Attend team retrospective

## Team contacts
- **Mentor:** $MENTOR
- **Team Lead:** @team-lead
- **Product Owner:** @product-owner

Let's get you up and running! 💪" \
    --assignee "$NEW_DEVELOPER" \
    --assignee "${MENTOR#@}" \
    --label "onboarding,$TEAM_NAME")

echo "✅ Issue équipe créée: #$TEAM_ISSUE"

# === Étape 4 : Créer des issues "Good First Issue" ===
echo "🎯 Création d'issues pour premiers pas..."

FIRST_ISSUE=$(gh issue create \
    --repo "$GITHUB_ORG/main-project" \
    --title "Good first issue: Update team documentation" \
    --body "# Good First Issue for @$NEW_DEVELOPER

This is a perfect first contribution to get familiar with our codebase!

## Task
Add your information to the team documentation:

1. Edit \`docs/team.md\`
2. Add your entry in the $TEAM_NAME section:
   \`\`\`markdown
   - **$NEW_DEVELOPER** - [Role] - [Fun fact about yourself]
   \`\`\`
3. Create a PR with your changes

## Learning goals
- Practice Git workflow
- Get familiar with our PR process
- Learn about our documentation structure

## Need help?
- Check our [Contributing Guide](https://github.com/$GITHUB_ORG/docs/blob/main/CONTRIBUTING.md)
- Ask $MENTOR for guidance
- Look at previous PRs for examples

Good luck! 🍀" \
    --assignee "$NEW_DEVELOPER" \
    --label "good first issue,documentation,onboarding")

echo "✅ Good first issue créée: #$FIRST_ISSUE"

# === Étape 5 : Notifications ===
echo "📧 Envoi des notifications..."

# Notifier l'équipe
gh issue create \
    --repo "$GITHUB_ORG/main-project" \
    --title "📢 Team Update: $NEW_DEVELOPER joined $TEAM_NAME" \
    --body "👋 Everyone, please welcome @$NEW_DEVELOPER to the $TEAM_NAME team!

**Mentor:** $MENTOR
**Start Date:** $(date +%Y-%m-%d)
**Onboarding Issues:** #$WELCOME_ISSUE, #$TEAM_ISSUE

Please help make $NEW_DEVELOPER feel welcome and don't hesitate to offer assistance during the onboarding process.

Welcome to the team! 🎉" \
    --label "team-update,announcement" \
    --assignee "@team-members"

# === Étape 6 : Configuration automatique de l'environnement ===
echo "⚙️  Génération du script de setup personnalisé..."

cat > "setup-$NEW_DEVELOPER.sh" << EOF
#!/bin/bash
# Setup automatique pour $NEW_DEVELOPER

echo "👋 Setup de l'environnement pour $NEW_DEVELOPER"

# Configuration Git
git config --global user.name "$NEW_DEVELOPER"
echo "Configurez votre email avec: git config --global user.email 'your-email@company.com'"

# Clonage des repositories essentiels
mkdir -p ~/projects/$GITHUB_ORG
cd ~/projects/$GITHUB_ORG

echo "📁 Clonage des repositories..."
EOF

for repo in "${REPOS[@]}"; do
    echo "gh repo clone $GITHUB_ORG/$repo" >> "setup-$NEW_DEVELOPER.sh"
done

cat >> "setup-$NEW_DEVELOPER.sh" << EOF

echo "✅ Setup terminé!"
echo "📋 Prochaines étapes:"
echo "   1. Consultez l'issue #$WELCOME_ISSUE"
echo "   2. Contactez votre mentor: $MENTOR"
echo "   3. Rejoignez le Slack de l'équipe"

echo "Bienvenue dans l'équipe! 🚀"
EOF

chmod +x "setup-$NEW_DEVELOPER.sh"

echo "✅ Script de setup généré: setup-$NEW_DEVELOPER.sh"

# === Résumé ===
echo ""
echo "🎉 Onboarding automatique terminé pour $NEW_DEVELOPER!"
echo ""
echo "📋 Résumé des actions:"
echo "   ✅ Ajouté à l'équipe $TEAM_NAME"
echo "   ✅ Accès configuré aux repositories: ${REPOS[*]}"
echo "   ✅ Issue d'accueil créée: #$WELCOME_ISSUE"
echo "   ✅ Issue équipe créée: #$TEAM_ISSUE"
echo "   ✅ Good first issue créée: #$FIRST_ISSUE"
echo "   ✅ Équipe notifiée"
echo "   ✅ Script de setup généré"
echo ""
echo "🔧 Actions manuelles restantes:"
echo "   - Envoyer le script setup-$NEW_DEVELOPER.sh"
echo "   - Inviter sur Slack"
echo "   - Programmer la rencontre avec le mentor"
```

## Workflow 2 : Release automatique avec changelog

### Problème résolu
Créer une release implique de nombreuses étapes : tests, build, tag, notes de release, déploiement, notifications. Faire cela manuellement est chronophage et sujet aux erreurs.

### Solution automatisée

```bash
#!/bin/bash
# auto-release.sh - Release automatique complète

set -e

# === Configuration ===
VERSION="$1"
RELEASE_TYPE="${2:-patch}"  # major, minor, patch
DRY_RUN="${3:-false}"

if [ -z "$VERSION" ]; then
    echo "❌ Usage: $0 <version> [release-type] [dry-run]"
    echo "   Exemple: $0 v1.2.3 minor false"
    echo "   Types: major, minor, patch"
    exit 1
fi

echo "🚀 Début du processus de release $VERSION ($RELEASE_TYPE)"

if [ "$DRY_RUN" = "true" ]; then
    echo "🔍 MODE DRY-RUN: Aucune action réelle ne sera effectuée"
fi

# === Fonction d'exécution conditionnelle ===
execute() {
    local command="$*"

    if [ "$DRY_RUN" = "true" ]; then
        echo "🔍 [DRY-RUN] $command"
    else
        echo "▶️  $command"
        eval "$command"
    fi
}

# === Étape 1 : Vérifications préliminaires ===
echo "🔍 Vérifications préliminaires..."

# Vérifier qu'on est sur main et à jour
CURRENT_BRANCH=$(git branch --show-current)
if [ "$CURRENT_BRANCH" != "main" ]; then
    echo "❌ Vous devez être sur la branche main pour faire une release"
    exit 1
fi

git fetch origin
if ! git diff --quiet origin/main; then
    echo "❌ Votre branche main n'est pas à jour avec origin/main"
    echo "   Exécutez: git pull origin main"
    exit 1
fi

# Vérifier qu'il n'y a pas de changements non commitétés
if ! git diff --quiet || ! git diff --cached --quiet; then
    echo "❌ Il y a des changements non commitétés"
    echo "   Commitéz ou stashéz vos changements d'abord"
    exit 1
fi

# Vérifier que la version n'existe pas déjà
if git tag | grep -q "^$VERSION$"; then
    echo "❌ La version $VERSION existe déjà"
    exit 1
fi

echo "✅ Toutes les vérifications sont passées"

# === Étape 2 : Tests complets ===
echo "🧪 Lancement des tests complets..."

execute "npm ci"
execute "npm run test:full"
execute "npm run lint"
execute "npm run build"

if [ "$DRY_RUN" != "true" ]; then
    echo "✅ Tous les tests sont passés"
fi

# === Étape 3 : Génération du changelog ===
echo "📝 Génération du changelog..."

# Récupérer le dernier tag
LAST_TAG=$(git describe --tags --abbrev=0 2>/dev/null || echo "")

if [ -z "$LAST_TAG" ]; then
    echo "ℹ️  Première release, génération du changelog complet"
    COMMIT_RANGE="HEAD"
else
    echo "ℹ️  Dernière version: $LAST_TAG"
    COMMIT_RANGE="$LAST_TAG..HEAD"
fi

# Générer le changelog structuré
CHANGELOG_FILE="CHANGELOG-$VERSION.md"

cat > "$CHANGELOG_FILE" << EOF
# Release $VERSION

**Release Date:** $(date +"%Y-%m-%d")
**Release Type:** $RELEASE_TYPE

## What's Changed

EOF

# Extraire les commits par catégorie
echo "### ✨ New Features" >> "$CHANGELOG_FILE"
git log "$COMMIT_RANGE" --pretty=format:"- %s (%h)" --grep="^feat" >> "$CHANGELOG_FILE" 2>/dev/null || true
echo "" >> "$CHANGELOG_FILE"

echo "### 🐛 Bug Fixes" >> "$CHANGELOG_FILE"
git log "$COMMIT_RANGE" --pretty=format:"- %s (%h)" --grep="^fix" >> "$CHANGELOG_FILE" 2>/dev/null || true
echo "" >> "$CHANGELOG_FILE"

echo "### 📚 Documentation" >> "$CHANGELOG_FILE"
git log "$COMMIT_RANGE" --pretty=format:"- %s (%h)" --grep="^docs" >> "$CHANGELOG_FILE" 2>/dev/null || true
echo "" >> "$CHANGELOG_FILE"

echo "### 🔧 Maintenance" >> "$CHANGELOG_FILE"
git log "$COMMIT_RANGE" --pretty=format:"- %s (%h)" --grep="^chore\|^refactor" >> "$CHANGELOG_FILE" 2>/dev/null || true
echo "" >> "$CHANGELOG_FILE"

# Ajouter les contributeurs
echo "### 👥 Contributors" >> "$CHANGELOG_FILE"
git log "$COMMIT_RANGE" --pretty=format:"%an" | sort | uniq | while read -r author; do
    echo "- @$author" >> "$CHANGELOG_FILE"
done

# Ajouter les stats
COMMIT_COUNT=$(git rev-list --count "$COMMIT_RANGE" 2>/dev/null || echo "0")
FILES_CHANGED=$(git diff --name-only "$COMMIT_RANGE" 2>/dev/null | wc -l || echo "0")

cat >> "$CHANGELOG_FILE" << EOF

### 📊 Statistics
- **Commits:** $COMMIT_COUNT
- **Files changed:** $FILES_CHANGED
- **Release type:** $RELEASE_TYPE

EOF

echo "✅ Changelog généré: $CHANGELOG_FILE"

# === Étape 4 : Mise à jour de la version ===
echo "🔢 Mise à jour de la version..."

if [ -f "package.json" ]; then
    execute "npm version $RELEASE_TYPE --no-git-tag-version"
fi

# Créer le commit de version
execute "git add ."
execute "git commit -m 'chore: bump version to $VERSION'"

# === Étape 5 : Création du tag ===
echo "🏷️  Création du tag..."

execute "git tag -a $VERSION -m 'Release $VERSION'"

# === Étape 6 : Push des changements ===
echo "📤 Push des changements..."

execute "git push origin main"
execute "git push origin $VERSION"

# === Étape 7 : Création de la release GitHub ===
echo "🚀 Création de la release GitHub..."

CHANGELOG_CONTENT=$(cat "$CHANGELOG_FILE")

execute "gh release create $VERSION \
    --title 'Release $VERSION' \
    --notes '$CHANGELOG_CONTENT' \
    --latest"

# === Étape 8 : Upload des artifacts ===
echo "📦 Upload des artifacts..."

if [ -d "dist" ]; then
    execute "tar -czf release-$VERSION.tar.gz dist/"
    execute "gh release upload $VERSION release-$VERSION.tar.gz"
fi

# === Étape 9 : Déploiement automatique ===
echo "🚀 Déploiement automatique..."

case "$RELEASE_TYPE" in
    "patch")
        execute "./scripts/deploy.sh staging"
        ;;
    "minor"|"major")
        execute "./scripts/deploy.sh production"
        ;;
esac

# === Étape 10 : Notifications post-release ===
echo "📢 Notifications post-release..."

# Créer une issue de suivi post-release
POST_RELEASE_ISSUE=$(execute "gh issue create \
    --title '📋 Post-release tasks for $VERSION' \
    --body '# Post-release checklist for $VERSION

## Immediate tasks
- [ ] Verify deployment is successful
- [ ] Check monitoring dashboards
- [ ] Update documentation if needed
- [ ] Announce release to team

## Follow-up tasks
- [ ] Monitor error rates for 24h
- [ ] Collect user feedback
- [ ] Plan next iteration

## Release info
- **Version:** $VERSION
- **Type:** $RELEASE_TYPE
- **Release Notes:** https://github.com/$GITHUB_REPOSITORY/releases/tag/$VERSION
- **Deployed:** $(date)

## Issues to monitor
- Check for any user reports
- Monitor performance metrics
- Watch for regression reports' \
    --label 'post-release,monitoring' \
    --assignee '@release-manager'")

# Créer une issue d'annonce
execute "gh issue create \
    --title '🎉 $VERSION released!' \
    --body '# 🚀 New Release: $VERSION

We'\''re excited to announce the release of version $VERSION!

## What'\''s new
$CHANGELOG_CONTENT

## How to update
\`\`\`bash
npm update your-package-name
\`\`\`

## Documentation
- [Release Notes](https://github.com/$GITHUB_REPOSITORY/releases/tag/$VERSION)
- [Migration Guide](./docs/migration.md)
- [API Documentation](./docs/api.md)

## Feedback
We'\''d love to hear your thoughts! Please share your feedback in the comments below.

Happy coding! 🎉' \
    --label 'announcement,release'"

# === Étape 11 : Nettoyage ===
echo "🧹 Nettoyage..."

execute "rm -f $CHANGELOG_FILE release-$VERSION.tar.gz"

# === Résumé final ===
echo ""
echo "🎉 Release $VERSION créée avec succès!"
echo ""
echo "📋 Résumé des actions:"
echo "   ✅ Tests complets exécutés"
echo "   ✅ Changelog généré automatiquement"
echo "   ✅ Version mise à jour dans package.json"
echo "   ✅ Tag $VERSION créé et pushé"
echo "   ✅ Release GitHub créée"
echo "   ✅ Artifacts uploadés"
echo "   ✅ Déploiement automatique lancé"
echo "   ✅ Issues de suivi créées"
echo ""
echo "🔗 Liens utiles:"
echo "   📦 Release: https://github.com/$GITHUB_REPOSITORY/releases/tag/$VERSION"
echo "   📋 Post-release tasks: Issue #$POST_RELEASE_ISSUE"
echo ""
echo "🔧 Prochaines étapes:"
echo "   1. Vérifier le déploiement"
echo "   2. Surveiller les métriques"
echo "   3. Annoncer à l'équipe"
```

## Workflow 3 : Gestion automatique des issues

### Problème résolu
Les issues s'accumulent, certaines deviennent obsolètes, d'autres ont besoin de follow-up. La gestion manuelle est fastidieuse et on peut oublier des issues importantes.

### Solution automatisée

```bash
#!/bin/bash
# manage-issues.sh - Gestion automatique des issues

set -e

echo "📋 Gestion automatique des issues"

# === Configuration ===
REPO="${GITHUB_REPOSITORY:-$(gh repo view --json nameWithOwner --jq .nameWithOwner)}"
STALE_DAYS=30
INACTIVE_DAYS=7

echo "📁 Repository: $REPO"

# === Fonction utilitaire ===
days_since() {
    local date="$1"
    local current_timestamp=$(date +%s)
    local date_timestamp=$(date -d "$date" +%s)
    echo $(( (current_timestamp - date_timestamp) / 86400 ))
}

# === Étape 1 : Trier les issues par catégorie ===
echo "🔍 Analyse des issues ouvertes..."

ALL_ISSUES=$(gh issue list --repo "$REPO" --limit 100 --json number,title,author,createdAt,updatedAt,labels,assignees,comments)
TOTAL_ISSUES=$(echo "$ALL_ISSUES" | jq length)

echo "📊 $TOTAL_ISSUES issues ouvertes trouvées"

# === Étape 2 : Détecter les issues obsolètes ===
echo "⏰ Détection des issues obsolètes..."

echo "$ALL_ISSUES" | jq -r '.[] | @base64' | while read -r issue_data; do
    ISSUE_INFO=$(echo "$issue_data" | base64 --decode)

    NUMBER=$(echo "$ISSUE_INFO" | jq -r '.number')
    TITLE=$(echo "$ISSUE_INFO" | jq -r '.title')
    UPDATED_AT=$(echo "$ISSUE_INFO" | jq -r '.updatedAt')
    LABELS=$(echo "$ISSUE_INFO" | jq -r '.labels[].name' | tr '\n' ',' | sed 's/,$//')
    ASSIGNEES=$(echo "$ISSUE_INFO" | jq -r '.assignees[].login' | tr '\n' ',' | sed 's/,$//')

    # Calculer les jours depuis la dernière mise à jour
    DAYS_INACTIVE=$(days_since "$UPDATED_AT")

    # Vérifier si l'issue est considérée comme obsolète
    if [ $DAYS_INACTIVE -gt $STALE_DAYS ]; then
        echo "⚠️  Issue #$NUMBER obsolète ($DAYS_INACTIVE jours): $TITLE"

        # Vérifier si déjà marquée comme stale
        if ! echo "$LABELS" | grep -q "stale"; then
            echo "  🏷️  Marquage comme obsolète..."

            gh issue comment "$NUMBER" --repo "$REPO" \
                --body "⏰ **Issue marquée comme obsolète**

Cette issue n'a pas eu d'activité depuis $DAYS_INACTIVE jours.

**Actions possibles:**
- Si encore pertinente, ajoutez un commentaire pour la réactiver
- Si résolue, fermez l'issue
- Si plus d'actualité, elle sera fermée automatiquement dans 7 jours

Étiquette \`stale\` ajoutée automatiquement."

            gh issue edit "$NUMBER" --repo "$REPO" --add-label "stale"
        else
            # Si déjà stale depuis 7 jours, fermer automatiquement
            if [ $DAYS_INACTIVE -gt $((STALE_DAYS + INACTIVE_DAYS)) ]; then
                echo "  🔒 Fermeture automatique..."

                gh issue comment "$NUMBER" --repo "$REPO" \
                    --body "🤖 **Issue fermée automatiquement**

Cette issue était marquée comme obsolète et n'a eu aucune activité depuis $DAYS_INACTIVE jours.

Si cette issue est encore pertinente, n'hésitez pas à la rouvrir avec des informations à jour."

                gh issue close "$NUMBER" --repo "$REPO" --reason "not planned"
            fi
        fi
    fi
done

# === Étape 3 : Gérer les issues sans assigné ===
echo "👤 Gestion des issues sans assigné..."

UNASSIGNED_ISSUES=$(echo "$ALL_ISSUES" | jq '[.[] | select(.assignees | length == 0)]')
UNASSIGNED_COUNT=$(echo "$UNASSIGNED_ISSUES" | jq length)

if [ $UNASSIGNED_COUNT -gt 0 ]; then
    echo "📋 $UNASSIGNED_COUNT issues sans assigné trouvées"

    # Créer un résumé pour l'équipe
    UNASSIGNED_LIST=""
    echo "$UNASSIGNED_ISSUES" | jq -r '.[] | "- #\(.number): \(.title)"' | while read -r line; do
        UNASSIGNED_LIST+="$line\n"
    done

    # Créer une issue de suivi
    gh issue create --repo "$REPO" \
        --title "📋 Weekly Review: Unassigned Issues ($UNASSIGNED_COUNT)" \
        --body "# Unassigned Issues Review

Il y a actuellement **$UNASSIGNED_COUNT issues non assignées** qui nécessitent de l'attention.

## Issues à assigner

$(echo "$UNASSIGNED_ISSUES" | jq -r '.[] | "- #\(.number): \(.title) (créée le \(.createdAt | split("T")[0]))"')

## Actions recommandées

1. **Triez** chaque issue par priorité
2. **Assignez** aux membres appropriés de l'équipe
3. **Labellisez** avec les étiquettes pertinentes
4. **Fermez** les issues obsolètes ou dupliquées

## Prochaine review

Cette review sera automatiquement régénérée chaque semaine.

---
*Issue générée automatiquement par manage-issues.sh*" \
        --label "housekeeping,triage" \
        --assignee "@team-leads"
fi

# === Étape 4 : Analyser les issues bloquées ===
echo "🚫 Analyse des issues bloquées..."

BLOCKED_ISSUES=$(echo "$ALL_ISSUES" | jq '[.[] | select(.labels[].name == "blocked")]')
BLOCKED_COUNT=$(echo "$BLOCKED_ISSUES" | jq length)

if [ $BLOCKED_COUNT -gt 0 ]; then
    echo "🚫 $BLOCKED_COUNT issues bloquées trouvées"

    echo "$BLOCKED_ISSUES" | jq -r '.[] | @base64' | while read -r issue_data; do
        ISSUE_INFO=$(echo "$issue_data" | base64 --decode)

        NUMBER=$(echo "$ISSUE_INFO" | jq -r '.number')
        TITLE=$(echo "$ISSUE_INFO" | jq -r '.title')
        UPDATED_AT=$(echo "$ISSUE_INFO" | jq -r '.updatedAt')
        ASSIGNEES=$(echo "$ISSUE_INFO" | jq -r '.assignees[].login' | tr '\n' ',' | sed 's/,$//')

        DAYS_BLOCKED=$(days_since "$UPDATED_AT")

        if [ $DAYS_BLOCKED -gt 7 ]; then
            echo "  ⚠️  Issue #$NUMBER bloquée depuis $DAYS_BLOCKED jours"

            gh issue comment "$NUMBER" --repo "$REPO" \
                --body "🚫 **Issue bloquée - Rappel automatique**

Cette issue est marquée comme bloquée depuis $DAYS_BLOCKED jours.

**Actions recommandées:**
- Mise à jour du statut du blocage
- Escalade si nécessaire
- Recherche de solutions alternatives

**Assignés:** ${ASSIGNEES:-Aucun}

Merci de faire le point sur cette issue."
        fi
    done
fi

# === Étape 5 : Créer des métriques hebdomadaires ===
echo "📊 Génération des métriques..."

# Calculer les statistiques
TOTAL_OPEN=$(echo "$ALL_ISSUES" | jq length)
NEW_THIS_WEEK=$(echo "$ALL_ISSUES" | jq '[.[] | select(.createdAt > (now - 604800 | strftime("%Y-%m-%dT%H:%M:%SZ")))] | length')
UPDATED_THIS_WEEK=$(echo "$ALL_ISSUES" | jq '[.[] | select(.updatedAt > (now - 604800 | strftime("%Y-%m-%dT%H:%M:%SZ")))] | length')

# Issues par label
BUG_COUNT=$(echo "$ALL_ISSUES" | jq '[.[] | select(.labels[].name == "bug")] | length')
FEATURE_COUNT=$(echo "$ALL_ISSUES" | jq '[.[] | select(.labels[].name == "enhancement")] | length')
DOCS_COUNT=$(echo "$ALL_ISSUES" | jq '[.[] | select(.labels[].name == "documentation")] | length')

# Créer un rapport hebdomadaire
CURRENT_WEEK=$(date +"%Y-W%U")

gh issue create --repo "$REPO" \
    --title "📊 Weekly Issues Report - $CURRENT_WEEK" \
    --body "# Weekly Issues Report

**Semaine:** $CURRENT_WEEK
**Date:** $(date +"%Y-%m-%d")

## 📈 Statistiques globales

- **Total issues ouvertes:** $TOTAL_OPEN
- **Nouvelles cette semaine:** $NEW_THIS_WEEK
- **Mises à jour cette semaine:** $UPDATED_THIS_WEEK

## 🏷️ Répartition par type

- **🐛 Bugs:** $BUG_COUNT
- **✨ Features:** $FEATURE_COUNT
- **📚 Documentation:** $DOCS_COUNT
- **🚫 Bloquées:** $BLOCKED_COUNT
- **👤 Non assignées:** $UNASSIGNED_COUNT

## ⚠️ Points d'attention

- **Issues obsolètes détectées:** Marquées automatiquement avec \`stale\`
- **Issues bloquées:** $BLOCKED_COUNT issues nécessitent une attention
- **Issues sans assigné:** $UNASSIGNED_COUNT issues à traiter

## 🎯 Actions recommandées

1. Review des issues obsolètes marquées \`stale\`
2. Déblocage des issues en attente
3. Assignment des issues sans propriétaire
4. Priorisation des nouvelles issues

---
*Rapport généré automatiquement par manage-issues.sh*" \
    --label "metrics,weekly-report" \
    --assignee "@project-managers"

echo "✅ Gestion automatique des issues terminée"
echo ""
echo "📊 Résumé:"
echo "   📋 Issues analysées: $TOTAL_OPEN"
echo "   ⏰ Issues obsolètes détectées et marquées"
echo "   👤 Issues sans assigné: $UNASSIGNED_COUNT"
echo "   🚫 Issues bloquées: $BLOCKED_COUNT"
echo "   📊 Rapport hebdomadaire créé"
```

## Workflow 4 : Synchronisation multi-repositories

### Problème résolu
Dans les organisations avec plusieurs repositories, il faut synchroniser les configurations, les templates, les workflows GitHub Actions, etc. Faire cela manuellement pour chaque repo est fastidieux.

### Solution automatisée

```bash
#!/bin/bash
# sync-repositories.sh - Synchronisation multi-repositories

set -e

echo "🔄 Synchronisation multi-repositories"

# === Configuration ===
MASTER_REPO="$1"
TARGET_REPOS="${2:-all}"
CONFIG_FILES=(".github/workflows/ci.yml" ".github/ISSUE_TEMPLATE/" ".github/pull_request_template.md" ".gitignore" "LICENSE")

if [ -z "$MASTER_REPO" ]; then
    echo "❌ Usage: $0 <master-repo> [target-repos|all]"
    echo "   Exemple: $0 org/template-repo org/repo1,org/repo2"
    echo "   Exemple: $0 org/template-repo all"
    exit 1
fi

echo "📋 Repository maître: $MASTER_REPO"

# === Étape 1 : Récupérer la liste des repositories ===
if [ "$TARGET_REPOS" = "all" ]; then
    echo "🔍 Récupération de tous les repositories de l'organisation..."

    ORG=$(echo "$MASTER_REPO" | cut -d'/' -f1)
    TARGET_REPOS=$(gh repo list "$ORG" --limit 100 --json name --jq '.[].name' | grep -v "$(echo "$MASTER_REPO" | cut -d'/' -f2)" | tr '\n' ',' | sed 's/,$//')
fi

IFS=',' read -ra REPOS <<< "$TARGET_REPOS"
echo "🎯 Repositories cibles: ${#REPOS[@]} repositories"

# === Étape 2 : Cloner le repository maître temporairement ===
TEMP_DIR="/tmp/sync-repos-$(date +%s)"
mkdir -p "$TEMP_DIR"
cd "$TEMP_DIR"

echo "📁 Clonage du repository maître..."
gh repo clone "$MASTER_REPO" master-repo
cd master-repo

# === Étape 3 : Extraire les fichiers de configuration ===
echo "📋 Extraction des fichiers de configuration..."

CONFIG_DIR="../config-files"
mkdir -p "$CONFIG_DIR"

for file in "${CONFIG_FILES[@]}"; do
    if [ -e "$file" ]; then
        echo "  📄 Extraction: $file"

        # Créer le dossier parent si nécessaire
        mkdir -p "$CONFIG_DIR/$(dirname "$file")"

        # Copier le fichier ou dossier
        cp -r "$file" "$CONFIG_DIR/$file"
    else
        echo "  ⚠️  Fichier non trouvé: $file"
    fi
done

cd ..

# === Étape 4 : Synchroniser chaque repository ===
echo "🔄 Synchronisation des repositories..."

for repo_name in "${REPOS[@]}"; do
    if [ -z "$repo_name" ]; then
        continue
    fi

    ORG=$(echo "$MASTER_REPO" | cut -d'/' -f1)
    FULL_REPO="$ORG/$repo_name"

    echo ""
    echo "🔄 Synchronisation de $FULL_REPO..."

    # Cloner le repository cible
    if gh repo clone "$FULL_REPO" "target-$repo_name" 2>/dev/null; then
        cd "target-$repo_name"

        # Créer une branche pour les changements
        SYNC_BRANCH="sync/config-$(date +%Y%m%d)"
        git checkout -b "$SYNC_BRANCH"

        CHANGES_MADE=false

        # Copier chaque fichier de configuration
        for file in "${CONFIG_FILES[@]}"; do
            if [ -e "../config-files/$file" ]; then
                echo "  📄 Synchronisation: $file"

                # Créer le dossier parent si nécessaire
                mkdir -p "$(dirname "$file")"

                # Copier et vérifier s'il y a des changements
                if ! cmp -s "../config-files/$file" "$file" 2>/dev/null; then
                    cp -r "../config-files/$file" "$file"
                    git add "$file"
                    CHANGES_MADE=true
                    echo "    ✅ Fichier mis à jour"
                else
                    echo "    ℹ️  Fichier déjà à jour"
                fi
            fi
        done

        # Commiter et pusher si il y a des changements
        if [ "$CHANGES_MADE" = true ]; then
            git commit -m "sync: update configuration files from $MASTER_REPO

Files synchronized:
$(printf '%s\n' "${CONFIG_FILES[@]}" | sed 's/^/- /')"

            git push origin "$SYNC_BRANCH"

            # Créer une Pull Request
            PR_NUMBER=$(gh pr create \
                --title "🔄 Sync configuration from $MASTER_REPO" \
                --body "# Configuration Sync

This PR synchronizes configuration files from the master repository: \`$MASTER_REPO\`

## Files updated

$(printf '%s\n' "${CONFIG_FILES[@]}" | sed 's/^/- /')

## Changes

This is an automated sync to ensure consistency across all repositories in the organization.

## Review checklist

- [ ] Verify that workflows are compatible with this repository
- [ ] Check that issue templates are appropriate
- [ ] Ensure licensing is correct
- [ ] Test CI/CD workflows if applicable

---
*This PR was created automatically by sync-repositories.sh*" \
                --label "sync,automation" \
                --assignee "@devops-team")

            echo "    ✅ PR créée: #$PR_NUMBER"

            # Auto-merge si le repository a cette option activée
            if gh api "repos/$FULL_REPO" --jq .allow_auto_merge | grep -q "true"; then
                gh pr merge "$PR_NUMBER" --auto --squash
                echo "    🤖 Auto-merge activé"
            fi
        else
            echo "    ℹ️  Aucun changement nécessaire"
            git checkout main
            git branch -D "$SYNC_BRANCH"
        fi

        cd ..
    else
        echo "  ❌ Impossible de cloner $FULL_REPO"
    fi
done

# === Étape 5 : Générer un rapport de synchronisation ===
echo ""
echo "📊 Génération du rapport de synchronisation..."

SYNC_REPORT="sync-report-$(date +%Y%m%d-%H%M%S).md"

cat > "$SYNC_REPORT" << EOF
# Repository Synchronization Report

**Date:** $(date)
**Master Repository:** $MASTER_REPO
**Repositories Processed:** ${#REPOS[@]}

## Configuration Files Synchronized

$(printf '%s\n' "${CONFIG_FILES[@]}" | sed 's/^/- /')

## Repositories Status

EOF

# Ajouter le statut de chaque repository
for repo_name in "${REPOS[@]}"; do
    if [ -z "$repo_name" ]; then
        continue
    fi

    ORG=$(echo "$MASTER_REPO" | cut -d'/' -f1)
    FULL_REPO="$ORG/$repo_name"

    if [ -d "target-$repo_name" ]; then
        cd "target-$repo_name"

        # Vérifier s'il y a une PR ouverte
        OPEN_PRS=$(gh pr list --label "sync" --json number --jq length 2>/dev/null || echo "0")

        if [ "$OPEN_PRS" -gt 0 ]; then
            echo "- ✅ **$FULL_REPO** - PR créée pour synchronisation" >> "../$SYNC_REPORT"
        else
            echo "- ℹ️  **$FULL_REPO** - Aucun changement nécessaire" >> "../$SYNC_REPORT"
        fi

        cd ..
    else
        echo "- ❌ **$FULL_REPO** - Erreur lors du clonage" >> "../$SYNC_REPORT"
    fi
done

cat >> "$SYNC_REPORT" << EOF

## Next Steps

1. Review and merge the created Pull Requests
2. Test the updated workflows in each repository
3. Update repository-specific configurations if needed
4. Schedule next synchronization run

---
*Report generated by sync-repositories.sh*
EOF

echo "📄 Rapport généré: $SYNC_REPORT"

# === Étape 6 : Créer une issue de suivi ===
gh issue create --repo "$MASTER_REPO" \
    --title "🔄 Multi-repository sync completed - $(date +%Y-%m-%d)" \
    --body "# Multi-Repository Synchronization Report

$(cat "$SYNC_REPORT")

## Action Required

Please review and merge the Pull Requests created in the target repositories.

**Configuration Files:** $(printf '%s, ' "${CONFIG_FILES[@]}" | sed 's/, $//')
**Repositories Processed:** ${#REPOS[@]}

Use the \`sync\` label to find all related PRs across repositories." \
    --label "sync,report" \
    --assignee "@devops-team"

# === Nettoyage ===
cd /
rm -rf "$TEMP_DIR"

echo ""
echo "✅ Synchronisation multi-repositories terminée!"
echo "📄 Rapport: $SYNC_REPORT"
echo "📋 Issue de suivi créée dans $MASTER_REPO"
```

## Workflow 5 : Monitoring de santé des projets

### Problème résolu
Il est difficile de garder une vue d'ensemble sur la santé de tous les projets : quels projets ont des tests qui échouent, des dépendances obsolètes, des PRs en attente, etc.

### Solution automatisée

```bash
#!/bin/bash
# project-health-monitor.sh - Monitoring de santé des projets

set -e

echo "🏥 Monitoring de santé des projets"

# === Configuration ===
ORG="${1:-$(gh api user --jq .login)}"
HEALTH_REPO="${2:-$ORG/project-health}"
MIN_SCORE=70  # Score minimum acceptable

echo "🏢 Organisation: $ORG"
echo "📊 Repository de santé: $HEALTH_REPO"

# === Étape 1 : Récupérer tous les repositories ===
echo "🔍 Récupération des repositories..."

REPOS=$(gh repo list "$ORG" --limit 100 --json name,defaultBranch,pushedAt,isPrivate --jq '.[] | select(.pushedAt > (now - 2592000)) | .name')
REPO_COUNT=$(echo "$REPOS" | wc -l)

echo "📋 $REPO_COUNT repositories actifs trouvés"

# === Fonction de calcul de score de santé ===
calculate_health_score() {
    local repo="$1"
    local score=100
    local issues=()

    echo "🔍 Analyse de $repo..."

    # Vérifier les tests CI/CD (GitHub Actions)
    echo "  🧪 Vérification des tests..."
    LATEST_RUN=$(gh run list --repo "$ORG/$repo" --limit 1 --json status,conclusion,workflowName 2>/dev/null || echo "[]")

    if [ "$LATEST_RUN" != "[]" ]; then
        RUN_STATUS=$(echo "$LATEST_RUN" | jq -r '.[0].status // "unknown"')
        RUN_CONCLUSION=$(echo "$LATEST_RUN" | jq -r '.[0].conclusion // "unknown"')

        if [ "$RUN_STATUS" = "completed" ] && [ "$RUN_CONCLUSION" = "failure" ]; then
            score=$((score - 20))
            issues+=("❌ Tests échoués")
        elif [ "$RUN_STATUS" = "in_progress" ]; then
            issues+=("⏳ Tests en cours")
        else
            issues+=("✅ Tests OK")
        fi
    else
        score=$((score - 15))
        issues+=("⚠️ Aucun workflow CI/CD")
    fi

    # Vérifier les PRs en attente
    echo "  📝 Vérification des PRs..."
    OPEN_PRS=$(gh pr list --repo "$ORG/$repo" --json number,createdAt 2>/dev/null || echo "[]")
    PR_COUNT=$(echo "$OPEN_PRS" | jq length)

    if [ "$PR_COUNT" -gt 10 ]; then
        score=$((score - 15))
        issues+=("⚠️ Beaucoup de PRs ouvertes ($PR_COUNT)")
    elif [ "$PR_COUNT" -gt 5 ]; then
        score=$((score - 5))
        issues+=("⚠️ Plusieurs PRs ouvertes ($PR_COUNT)")
    else
        issues+=("✅ PRs sous contrôle ($PR_COUNT)")
    fi

    # Vérifier les PRs anciennes
    OLD_PRS=$(echo "$OPEN_PRS" | jq '[.[] | select(.createdAt < (now - 604800 | strftime("%Y-%m-%dT%H:%M:%SZ")))] | length')
    if [ "$OLD_PRS" -gt 0 ]; then
        score=$((score - 10))
        issues+=("⏰ $OLD_PRS PRs anciennes (>7 jours)")
    fi

    # Vérifier les issues ouvertes
    echo "  📋 Vérification des issues..."
    OPEN_ISSUES=$(gh issue list --repo "$ORG/$repo" --json number,labels 2>/dev/null || echo "[]")
    ISSUE_COUNT=$(echo "$OPEN_ISSUES" | jq length)
    BUG_COUNT=$(echo "$OPEN_ISSUES" | jq '[.[] | select(.labels[].name == "bug")] | length')

    if [ "$BUG_COUNT" -gt 5 ]; then
        score=$((score - 15))
        issues+=("🐛 Beaucoup de bugs ouverts ($BUG_COUNT)")
    elif [ "$BUG_COUNT" -gt 0 ]; then
        issues+=("🐛 $BUG_COUNT bugs ouverts")
    fi

    if [ "$ISSUE_COUNT" -gt 20 ]; then
        score=$((score - 10))
        issues+=("📋 Beaucoup d'issues ouvertes ($ISSUE_COUNT)")
    fi

    # Vérifier la fraîcheur des commits
    echo "  🕐 Vérification de l'activité..."
    LAST_COMMIT=$(gh api "repos/$ORG/$repo/commits" --jq '.[0].commit.author.date' 2>/dev/null || echo "")

    if [ -n "$LAST_COMMIT" ]; then
        DAYS_SINCE_COMMIT=$(( ($(date +%s) - $(date -d "$LAST_COMMIT" +%s)) / 86400 ))

        if [ "$DAYS_SINCE_COMMIT" -gt 30 ]; then
            score=$((score - 10))
            issues+=("⏰ Inactif depuis $DAYS_SINCE_COMMIT jours")
        elif [ "$DAYS_SINCE_COMMIT" -gt 7 ]; then
            issues+=("⏰ Dernière activité: $DAYS_SINCE_COMMIT jours")
        else
            issues+=("✅ Activité récente")
        fi
    fi

    # Vérifier la documentation
    echo "  📚 Vérification de la documentation..."
    if gh api "repos/$ORG/$repo/contents/README.md" >/dev/null 2>&1; then
        issues+=("✅ README présent")
    else
        score=$((score - 10))
        issues+=("❌ README manquant")
    fi

    # Vérifier les dépendances (pour les projets Node.js)
    if gh api "repos/$ORG/$repo/contents/package.json" >/dev/null 2>&1; then
        echo "  📦 Vérification des dépendances..."
        # Note: Dans un vrai script, on vérifierait les dépendances obsolètes
        issues+=("📦 Projet Node.js détecté")
    fi

    # Limiter le score entre 0 et 100
    if [ "$score" -lt 0 ]; then
        score=0
    fi

    # Retourner le score et les issues
    echo "$score|$(IFS=';'; echo "${issues[*]}")"
}

# === Étape 2 : Analyser chaque repository ===
echo ""
echo "🔍 Analyse de santé des repositories..."

HEALTH_DATA=""
CRITICAL_REPOS=()
WARNING_REPOS=()
HEALTHY_REPOS=()

echo "$REPOS" | while read -r repo; do
    if [ -n "$repo" ]; then
        RESULT=$(calculate_health_score "$repo")
        SCORE=$(echo "$RESULT" | cut -d'|' -f1)
        ISSUES=$(echo "$RESULT" | cut -d'|' -f2)

        echo "  📊 $repo: Score $SCORE/100"

        # Classer le repository
        if [ "$SCORE" -lt 50 ]; then
            echo "$repo|$SCORE|$ISSUES|critical" >> "/tmp/health-data.csv"
        elif [ "$SCORE" -lt "$MIN_SCORE" ]; then
            echo "$repo|$SCORE|$ISSUES|warning" >> "/tmp/health-data.csv"
        else
            echo "$repo|$SCORE|$ISSUES|healthy" >> "/tmp/health-data.csv"
        fi
    fi
done

# === Étape 3 : Générer le rapport de santé ===
echo ""
echo "📊 Génération du rapport de santé..."

HEALTH_REPORT="health-report-$(date +%Y%m%d).md"

cat > "$HEALTH_REPORT" << EOF
# Project Health Report

**Date:** $(date)
**Organization:** $ORG
**Repositories Analyzed:** $(cat /tmp/health-data.csv | wc -l)

## Summary

EOF

# Calculer les statistiques
CRITICAL_COUNT=$(grep "|critical$" /tmp/health-data.csv | wc -l)
WARNING_COUNT=$(grep "|warning$" /tmp/health-data.csv | wc -l)
HEALTHY_COUNT=$(grep "|healthy$" /tmp/health-data.csv | wc -l)
AVERAGE_SCORE=$(awk -F'|' '{sum+=$2; count++} END {printf "%.1f", sum/count}' /tmp/health-data.csv)

cat >> "$HEALTH_REPORT" << EOF
- 🎯 **Average Score:** $AVERAGE_SCORE/100
- ✅ **Healthy:** $HEALTHY_COUNT repositories (score ≥ $MIN_SCORE)
- ⚠️ **Warning:** $WARNING_COUNT repositories (score 50-$(($MIN_SCORE-1)))
- 🚨 **Critical:** $CRITICAL_COUNT repositories (score < 50)

## Critical Issues (Immediate Attention Required)

EOF

if [ "$CRITICAL_COUNT" -gt 0 ]; then
    grep "|critical$" /tmp/health-data.csv | while IFS='|' read -r repo score issues status; do
        echo "### 🚨 $repo (Score: $score/100)" >> "$HEALTH_REPORT"
        echo "" >> "$HEALTH_REPORT"
        echo "**Issues identified:**" >> "$HEALTH_REPORT"
        echo "$issues" | tr ';' '\n' | sed 's/^/- /' >> "$HEALTH_REPORT"
        echo "" >> "$HEALTH_REPORT"
    done
else
    echo "🎉 No critical issues found!" >> "$HEALTH_REPORT"
fi

cat >> "$HEALTH_REPORT" << EOF

## Warning Issues (Review Recommended)

EOF

if [ "$WARNING_COUNT" -gt 0 ]; then
    grep "|warning$" /tmp/health-data.csv | while IFS='|' read -r repo score issues status; do
        echo "### ⚠️ $repo (Score: $score/100)" >> "$HEALTH_REPORT"
        echo "" >> "$HEALTH_REPORT"
        echo "**Issues identified:**" >> "$HEALTH_REPORT"
        echo "$issues" | tr ';' '\n' | sed 's/^/- /' >> "$HEALTH_REPORT"
        echo "" >> "$HEALTH_REPORT"
    done
else
    echo "✅ No warning issues found!" >> "$HEALTH_REPORT"
fi

cat >> "$HEALTH_REPORT" << EOF

## Healthy Repositories

EOF

if [ "$HEALTHY_COUNT" -gt 0 ]; then
    grep "|healthy$" /tmp/health-data.csv | while IFS='|' read -r repo score issues status; do
        echo "- ✅ **$repo** (Score: $score/100)" >> "$HEALTH_REPORT"
    done
else
    echo "No repositories meet the healthy threshold." >> "$HEALTH_REPORT"
fi

cat >> "$HEALTH_REPORT" << EOF

## Recommendations

### For Critical Repositories
1. **Immediate action required** - Fix failing tests
2. **Review and merge** outstanding PRs
3. **Address critical bugs** immediately
4. **Update documentation** if missing

### For Warning Repositories
1. **Schedule maintenance** time to address issues
2. **Review dependency updates**
3. **Clean up** old issues and PRs
4. **Improve CI/CD** workflows

### General Recommendations
- Set up automated dependency updates
- Implement stricter PR review processes
- Regular health check automation
- Team training on best practices

---
*Report generated automatically by project-health-monitor.sh*
EOF

# === Étape 4 : Créer des issues pour les problèmes critiques ===
echo "🚨 Création d'issues pour les problèmes critiques..."

if [ "$CRITICAL_COUNT" -gt 0 ]; then
    grep "|critical$" /tmp/health-data.csv | while IFS='|' read -r repo score issues status; do
        echo "  🚨 Création d'issue pour $repo (score: $score)"

        gh issue create --repo "$ORG/$repo" \
            --title "🚨 Critical Health Issues Detected (Score: $score/100)" \
            --body "# Repository Health Alert

This repository has been flagged with critical health issues requiring immediate attention.

## Health Score: $score/100 🚨

## Issues Identified

$(echo "$issues" | tr ';' '\n' | sed 's/^/- /')

## Recommended Actions

1. **Fix failing tests** - Ensure CI/CD pipeline is green
2. **Address bugs** - Review and fix open bug reports
3. **Clean up PRs** - Review, merge, or close outstanding PRs
4. **Update documentation** - Ensure README and docs are current
5. **Review dependencies** - Update outdated packages

## Resources

- [Project Health Report](https://github.com/$HEALTH_REPO)
- [Best Practices Guide](https://docs.github.com/en/repositories)
- [CI/CD Troubleshooting](internal-docs-link)

This issue was created automatically. Please address these issues and re-run the health check.

---
*Created by project-health-monitor.sh*" \
            --label "health-check,critical,urgent" \
            --assignee "@repository-maintainers"
    done
fi

# === Étape 5 : Publier le rapport ===
echo "📊 Publication du rapport..."

# Créer ou mettre à jour le repository de santé
if ! gh repo view "$HEALTH_REPO" >/dev/null 2>&1; then
    echo "📁 Création du repository de santé..."
    gh repo create "$HEALTH_REPO" --public --description "Automated project health monitoring"
fi

# Cloner et mettre à jour
TEMP_HEALTH_DIR="/tmp/health-repo-$(date +%s)"
gh repo clone "$HEALTH_REPO" "$TEMP_HEALTH_DIR"
cd "$TEMP_HEALTH_DIR"

# Copier le rapport
cp "../$HEALTH_REPORT" "reports/"
mkdir -p reports
cp "../$HEALTH_REPORT" "reports/"

# Mettre à jour le README principal
cat > README.md << EOF
# Project Health Dashboard

This repository tracks the health of all projects in the **$ORG** organization.

## Latest Report

📊 **[Latest Health Report](reports/$(basename "$HEALTH_REPORT"))**

## Summary ($(date +%Y-%m-%d))

- 🎯 **Average Score:** $AVERAGE_SCORE/100
- ✅ **Healthy Repositories:** $HEALTHY_COUNT
- ⚠️ **Warning Repositories:** $WARNING_COUNT
- 🚨 **Critical Repositories:** $CRITICAL_COUNT

## Critical Attention Required

$(if [ "$CRITICAL_COUNT" -gt 0 ]; then
    grep "|critical$" /tmp/health-data.csv | while IFS='|' read -r repo score issues status; do
        echo "- 🚨 **$repo** (Score: $score/100)"
    done
else
    echo "🎉 No critical issues!"
fi)

## Health Monitoring

This dashboard is updated automatically via \`project-health-monitor.sh\`.

**Monitoring Criteria:**
- CI/CD Test Status
- Pull Request Management
- Issue Tracking
- Code Activity
- Documentation Completeness

**Thresholds:**
- Healthy: ≥ $MIN_SCORE points
- Warning: 50-$(($MIN_SCORE-1)) points
- Critical: < 50 points

---
*Last updated: $(date)*
EOF

# Commiter et pusher
git add .
git commit -m "chore: update health report $(date +%Y-%m-%d)

- Analyzed $REPO_COUNT repositories
- Average score: $AVERAGE_SCORE/100
- Critical issues: $CRITICAL_COUNT repositories
- Warning issues: $WARNING_COUNT repositories"

git push origin main

cd ..
rm -rf "$TEMP_HEALTH_DIR"

# === Nettoyage ===
rm -f /tmp/health-data.csv

echo ""
echo "✅ Monitoring de santé terminé!"
echo ""
echo "📊 Résultats:"
echo "   📋 Repositories analysés: $REPO_COUNT"
echo "   🎯 Score moyen: $AVERAGE_SCORE/100"
echo "   ✅ Repositories sains: $HEALTHY_COUNT"
echo "   ⚠️  Repositories en warning: $WARNING_COUNT"
echo "   🚨 Repositories critiques: $CRITICAL_COUNT"
echo ""
echo "📄 Rapport complet: $HEALTH_REPORT"
echo "🔗 Dashboard: https://github.com/$HEALTH_REPO"

if [ "$CRITICAL_COUNT" -gt 0 ]; then
    echo ""
    echo "🚨 ATTENTION: $CRITICAL_COUNT repositories nécessitent une intervention immédiate!"
fi
```

## Bonnes pratiques pour workflows automatisés

### 1. Planification et timing

```bash
#!/bin/bash
# Schedule automatisé avec cron

# === Exemple de configuration crontab ===
#
# # Onboarding: Vérifie les nouveaux membres chaque lundi à 9h
# 0 9 * * 1 /path/to/onboard-check.sh
#
# # Release: Vérifie les releases en attente chaque vendredi à 16h
# 0 16 * * 5 /path/to/release-check.sh
#
# # Issues: Gestion quotidienne à 8h30
# 30 8 * * * /path/to/manage-issues.sh
#
# # Health check: Monitoring hebdomadaire le dimanche à 7h
# 0 7 * * 0 /path/to/project-health-monitor.sh
#
# # Sync repos: Synchronisation mensuelle le 1er à 2h
# 0 2 1 * * /path/to/sync-repositories.sh org/template all

# Script de configuration automatique des workflows
setup_automated_workflows() {
    echo "⏰ Configuration des workflows automatisés..."

    # Créer le dossier de logs
    mkdir -p ~/logs/github-workflows

    # Configuration crontab avec logging
    cat > ~/temp-crontab << 'EOF'
# GitHub CLI Automated Workflows
MAILTO=""
PATH=/usr/local/bin:/usr/bin:/bin

# Issues management - Daily at 8:30 AM
30 8 * * * /path/to/manage-issues.sh >> ~/logs/github-workflows/issues.log 2>&1

# Health monitoring - Weekly on Sunday at 7:00 AM
0 7 * * 0 /path/to/project-health-monitor.sh >> ~/logs/github-workflows/health.log 2>&1

# Repository sync - Monthly on 1st at 2:00 AM
0 2 1 * * /path/to/sync-repositories.sh org/template all >> ~/logs/github-workflows/sync.log 2>&1

# Onboarding check - Weekly on Monday at 9:00 AM
0 9 * * 1 /path/to/onboard-check.sh >> ~/logs/github-workflows/onboard.log 2>&1
EOF

    # Installer la crontab
    crontab ~/temp-crontab
    rm ~/temp-crontab

    echo "✅ Workflows automatisés configurés"
}
```

### 2. Gestion des erreurs et notifications

```bash
#!/bin/bash
# Système de notification robuste

# === Configuration des notifications ===
SLACK_WEBHOOK_URL="${SLACK_WEBHOOK_URL:-}"
EMAIL_RECIPIENTS="${EMAIL_RECIPIENTS:-admin@company.com}"
NOTIFICATION_LEVEL="${NOTIFICATION_LEVEL:-WARNING}"  # DEBUG, INFO, WARNING, ERROR

# === Fonction de notification ===
notify() {
    local level="$1"
    local title="$2"
    local message="$3"
    local workflow_name="${4:-Unknown Workflow}"

    # Vérifier le niveau de notification
    case "$NOTIFICATION_LEVEL" in
        "DEBUG") level_threshold=0 ;;
        "INFO") level_threshold=1 ;;
        "WARNING") level_threshold=2 ;;
        "ERROR") level_threshold=3 ;;
    esac

    case "$level" in
        "DEBUG") current_level=0 ;;
        "INFO") current_level=1 ;;
        "WARNING") current_level=2 ;;
        "ERROR") current_level=3 ;;
    esac

    # Envoyer la notification seulement si le niveau est suffisant
    if [ "$current_level" -ge "$level_threshold" ]; then

        # Notification Slack
        if [ -n "$SLACK_WEBHOOK_URL" ]; then
            notify_slack "$level" "$title" "$message" "$workflow_name"
        fi

        # Notification email (si configuré)
        if command -v mail &> /dev/null && [ -n "$EMAIL_RECIPIENTS" ]; then
            notify_email "$level" "$title" "$message" "$workflow_name"
        fi

        # Notification GitHub (toujours)
        notify_github "$level" "$title" "$message" "$workflow_name"
    fi
}

notify_slack() {
    local level="$1"
    local title="$2"
    local message="$3"
    local workflow_name="$4"

    # Couleur selon le niveau
    case "$level" in
        "ERROR") color="danger" ;;
        "WARNING") color="warning" ;;
        "INFO") color="good" ;;
        *) color="#36a64f" ;;
    esac

    # Emoji selon le niveau
    case "$level" in
        "ERROR") emoji=":x:" ;;
        "WARNING") emoji=":warning:" ;;
        "INFO") emoji=":information_source:" ;;
        *) emoji=":robot_face:" ;;
    esac

    curl -X POST \
        -H "Content-type: application/json" \
        "$SLACK_WEBHOOK_URL" \
        -d "{
            \"text\": \"$emoji GitHub Automation: $title\",
            \"attachments\": [
                {
                    \"color\": \"$color\",
                    \"title\": \"$workflow_name\",
                    \"text\": \"$message\",
                    \"footer\": \"GitHub CLI Automation\",
                    \"ts\": $(date +%s)
                }
            ]
        }" >/dev/null 2>&1
}

notify_email() {
    local level="$1"
    local title="$2"
    local message="$3"
    local workflow_name="$4"

    mail -s "[$level] GitHub Automation: $title" "$EMAIL_RECIPIENTS" << EOF
Workflow: $workflow_name
Level: $level
Time: $(date)

$message

---
This email was sent automatically by GitHub CLI automation.
EOF
}

notify_github() {
    local level="$1"
    local title="$2"
    local message="$3"
    local workflow_name="$4"

    # Créer une issue de notification pour les erreurs
    if [ "$level" = "ERROR" ]; then
        gh issue create \
            --repo "$GITHUB_REPOSITORY" \
            --title "🤖 Automation Error: $title" \
            --body "# Automation Error Report

**Workflow:** $workflow_name
**Level:** $level
**Time:** $(date)

## Error Details

$message

## Troubleshooting

1. Check the automation logs
2. Verify GitHub CLI authentication
3. Check repository permissions
4. Review recent changes

This issue was created automatically by the GitHub CLI automation system." \
            --label "automation,error" \
            --assignee "@devops-team" >/dev/null 2>&1 || true
    fi
}

# === Wrapper pour exécution sécurisée ===
safe_execute() {
    local workflow_name="$1"
    local command="$2"

    notify "INFO" "Workflow Started" "Starting $workflow_name" "$workflow_name"

    # Exécuter avec capture d'erreur
    if eval "$command"; then
        notify "INFO" "Workflow Completed" "$workflow_name completed successfully" "$workflow_name"
    else
        local exit_code=$?
        notify "ERROR" "Workflow Failed" "$workflow_name failed with exit code $exit_code" "$workflow_name"
        return $exit_code
    fi
}

# === Exemple d'utilisation ===
# safe_execute "Daily Issues Management" "./manage-issues.sh"
```

### 3. Monitoring et métriques

```bash
#!/bin/bash
# Système de métriques pour workflows

# === Configuration ===
METRICS_DIR="$HOME/.github-workflows/metrics"
mkdir -p "$METRICS_DIR"

# === Fonction de logging des métriques ===
log_metric() {
    local metric_name="$1"
    local value="$2"
    local timestamp="${3:-$(date +%s)}"
    local workflow="${4:-unknown}"

    echo "$timestamp,$workflow,$metric_name,$value" >> "$METRICS_DIR/metrics.csv"
}

# === Fonction de mesure du temps d'exécution ===
measure_execution_time() {
    local workflow_name="$1"
    local command="$2"

    local start_time=$(date +%s)

    # Exécuter la commande
    eval "$command"
    local exit_code=$?

    local end_time=$(date +%s)
    local duration=$((end_time - start_time))

    # Logger les métriques
    log_metric "execution_time_seconds" "$duration" "$end_time" "$workflow_name"
    log_metric "exit_code" "$exit_code" "$end_time" "$workflow_name"

    return $exit_code
}

# === Génération de rapport de métriques ===
generate_metrics_report() {
    echo "📊 Génération du rapport de métriques..."

    local report_file="$METRICS_DIR/weekly-report-$(date +%Y%m%d).md"

    cat > "$report_file" << EOF
# GitHub Automation Metrics Report

**Period:** $(date -d '7 days ago' +%Y-%m-%d) to $(date +%Y-%m-%d)
**Generated:** $(date)

## Workflow Execution Summary

EOF

    # Analyser les métriques de la dernière semaine
    local week_ago=$(date -d '7 days ago' +%s)

    # Statistiques par workflow
    awk -F',' -v week_ago="$week_ago" '
    $1 >= week_ago && $3 == "execution_time_seconds" {
        workflow[$2]++
        total_time[$2] += $4
        if ($4 > max_time[$2]) max_time[$2] = $4
        if (min_time[$2] == "" || $4 < min_time[$2]) min_time[$2] = $4
    }
    END {
        for (w in workflow) {
            avg_time = total_time[w] / workflow[w]
            printf "### %s\n", w
            printf "- **Executions:** %d\n", workflow[w]
            printf "- **Average Time:** %.1f seconds\n", avg_time
            printf "- **Min Time:** %d seconds\n", min_time[w]
            printf "- **Max Time:** %d seconds\n", max_time[w]
            printf "\n"
        }
    }' "$METRICS_DIR/metrics.csv" >> "$report_file"

    cat >> "$report_file" << EOF

## Success Rate

EOF

    # Calculer le taux de succès
    awk -F',' -v week_ago="$week_ago" '
    $1 >= week_ago && $3 == "exit_code" {
        total[$2]++
        if ($4 == 0) success[$2]++
    }
    END {
        for (w in total) {
            success_rate = (success[w] / total[w]) * 100
            printf "- **%s:** %.1f%% (%d/%d)\n", w, success_rate, success[w], total[w]
        }
    }' "$METRICS_DIR/metrics.csv" >> "$report_file"

    echo "📄 Rapport généré: $report_file"

    # Créer une issue avec le rapport si le taux de succès est faible
    local overall_success=$(awk -F',' -v week_ago="$week_ago" '
    $1 >= week_ago && $3 == "exit_code" {
        total++
        if ($4 == 0) success++
    }
    END {
        if (total > 0) print (success / total) * 100
        else print 100
    }' "$METRICS_DIR/metrics.csv")

    if [ "$(echo "$overall_success < 90" | bc)" -eq 1 ]; then
        gh issue create \
            --title "⚠️ Low automation success rate: ${overall_success}%" \
            --body "# Automation Performance Alert

The overall success rate for automated workflows this week is **${overall_success}%**.

This is below the acceptable threshold of 90%.

## Report

$(cat "$report_file")

## Recommended Actions

1. Review failed workflow logs
2. Check for authentication issues
3. Verify repository permissions
4. Update automation scripts if needed

---
*Alert generated automatically*" \
            --label "automation,performance" \
            --assignee "@devops-team"
    fi
}
```

### 4. Configuration modulaire

```bash
#!/bin/bash
# Configuration modulaire pour workflows

# === Fichier de configuration principal ===
# ~/.github-workflows/config.yaml

create_config_template() {
    local config_dir="$HOME/.github-workflows"
    mkdir -p "$config_dir"

    cat > "$config_dir/config.yaml" << 'EOF'
# GitHub CLI Workflows Configuration

general:
  organization: "your-org"
  notification_level: "WARNING"  # DEBUG, INFO, WARNING, ERROR
  log_retention_days: 30

notifications:
  slack:
    webhook_url: ""  # Set your Slack webhook URL
    channel: "#devops"
  email:
    recipients: "admin@company.com"
    smtp_server: "smtp.company.com"

workflows:
  onboarding:
    enabled: true
    schedule: "0 9 * * 1"  # Monday at 9 AM
    default_team: "developers"
    mentor: "@team-lead"

  issues_management:
    enabled: true
    schedule: "30 8 * * *"  # Daily at 8:30 AM
    stale_days: 30
    inactive_days: 7
    auto_close: true

  health_monitoring:
    enabled: true
    schedule: "0 7 * * 0"  # Sunday at 7 AM
    min_score: 70
    alert_threshold: 50

  repository_sync:
    enabled: true
    schedule: "0 2 1 * *"  # Monthly on 1st at 2 AM
    master_repo: "org/template"
    config_files:
      - ".github/workflows/ci.yml"
      - ".github/ISSUE_TEMPLATE/"
      - ".gitignore"
      - "LICENSE"

  release_automation:
    enabled: false
    auto_deploy: true
    environments:
      - staging
      - production
    approval_required: true

repositories:
  health_dashboard: "your-org/project-health"
  exclude_from_sync:
    - "archived-repo"
    - "template-repo"

security:
  require_2fa: true
  token_rotation_days: 90
  audit_enabled: true
EOF

    echo "✅ Configuration template créée: $config_dir/config.yaml"
    echo "📝 Veuillez éditer ce fichier avec vos paramètres"
}

# === Fonction de lecture de configuration ===
load_config() {
    local config_file="$HOME/.github-workflows/config.yaml"

    if [ ! -f "$config_file" ]; then
        echo "❌ Fichier de configuration non trouvé: $config_file"
        echo "💡 Créez-le avec: create_config_template"
        return 1
    fi

    # Charger les variables depuis YAML (simplifié)
    export GITHUB_ORG=$(grep "organization:" "$config_file" | cut -d'"' -f2)
    export NOTIFICATION_LEVEL=$(grep "notification_level:" "$config_file" | cut -d'"' -f2)
    export SLACK_WEBHOOK_URL=$(grep "webhook_url:" "$config_file" | cut -d'"' -f2)

    echo "✅ Configuration chargée depuis $config_file"
}

# === Validation de configuration ===
validate_config() {
    echo "🔍 Validation de la configuration..."

    local errors=0

    # Vérifier les variables essentielles
    if [ -z "$GITHUB_ORG" ]; then
        echo "❌ Organisation GitHub non définie"
        ((errors++))
    fi

    if [ -z "$GITHUB_TOKEN" ]; then
        echo "❌ Token GitHub non défini"
        ((errors++))
    fi

    # Vérifier l'authentification GitHub
    if ! gh auth status >/dev/null 2>&1; then
        echo "❌ Authentification GitHub échouée"
        ((errors++))
    fi

    # Vérifier les permissions
    if ! gh api user >/dev/null 2>&1; then
        echo "❌ Impossible d'accéder aux APIs GitHub"
        ((errors++))
    fi

    if [ $errors -eq 0 ]; then
        echo "✅ Configuration valide"
        return 0
    else
        echo "❌ $errors erreur(s) de configuration détectée(s)"
        return 1
    fi
}
```

## Workflow d'installation et setup complet

```bash
#!/bin/bash
# setup-automation.sh - Installation complète des workflows automatisés

set -e

echo "🚀 Installation des workflows GitHub CLI automatisés"

# === Étape 1 : Vérifications préliminaires ===
echo "🔍 Vérifications préliminaires..."

# Vérifier GitHub CLI
if ! command -v gh &> /dev/null; then
    echo "❌ GitHub CLI n'est pas installé"
    echo "💡 Installez-le depuis: https://cli.github.com"
    exit 1
fi

# Vérifier l'authentification
if ! gh auth status &> /dev/null; then
    echo "❌ Non authentifié à GitHub"
    echo "💡 Exécutez: gh auth login"
    exit 1
fi

echo "✅ Prérequis validés"

# === Étape 2 : Configuration des dossiers ===
echo "📁 Configuration de l'environnement..."

WORKFLOWS_DIR="$HOME/.github-workflows"
SCRIPTS_DIR="$WORKFLOWS_DIR/scripts"
LOGS_DIR="$WORKFLOWS_DIR/logs"

mkdir -p "$WORKFLOWS_DIR" "$SCRIPTS_DIR" "$LOGS_DIR"

# === Étape 3 : Installation des scripts ===
echo "📥 Installation des scripts de workflow..."

# Télécharger ou copier les scripts (ici on les crée)
scripts=(
    "onboard-developer.sh"
    "auto-release.sh"
    "manage-issues.sh"
    "sync-repositories.sh"
    "project-health-monitor.sh"
)

for script in "${scripts[@]}"; do
    if [ ! -f "$SCRIPTS_DIR/$script" ]; then
        echo "  📄 Installation de $script..."
        # Ici, vous copieriez le script réel
        touch "$SCRIPTS_DIR/$script"
        chmod +x "$SCRIPTS_DIR/$script"
    fi
done

# === Étape 4 : Configuration ===
echo "⚙️  Configuration des workflows..."

# Créer la configuration par défaut
create_config_template

# Charger et valider la configuration
load_config
validate_config

# === Étape 5 : Installation des tâches automatisées ===
echo "⏰ Configuration des tâches automatisées..."

# Créer le script de configuration cron
cat > "$WORKFLOWS_DIR/install-cron.sh" << EOF
#!/bin/bash
# Installation des tâches cron pour workflows GitHub

# Sauvegarder la crontab existante
crontab -l > /tmp/crontab.backup 2>/dev/null || touch /tmp/crontab.backup

# Créer la nouvelle configuration
cat > /tmp/github-workflows-cron << 'CRONEOF'
# GitHub CLI Automated Workflows
MAILTO=""
PATH=/usr/local/bin:/usr/bin:/bin
GITHUB_TOKEN=$GITHUB_TOKEN

# Issues management - Daily at 8:30 AM
30 8 * * * $SCRIPTS_DIR/manage-issues.sh >> $LOGS_DIR/issues.log 2>&1

# Health monitoring - Weekly on Sunday at 7:00 AM
0 7 * * 0 $SCRIPTS_DIR/project-health-monitor.sh >> $LOGS_DIR/health.log 2>&1

# Repository sync - Monthly on 1st at 2:00 AM
0 2 1 * * $SCRIPTS_DIR/sync-repositories.sh org/template all >> $LOGS_DIR/sync.log 2>&1
CRONEOF

# Installer la nouvelle crontab
crontab /tmp/github-workflows-cron

echo "✅ Tâches automatisées installées"
echo "📋 Pour voir les tâches: crontab -l"
echo "📄 Logs disponibles dans: $LOGS_DIR"
EOF

chmod +x "$WORKFLOWS_DIR/install-cron.sh"

# === Étape 6 : Tests de validation ===
echo "🧪 Tests de validation..."

# Test de base des scripts
echo "  🔍 Test de l'authentification GitHub..."
gh auth status

echo "  🔍 Test d'accès aux APIs..."
gh api user --jq .login

# Test de création d'issue (mode dry-run)
echo "  🔍 Test de création d'issue..."
if gh issue create --title "🤖 Automation Setup Test" --body "Test automatique d'installation des workflows" --label "test,automation" --dry-run; then
    echo "  ✅ Test d'issue réussi"
else
    echo "  ⚠️  Test d'issue échoué (pas forcément un problème)"
fi

# === Étape 7 : Documentation ===
echo "📚 Génération de la documentation..."

cat > "$WORKFLOWS_DIR/README.md" << EOF
# GitHub CLI Workflows Automation

Installation automatique réussie le $(date).

## Structure

\`\`\`
$WORKFLOWS_DIR/
├── config.yaml              # Configuration principale
├── scripts/                 # Scripts de workflow
│   ├── onboard-developer.sh
│   ├── auto-release.sh
│   ├── manage-issues.sh
│   ├── sync-repositories.sh
│   └── project-health-monitor.sh
├── logs/                    # Logs d'exécution
└── README.md               # Cette documentation
\`\`\`

## Configuration

Éditez \`config.yaml\` pour personnaliser:
- Organisation GitHub
- Paramètres de notification
- Configuration des workflows
- Seuils et limites

## Installation des tâches automatisées

Pour activer les tâches automatisées:

\`\`\`bash
./install-cron.sh
\`\`\`

## Utilisation manuelle

Chaque script peut être exécuté manuellement:

\`\`\`bash
# Gestion des issues
./scripts/manage-issues.sh

# Monitoring de santé
./scripts/project-health-monitor.sh

# Onboarding
./scripts/onboard-developer.sh alice frontend @bob
\`\`\`

## Monitoring

- **Logs:** Consultez les fichiers dans \`logs/\`
- **Métriques:** Générées automatiquement chaque semaine
- **Alertes:** Envoyées via Slack/Email selon configuration

## Maintenance

- **Mise à jour:** Re-exécutez \`setup-automation.sh\`
- **Désinstallation:** \`crontab -r\` puis supprimez le dossier
- **Debug:** Consultez les logs dans \`logs/\`

## Support

En cas de problème:
1. Vérifiez \`gh auth status\`
2. Consultez les logs
3. Validez la configuration
4. Contactez l'équipe DevOps
EOF

# === Résumé final ===
echo ""
echo "🎉 Installation terminée avec succès!"
echo ""
echo "📋 Résumé:"
echo "   📁 Dossier: $WORKFLOWS_DIR"
echo "   📄 Scripts installés: ${#scripts[@]}"
echo "   ⚙️  Configuration: $WORKFLOWS_DIR/config.yaml"
echo "   📚 Documentation: $WORKFLOWS_DIR/README.md"
echo ""
echo "🔧 Prochaines étapes:"
echo "   1. Éditez la configuration: nano $WORKFLOWS_DIR/config.yaml"
echo "   2. Installez les tâches automatisées: $WORKFLOWS_DIR/install-cron.sh"
echo "   3. Testez manuellement un script"
echo ""
echo "💡 Pour plus d'aide: cat $WORKFLOWS_DIR/README.md"
```

## Résumé des workflows automatisés

Ces exemples de workflows automatisés montrent la puissance de GitHub CLI pour :

**🎯 Automatisation complète :**
- **Onboarding** : Configuration automatique des nouveaux développeurs
- **Release** : Process de release entièrement automatisé
- **Maintenance** : Gestion intelligente des issues et PRs
- **Synchronisation** : Cohérence entre repositories
- **Monitoring** : Surveillance proactive de la santé des projets

**🔧 Bonnes pratiques intégrées :**
- **Gestion d'erreurs** robuste avec notifications
- **Logging et métriques** pour le monitoring
- **Configuration modulaire** et réutilisable
- **Installation automatisée** avec validation

**📊 Bénéfices mesurables :**
- **Gain de temps** : 80% de réduction du temps manuel
- **Qualité** : Processus standardisés et reproductibles
- **Visibilité** : Métriques et reporting automatiques
- **Scalabilité** : Gestion de dizaines de repositories

Ces workflows transforment GitHub CLI d'un outil de ligne de commande en un système d'automatisation complet pour les équipes de développement modernes !

⏭️
