🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 8.2 Notifications et mentions

## Introduction

Les notifications et mentions sont le système nerveux de la collaboration sur GitHub. Un système bien configuré vous tient informé des développements importants sans vous submerger, tandis que les mentions permettent d'attirer l'attention des bonnes personnes au bon moment. Avec la CLI GitHub, vous pouvez gérer finement ces aspects pour optimiser votre productivité et celle de votre équipe.

Dans cette section, nous allons apprendre à maîtriser le système de notifications GitHub, utiliser efficacement les mentions, et créer des workflows de communication optimisés.

## Comprendre le système de notifications GitHub

### Types de notifications

**Notifications automatiques :**
- 🔔 **Watching** : Toute activité sur un repository suivi
- 📝 **Participating** : Quand vous êtes mentionné ou impliqué
- 🏷️ **Issues assignées** : Issues ou PR qui vous sont assignées
- 👥 **Team mentions** : Mentions d'équipes dont vous faites partie

**Événements déclencheurs :**
- Nouveaux issues/PR
- Commentaires ajoutés
- Reviews demandées
- Changements d'assignation
- Modifications de labels/milestones

### Canaux de notification

**Sur GitHub :**
- Icône de notification (🔔) dans l'interface
- Page dédiée aux notifications
- Emails (configurable)

**Externes :**
- Emails personnalisés
- Intégrations Slack/Discord
- Webhooks personnalisés

## Gestion des notifications avec la CLI

### Lister les notifications

```bash
# Voir toutes les notifications non lues
gh api notifications

# Voir les notifications avec formatage
gh api notifications --jq '.[] | {title: .subject.title, type: .subject.type, repo: .repository.full_name, updated: .updated_at}'
```

**Sortie typique :**
```json
{
  "title": "Fix authentication bug",
  "type": "PullRequest",
  "repo": "mycompany/backend-api",
  "updated": "2024-07-20T10:30:00Z"
}
```

### Filtrer les notifications

```bash
# Notifications par repository
gh api notifications --jq '.[] | select(.repository.name=="myproject")'

# Notifications non lues seulement
gh api notifications --jq '.[] | select(.unread==true)'

# Notifications par type
gh api notifications --jq '.[] | select(.subject.type=="Issue")'

# Notifications récentes (dernières 24h)
gh api notifications --jq '.[] | select(.updated_at > "'$(date -u -d '24 hours ago' +%Y-%m-%dT%H:%M:%SZ)'")'
```

### Marquer les notifications comme lues

```bash
# Marquer une notification spécifique comme lue
NOTIFICATION_ID="123456789"
gh api notifications/threads/$NOTIFICATION_ID --method PATCH

# Marquer toutes les notifications d'un repo comme lues
gh api repos/OWNER/REPO/notifications --method PUT

# Marquer toutes les notifications comme lues
gh api notifications --method PUT --field read=true
```

## Système de mentions

### Types de mentions

**Mentions individuelles :**
```markdown
@username - Mention directe d'un utilisateur
```

**Mentions d'équipes :**
```markdown
@organization/team-name - Mention d'une équipe entière
```

**Mentions de rôles :**
```markdown
@organization/repo-admins - Mention basée sur les permissions
```

### Utiliser les mentions efficacement

**Dans les issues :**
```bash
# Créer une issue avec mentions
gh issue create --title "Bug critique" --body "
# Bug critique dans l'authentification

Un problème majeur a été détecté dans le système d'auth.

@mycompany/backend-team pouvez-vous investiguer ?
@john-doe pour info, cela affecte votre travail sur la feature login.

CC: @mycompany/security-team
"
```

**Dans les pull requests :**
```bash
# Créer une PR avec reviewers et mentions
gh pr create \
  --title "Fix authentication bug" \
  --body "@mycompany/backend-team Voici le fix pour le bug mentionné dans #123.

## Changements
- Correction de la validation des tokens
- Ajout de tests unitaires

@jane-reviewer pouvez-vous reviewer la partie sécurité ?
@qa-team merci de tester la connexion/déconnexion." \
  --reviewer mycompany/backend-team,jane-reviewer
```

### Mentions contextuelles

**Mentions dans les commentaires :**
```bash
# Ajouter un commentaire avec mention
gh issue comment 123 --body "
@designer-team merci pour les maquettes !

Quelques questions :
1. @alice-ux la couleur du bouton est-elle finale ?
2. @bob-ui faut-il ajuster le responsive ?

@frontend-team vous pouvez commencer l'intégration.
"
```

**Mentions en cascade :**
```bash
# Commenter avec escalade
gh pr comment 456 --body "
Après investigation avec @backend-team, ce bug nécessite une approche différente.

@tech-lead votre avis sur l'architecture ?
@product-manager cela peut affecter le planning.

@mycompany/engineering merci de donner votre feedback.
"
```

## Configuration personnalisée des notifications

### Paramétrer les notifications par repository

```bash
# S'abonner aux notifications d'un repository
gh api repos/OWNER/REPO/subscription \
  --method PUT \
  --field subscribed=true \
  --field ignored=false

# Se désabonner des notifications
gh api repos/OWNER/REPO/subscription \
  --method PUT \
  --field subscribed=false \
  --field ignored=true

# Configuration fine : seulement les issues
gh api repos/OWNER/REPO/subscription \
  --method PUT \
  --field subscribed=true \
  --field ignored=false \
  --field reason="issues"
```

### Gestion des notifications d'équipe

```bash
# Configurer les notifications pour une équipe (en tant qu'admin)
gh api orgs/ORGANIZATION/teams/TEAM_SLUG/notifications \
  --method PATCH \
  --field notifications_enabled=true
```

### Script de configuration globale

```bash
#!/bin/bash
# configure-notifications.sh - Configuration personnalisée des notifications

echo "🔔 Configuration des notifications GitHub"

# Repositories à surveiller de près (toutes notifications)
CRITICAL_REPOS=(
    "mycompany/production-api"
    "mycompany/frontend-app"
    "mycompany/security-tools"
)

# Repositories à surveiller légèrement (seulement participations)
LIGHT_REPOS=(
    "mycompany/documentation"
    "mycompany/internal-tools"
    "mycompany/experiments"
)

# Repositories à ignorer
IGNORED_REPOS=(
    "mycompany/archived-project"
    "mycompany/old-website"
)

echo "📢 Configuration des repositories critiques..."
for repo in "${CRITICAL_REPOS[@]}"; do
    echo "  Surveillance complète: $repo"
    gh api repos/$repo/subscription \
      --method PUT \
      --field subscribed=true \
      --field ignored=false &>/dev/null
done

echo "👁️  Configuration des repositories secondaires..."
for repo in "${LIGHT_REPOS[@]}"; do
    echo "  Surveillance légère: $repo"
    gh api repos/$repo/subscription \
      --method PUT \
      --field subscribed=false \
      --field ignored=false &>/dev/null
done

echo "🔇 Désactivation des repositories ignorés..."
for repo in "${IGNORED_REPOS[@]}"; do
    echo "  Ignoré: $repo"
    gh api repos/$repo/subscription \
      --method PUT \
      --field subscribed=false \
      --field ignored=true &>/dev/null
done

echo "✅ Configuration terminée"
```

## Workflow de notification intelligent

### Tableau de bord de notifications

```bash
#!/bin/bash
# notification-dashboard.sh - Dashboard des notifications

echo "📊 Dashboard des Notifications GitHub"
echo "====================================="

# Compter les notifications par type
echo "📈 Résumé:"
echo "  Total non lues: $(gh api notifications --jq '. | length')"
echo "  Issues: $(gh api notifications --jq '[.[] | select(.subject.type=="Issue")] | length')"
echo "  Pull Requests: $(gh api notifications --jq '[.[] | select(.subject.type=="PullRequest")] | length')"
echo "  Releases: $(gh api notifications --jq '[.[] | select(.subject.type=="Release")] | length')"

echo ""
echo "🔥 Notifications prioritaires (dernières 2h):"
RECENT_TIME=$(date -u -d '2 hours ago' +%Y-%m-%dT%H:%M:%SZ)
gh api notifications --jq --arg recent "$RECENT_TIME" '
  .[] | select(.updated_at > $recent) |
  {
    title: .subject.title,
    type: .subject.type,
    repo: .repository.name,
    updated: .updated_at
  }' | jq -s 'sort_by(.updated) | reverse | .[:5]'

echo ""
echo "👥 Mentions d'équipes:"
gh api notifications --jq '.[] | select(.reason=="team_mention") | {title: .subject.title, repo: .repository.name}'

echo ""
echo "📝 Reviews demandées:"
gh api notifications --jq '.[] | select(.reason=="review_requested") | {title: .subject.title, repo: .repository.name}'
```

### Automatisation des réponses

```bash
#!/bin/bash
# auto-respond.sh - Réponses automatiques intelligentes

# Fonction pour répondre automatiquement aux mentions
respond_to_mentions() {
    local notification_id=$1
    local subject_type=$2
    local subject_url=$3

    case $subject_type in
        "Issue")
            # Acquitter les mentions dans les issues
            issue_number=$(basename "$subject_url")
            repo_path=$(echo "$subject_url" | sed 's|https://api.github.com/repos/||' | sed 's|/issues/.*||')

            gh issue comment $issue_number --repo $repo_path --body "
👋 Merci pour la mention ! Je regarde ça et reviens vers vous.

_Message automatique - répondra manuellement si nécessaire_
            " &>/dev/null
            ;;
        "PullRequest")
            # Acquitter les mentions dans les PR
            pr_number=$(basename "$subject_url")
            repo_path=$(echo "$subject_url" | sed 's|https://api.github.com/repos/||' | sed 's|/pulls/.*||')

            gh pr comment $pr_number --repo $repo_path --body "
👀 Merci pour la mention ! Je vais review cette PR.

_Message automatique - review détaillée à suivre_
            " &>/dev/null
            ;;
    esac
}

# Traiter les mentions récentes
echo "🤖 Traitement automatique des mentions..."
gh api notifications --jq '.[] | select(.reason=="mention" or .reason=="team_mention") | {id: .id, type: .subject.type, url: .subject.url}' | \
jq -r '.id + "|" + .type + "|" + .url' | \
while IFS='|' read -r id type url; do
    echo "  Réponse automatique pour: $id"
    respond_to_mentions "$id" "$type" "$url"

    # Marquer comme traité
    gh api notifications/threads/$id --method PATCH &>/dev/null
done
```

## Intégrations et notifications externes

### Webhook Slack pour notifications

```bash
#!/bin/bash
# setup-slack-notifications.sh - Configuration Slack

SLACK_WEBHOOK_URL=$1
REPO_OWNER=$2
REPO_NAME=$3

if [ -z "$SLACK_WEBHOOK_URL" ] || [ -z "$REPO_OWNER" ] || [ -z "$REPO_NAME" ]; then
    echo "Usage: $0 <slack-webhook-url> <owner> <repo>"
    exit 1
fi

echo "📢 Configuration Slack pour $REPO_OWNER/$REPO_NAME"

# Créer le webhook
gh api repos/$REPO_OWNER/$REPO_NAME/hooks \
  --method POST \
  --field name="web" \
  --field active=true \
  --field events='["issues","pull_request","push","release"]' \
  --field config="{
    \"url\":\"$SLACK_WEBHOOK_URL\",
    \"content_type\":\"json\",
    \"insecure_ssl\":\"0\"
  }"

echo "✅ Webhook Slack configuré"
echo "📋 Événements: issues, pull_request, push, release"
```

### Script de notification Discord

```bash
#!/bin/bash
# discord-notifier.sh - Notifications Discord

DISCORD_WEBHOOK_URL=$1
MESSAGE=$2
MENTION_ROLE=$3

if [ -z "$DISCORD_WEBHOOK_URL" ] || [ -z "$MESSAGE" ]; then
    echo "Usage: $0 <discord-webhook> <message> [mention-role]"
    exit 1
fi

# Construire le payload
PAYLOAD=$(cat <<EOF
{
  "embeds": [{
    "title": "🔔 GitHub Notification",
    "description": "$MESSAGE",
    "color": 65280,
    "timestamp": "$(date -u +%Y-%m-%dT%H:%M:%S.000Z)"
  }]
}
EOF
)

# Ajouter mention de rôle si spécifiée
if [ -n "$MENTION_ROLE" ]; then
    PAYLOAD=$(echo "$PAYLOAD" | jq --arg role "$MENTION_ROLE" '. + {content: "<@&" + $role + ">"}')
fi

# Envoyer la notification
curl -H "Content-Type: application/json" \
     -d "$PAYLOAD" \
     "$DISCORD_WEBHOOK_URL"

echo "✅ Notification Discord envoyée"
```

## Filtrage avancé des notifications

### Script de filtrage intelligent

```bash
#!/bin/bash
# smart-filter.sh - Filtrage intelligent des notifications

echo "🧠 Filtrage intelligent des notifications"

# Définir les mots-clés prioritaires
PRIORITY_KEYWORDS=("urgent" "critical" "security" "hotfix" "production")
IGNORE_KEYWORDS=("wip" "draft" "test" "experiment")

# Fonction de scoring des notifications
score_notification() {
    local title="$1"
    local body="$2"
    local score=0

    # Points pour mots-clés prioritaires
    for keyword in "${PRIORITY_KEYWORDS[@]}"; do
        if echo "$title $body" | grep -qi "$keyword"; then
            score=$((score + 10))
        fi
    done

    # Déductions pour mots-clés à ignorer
    for keyword in "${IGNORE_KEYWORDS[@]}"; do
        if echo "$title $body" | grep -qi "$keyword"; then
            score=$((score - 5))
        fi
    done

    echo $score
}

# Traiter les notifications avec scoring
echo "🎯 Notifications prioritaires (score > 5):"
gh api notifications --jq '.[] | {id: .id, title: .subject.title, repo: .repository.name, url: .subject.url}' | \
jq -r '.id + "|" + .title + "|" + .repo + "|" + .url' | \
while IFS='|' read -r id title repo url; do
    # Obtenir le contenu pour scoring (approximatif)
    score=$(score_notification "$title" "")

    if [ $score -gt 5 ]; then
        echo "  🔥 Score: $score - $title ($repo)"
        echo "     URL: $url"
    fi
done

echo ""
echo "🔇 Notifications à ignorer automatiquement:"
gh api notifications --jq '.[] | {id: .id, title: .subject.title, repo: .repository.name}' | \
jq -r '.id + "|" + .title + "|" + .repo' | \
while IFS='|' read -r id title repo; do
    score=$(score_notification "$title" "")

    if [ $score -lt 0 ]; then
        echo "  💤 Score: $score - $title ($repo)"
        # Marquer comme lu automatiquement
        gh api notifications/threads/$id --method PATCH &>/dev/null
    fi
done
```

### Règles de filtrage par repository

```bash
#!/bin/bash
# repository-filters.sh - Filtres par repository

# Configuration des règles
declare -A REPO_RULES
REPO_RULES[mycompany/production-api]="all"          # Toutes notifications
REPO_RULES[mycompany/frontend-app]="mentions-only"  # Seulement mentions
REPO_RULES[mycompany/documentation]="ignore"        # Ignorer
REPO_RULES[mycompany/experiments]="weekends-only"   # Seulement week-ends

apply_repository_rules() {
    for repo in "${!REPO_RULES[@]}"; do
        rule="${REPO_RULES[$repo]}"

        case $rule in
            "all")
                echo "📢 $repo: Toutes notifications activées"
                gh api repos/$repo/subscription \
                  --method PUT \
                  --field subscribed=true \
                  --field ignored=false &>/dev/null
                ;;
            "mentions-only")
                echo "👤 $repo: Seulement les mentions"
                gh api repos/$repo/subscription \
                  --method PUT \
                  --field subscribed=false \
                  --field ignored=false &>/dev/null
                ;;
            "ignore")
                echo "🔇 $repo: Ignoré"
                gh api repos/$repo/subscription \
                  --method PUT \
                  --field subscribed=false \
                  --field ignored=true &>/dev/null
                ;;
            "weekends-only")
                echo "📅 $repo: Week-ends seulement"
                # Logique personnalisée selon le jour
                if [ $(date +%u) -gt 5 ]; then
                    gh api repos/$repo/subscription \
                      --method PUT \
                      --field subscribed=true \
                      --field ignored=false &>/dev/null
                else
                    gh api repos/$repo/subscription \
                      --method PUT \
                      --field subscribed=false \
                      --field ignored=false &>/dev/null
                fi
                ;;
        esac
    done
}

apply_repository_rules
```

## Bonnes pratiques des notifications

### Étiquette des mentions

**✅ Bonnes pratiques :**
```markdown
# Mentions spécifiques et justifiées
@alice-backend pouvez-vous vérifier cette API ?

# Contexte clair
@team/frontend cette PR affecte le composant LoginForm que vous maintenez

# Mentions groupées
@team/reviewers cette PR est prête pour review
```

**❌ À éviter :**
```markdown
# Mentions trop larges
@team/everyone regardez ça

# Mentions sans contexte
@john-doe

# Mentions répétitives
@team/backend @team/backend @team/backend
```

### Configuration optimale

```bash
#!/bin/bash
# optimal-setup.sh - Configuration optimale des notifications

echo "⚙️  Configuration optimale des notifications GitHub"

# 1. Configurer les notifications de base
echo "📧 Configuration des emails..."
gh api user --method PATCH --field notification_email="work@company.com"

# 2. Ajuster les préférences de participation
echo "👥 Configuration des participations..."
# (Nécessite l'interface web pour certains paramètres)

# 3. Configurer les repositories par priorité
echo "📁 Configuration par repository..."

# Repositories critiques - surveillance maximale
CRITICAL_REPOS=("company/api" "company/frontend" "company/auth")
for repo in "${CRITICAL_REPOS[@]}"; do
    gh api repos/$repo/subscription \
      --method PUT \
      --field subscribed=true \
      --field ignored=false &>/dev/null
    echo "  🔥 Critique: $repo"
done

# Repositories normaux - seulement participations
NORMAL_REPOS=("company/docs" "company/tools" "company/website")
for repo in "${NORMAL_REPOS[@]}"; do
    gh api repos/$repo/subscription \
      --method PUT \
      --field subscribed=false \
      --field ignored=false &>/dev/null
    echo "  📝 Normal: $repo"
done

echo "✅ Configuration optimale appliquée"
```

## Points clés à retenir

✅ **Notifications ciblées** réduisent le bruit et améliorent la productivité

✅ **Mentions contextuelles** facilitent la collaboration efficace

✅ **Configuration par repository** permet un contrôle granulaire

✅ **Automatisation** du traitement des notifications courantes

✅ **Intégrations externes** (Slack, Discord) centralisent la communication

✅ **Filtrage intelligent** priorise les notifications importantes

✅ **Bonnes pratiques** des mentions améliorent la communication d'équipe

## Prochaine étape

Maintenant que vous maîtrisez les notifications et mentions, nous allons apprendre à créer et utiliser des templates pour standardiser et améliorer vos processus de collaboration.


⏭️
