🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 8.1 Gestion des équipes et permissions

## Introduction

La gestion des équipes et des permissions est l'un des piliers d'une collaboration efficace sur GitHub. Une structure d'équipe bien organisée avec des permissions appropriées permet de maintenir la sécurité tout en facilitant la productivité. Avec la CLI GitHub, vous pouvez gérer ces aspects complexes de manière simple et automatisée.

Dans cette section, nous allons apprendre à créer et organiser des équipes, configurer des permissions granulaires, et maintenir une structure de collaboration sécurisée et efficace.

## Comprendre les équipes GitHub

### Qu'est-ce qu'une équipe ?

Une **équipe GitHub** est un groupe d'utilisateurs au sein d'une organisation qui partage des permissions et des responsabilités communes. Les équipes permettent de :

- 🎯 **Organiser** les membres selon leurs rôles
- 🔒 **Contrôler** l'accès aux repositories
- 📢 **Faciliter** la communication et les mentions
- 📊 **Suivre** les contributions par groupe

### Hiérarchie des équipes

**Structure typique :**
```
Organisation: MyCompany
├── @mycompany/admin
├── @mycompany/developers
│   ├── @mycompany/frontend
│   ├── @mycompany/backend
│   └── @mycompany/mobile
├── @mycompany/designers
└── @mycompany/qa
```

**Avantages de la hiérarchie :**
- Permissions héritées des équipes parentes
- Mentions groupées (@mycompany/developers)
- Organisation claire des responsabilités

## Gestion des équipes avec la CLI

### Lister les équipes existantes

```bash
# Voir toutes les équipes de l'organisation
gh api orgs/ORGANIZATION/teams --jq '.[].name'

# Voir les équipes avec plus de détails
gh api orgs/ORGANIZATION/teams --jq '.[] | {name, description, members_count}'
```

**Sortie typique :**
```json
{
  "name": "developers",
  "description": "Équipe de développement",
  "members_count": 8
}
{
  "name": "frontend",
  "description": "Développeurs frontend",
  "members_count": 3
}
```

### Créer une nouvelle équipe

```bash
# Créer une équipe de base
gh api orgs/ORGANIZATION/teams \
  --method POST \
  --field name="backend" \
  --field description="Équipe de développement backend" \
  --field privacy="closed"
```

**Options de privacy :**
- `closed` : Visible par tous, membres visibles par l'organisation
- `secret` : Visible seulement par les membres et propriétaires

### Créer une équipe avec équipe parente

```bash
# Créer une sous-équipe
gh api orgs/ORGANIZATION/teams \
  --method POST \
  --field name="api-team" \
  --field description="Équipe API backend" \
  --field privacy="closed" \
  --field parent_team_id=123456
```

### Obtenir l'ID d'une équipe

```bash
# Méthode simple pour obtenir l'ID
TEAM_ID=$(gh api orgs/ORGANIZATION/teams --jq '.[] | select(.name=="backend") | .id')
echo "ID de l'équipe backend: $TEAM_ID"
```

## Gestion des membres d'équipe

### Ajouter des membres

```bash
# Ajouter un membre à une équipe
gh api orgs/ORGANIZATION/teams/TEAM_SLUG/memberships/USERNAME \
  --method PUT \
  --field role="member"

# Ajouter un mainteneur (role élevé)
gh api orgs/ORGANIZATION/teams/TEAM_SLUG/memberships/USERNAME \
  --method PUT \
  --field role="maintainer"
```

**Rôles disponibles :**
- `member` : Membre standard
- `maintainer` : Peut gérer l'équipe et ses membres

### Lister les membres d'une équipe

```bash
# Voir tous les membres
gh api orgs/ORGANIZATION/teams/TEAM_SLUG/members --jq '.[].login'

# Voir avec les rôles
gh api orgs/ORGANIZATION/teams/TEAM_SLUG/members \
  --jq '.[] | {login, role: (.role // "member")}'
```

### Supprimer un membre

```bash
# Retirer un membre de l'équipe
gh api orgs/ORGANIZATION/teams/TEAM_SLUG/memberships/USERNAME \
  --method DELETE
```

## Gestion des permissions de repository

### Comprendre les niveaux de permissions

**Niveaux disponibles :**
- `pull` : Lecture seule (clone, pull)
- `triage` : Lecture + gestion des issues/PR (sans écriture code)
- `push` : Lecture + écriture (push vers branches)
- `maintain` : Push + gestion du repository (sans admin)
- `admin` : Contrôle total du repository

### Accorder des permissions à une équipe

```bash
# Donner accès en lecture
gh api repos/OWNER/REPO/teams/TEAM_SLUG \
  --method PUT \
  --field permission="pull"

# Donner accès en écriture
gh api repos/OWNER/REPO/teams/TEAM_SLUG \
  --method PUT \
  --field permission="push"

# Donner accès admin
gh api repos/OWNER/REPO/teams/TEAM_SLUG \
  --method PUT \
  --field permission="admin"
```

### Lister les permissions d'équipe sur un repository

```bash
# Voir toutes les équipes ayant accès
gh api repos/OWNER/REPO/teams --jq '.[] | {name, permission}'
```

**Sortie exemple :**
```json
{
  "name": "developers",
  "permission": "push"
}
{
  "name": "reviewers",
  "permission": "pull"
}
```

### Révoquer l'accès d'une équipe

```bash
# Supprimer l'accès d'une équipe au repository
gh api repos/OWNER/REPO/teams/TEAM_SLUG --method DELETE
```

## Scripts d'automatisation pour la gestion d'équipes

### Script de création d'équipe complète

```bash
#!/bin/bash
# create-team.sh - Création d'équipe avec configuration complète

ORGANIZATION="mycompany"
TEAM_NAME=$1
TEAM_DESCRIPTION=$2
PARENT_TEAM=$3

if [ -z "$TEAM_NAME" ] || [ -z "$TEAM_DESCRIPTION" ]; then
    echo "Usage: $0 <team-name> <description> [parent-team]"
    echo "Exemple: $0 mobile-ios \"Équipe iOS\" mobile"
    exit 1
fi

echo "🏗️  Création de l'équipe: $TEAM_NAME"

# Construire la requête de base
REQUEST_BODY=$(cat <<EOF
{
  "name": "$TEAM_NAME",
  "description": "$TEAM_DESCRIPTION",
  "privacy": "closed"
}
EOF
)

# Ajouter l'équipe parente si spécifiée
if [ -n "$PARENT_TEAM" ]; then
    echo "🔗 Équipe parente: $PARENT_TEAM"
    PARENT_ID=$(gh api orgs/$ORGANIZATION/teams --jq ".[] | select(.name==\"$PARENT_TEAM\") | .id")

    if [ -z "$PARENT_ID" ]; then
        echo "❌ Équipe parente '$PARENT_TEAM' introuvable"
        exit 1
    fi

    REQUEST_BODY=$(echo "$REQUEST_BODY" | jq --argjson parent_id "$PARENT_ID" '. + {parent_team_id: $parent_id}')
fi

# Créer l'équipe
echo "$REQUEST_BODY" | gh api orgs/$ORGANIZATION/teams --input -

# Vérifier la création
TEAM_ID=$(gh api orgs/$ORGANIZATION/teams --jq ".[] | select(.name==\"$TEAM_NAME\") | .id")

if [ -n "$TEAM_ID" ]; then
    echo "✅ Équipe '$TEAM_NAME' créée avec l'ID: $TEAM_ID"
    echo "🔗 URL: https://github.com/orgs/$ORGANIZATION/teams/$TEAM_NAME"
else
    echo "❌ Erreur lors de la création de l'équipe"
    exit 1
fi
```

### Script de gestion des membres en lot

```bash
#!/bin/bash
# manage-team-members.sh - Gestion des membres d'équipe en lot

ORGANIZATION="mycompany"
TEAM_SLUG=$1
ACTION=$2
MEMBERS_FILE=$3

if [ -z "$TEAM_SLUG" ] || [ -z "$ACTION" ] || [ -z "$MEMBERS_FILE" ]; then
    echo "Usage: $0 <team-slug> <add|remove> <members-file>"
    echo "Format du fichier: un nom d'utilisateur par ligne"
    exit 1
fi

if [ ! -f "$MEMBERS_FILE" ]; then
    echo "❌ Fichier '$MEMBERS_FILE' introuvable"
    exit 1
fi

echo "👥 Gestion des membres de l'équipe: $TEAM_SLUG"
echo "📝 Action: $ACTION"

# Vérifier que l'équipe existe
if ! gh api orgs/$ORGANIZATION/teams/$TEAM_SLUG &>/dev/null; then
    echo "❌ Équipe '$TEAM_SLUG' introuvable"
    exit 1
fi

# Traiter chaque membre
while IFS= read -r username; do
    # Ignorer les lignes vides et commentaires
    if [ -z "$username" ] || [[ "$username" =~ ^# ]]; then
        continue
    fi

    echo "👤 Traitement de: $username"

    case $ACTION in
        "add")
            if gh api orgs/$ORGANIZATION/teams/$TEAM_SLUG/memberships/$username \
                --method PUT \
                --field role="member" &>/dev/null; then
                echo "  ✅ Ajouté à l'équipe"
            else
                echo "  ❌ Erreur lors de l'ajout"
            fi
            ;;
        "remove")
            if gh api orgs/$ORGANIZATION/teams/$TEAM_SLUG/memberships/$username \
                --method DELETE &>/dev/null; then
                echo "  ✅ Retiré de l'équipe"
            else
                echo "  ❌ Erreur lors de la suppression"
            fi
            ;;
        *)
            echo "❌ Action invalide: $ACTION"
            exit 1
            ;;
    esac
done < "$MEMBERS_FILE"

echo "✅ Traitement terminé"
```

**Exemple de fichier membres :**
```
# Équipe backend
john-doe
jane-smith
alex-dev
# Nouveaux membres
marie-coder
```

### Script de configuration des permissions

```bash
#!/bin/bash
# setup-permissions.sh - Configuration des permissions d'équipe

ORGANIZATION="mycompany"
CONFIG_FILE="permissions.json"

if [ ! -f "$CONFIG_FILE" ]; then
    echo "❌ Fichier de configuration '$CONFIG_FILE' manquant"
    echo "📝 Créez un fichier JSON avec la structure suivante:"
    cat << 'EOF'
{
  "repositories": {
    "myapp-frontend": {
      "frontend": "admin",
      "backend": "pull",
      "qa": "triage"
    },
    "myapp-backend": {
      "backend": "admin",
      "frontend": "pull",
      "qa": "push"
    }
  }
}
EOF
    exit 1
fi

echo "🔒 Configuration des permissions depuis $CONFIG_FILE"

# Traiter chaque repository
jq -r '.repositories | keys[]' "$CONFIG_FILE" | while read repo; do
    echo "📁 Repository: $repo"

    # Traiter chaque équipe pour ce repository
    jq -r ".repositories[\"$repo\"] | keys[]" "$CONFIG_FILE" | while read team; do
        permission=$(jq -r ".repositories[\"$repo\"][\"$team\"]" "$CONFIG_FILE")

        echo "  👥 Équipe: $team → Permission: $permission"

        # Appliquer la permission
        if gh api repos/$ORGANIZATION/$repo/teams/$team \
            --method PUT \
            --field permission="$permission" &>/dev/null; then
            echo "    ✅ Permission configurée"
        else
            echo "    ❌ Erreur de configuration"
        fi
    done
done

echo "✅ Configuration des permissions terminée"
```

## Gestion des permissions granulaires

### Protection de branches par équipe

```bash
# Protéger la branche main avec review obligatoire
gh api repos/OWNER/REPO/branches/main/protection \
  --method PUT \
  --field required_status_checks='{"strict":true,"contexts":[]}' \
  --field enforce_admins=true \
  --field required_pull_request_reviews='{"required_approving_review_count":2,"dismiss_stale_reviews":true}' \
  --field restrictions='{"users":[],"teams":["developers"]}'
```

### Configuration des review requirements

```bash
# Exiger des reviews spécifiques d'équipes
gh api repos/OWNER/REPO/branches/main/protection/required_pull_request_reviews \
  --method PATCH \
  --field required_approving_review_count=1 \
  --field dismiss_stale_reviews=true \
  --field require_code_owner_reviews=true
```

### Gérer les CODEOWNERS

Créez un fichier `.github/CODEOWNERS` pour définir les propriétaires par partie du code :

```bash
# Exemple de fichier CODEOWNERS
cat > .github/CODEOWNERS << 'EOF'
# Propriétaires par défaut
* @mycompany/developers

# Frontend
/src/frontend/ @mycompany/frontend
/public/ @mycompany/frontend
*.css @mycompany/frontend

# Backend
/src/backend/ @mycompany/backend
/api/ @mycompany/backend
*.sql @mycompany/backend

# Infrastructure
/docker/ @mycompany/devops
/.github/ @mycompany/devops
/terraform/ @mycompany/devops

# Documentation
/docs/ @mycompany/tech-writers
*.md @mycompany/tech-writers
EOF
```

## Audit et monitoring des équipes

### Script d'audit des permissions

```bash
#!/bin/bash
# audit-permissions.sh - Audit des permissions d'équipes

ORGANIZATION="mycompany"
OUTPUT_FILE="permissions-audit-$(date +%Y%m%d).json"

echo "🔍 Audit des permissions pour l'organisation: $ORGANIZATION"
echo "📊 Rapport sera sauvé dans: $OUTPUT_FILE"

# Initialiser le rapport
echo '{"audit_date":"'$(date -u +%Y-%m-%dT%H:%M:%SZ)'","organization":"'$ORGANIZATION'","teams":[],"repositories":[]}' > "$OUTPUT_FILE"

# Auditer les équipes
echo "👥 Audit des équipes..."
TEAMS_DATA=$(gh api orgs/$ORGANIZATION/teams --jq '[.[] | {id, name, slug, description, privacy, members_count}]')
jq --argjson teams "$TEAMS_DATA" '.teams = $teams' "$OUTPUT_FILE" > tmp.json && mv tmp.json "$OUTPUT_FILE"

# Auditer les repositories
echo "📁 Audit des repositories..."
gh api orgs/$ORGANIZATION/repos --paginate --jq '.[].name' | while read repo; do
    echo "  Scanning: $repo"

    # Obtenir les équipes ayant accès
    REPO_TEAMS=$(gh api repos/$ORGANIZATION/$repo/teams --jq '[.[] | {name, slug, permission}]' 2>/dev/null || echo '[]')

    # Obtenir les collaborateurs directs
    COLLABORATORS=$(gh api repos/$ORGANIZATION/$repo/collaborators --jq '[.[] | {login, permissions}]' 2>/dev/null || echo '[]')

    # Ajouter au rapport
    REPO_DATA=$(cat <<EOF
{
  "name": "$repo",
  "teams": $REPO_TEAMS,
  "collaborators": $COLLABORATORS
}
EOF
)

    jq --argjson repo_data "$REPO_DATA" '.repositories += [$repo_data]' "$OUTPUT_FILE" > tmp.json && mv tmp.json "$OUTPUT_FILE"
done

echo "✅ Audit terminé: $OUTPUT_FILE"

# Résumé
echo ""
echo "📋 Résumé de l'audit:"
echo "  Équipes: $(jq '.teams | length' "$OUTPUT_FILE")"
echo "  Repositories: $(jq '.repositories | length' "$OUTPUT_FILE")"
echo "  Total des permissions d'équipes: $(jq '[.repositories[].teams | length] | add' "$OUTPUT_FILE")"
```

### Monitoring des changements d'équipes

```bash
#!/bin/bash
# monitor-team-changes.sh - Surveillance des changements d'équipes

ORGANIZATION="mycompany"
WEBHOOK_URL=$1

if [ -z "$WEBHOOK_URL" ]; then
    echo "Usage: $0 <webhook-url>"
    echo "Exemple: $0 https://hooks.slack.com/services/..."
    exit 1
fi

# Configuration du webhook pour les événements d'équipe
gh api orgs/$ORGANIZATION/hooks \
  --method POST \
  --field name="web" \
  --field active=true \
  --field events='["team"]' \
  --field config='{"url":"'$WEBHOOK_URL'","content_type":"json"}'

echo "✅ Monitoring configuré pour les changements d'équipes"
echo "📡 Webhook: $WEBHOOK_URL"
echo "📋 Événements surveillés: team (création, modification, suppression)"
```

## Bonnes pratiques de gestion d'équipes

### Structure d'équipes recommandée

**1. Principe de moindre privilège :**
```bash
# ✅ Bon: permissions minimales nécessaires
gh api repos/OWNER/REPO/teams/qa \
  --method PUT \
  --field permission="triage"  # QA peut gérer issues mais pas modifier code

# ❌ Mauvais: permissions trop larges
gh api repos/OWNER/REPO/teams/qa \
  --method PUT \
  --field permission="admin"   # Trop de permissions
```

**2. Hiérarchie logique :**
```
@company/all-staff (organisation)
├── @company/engineering
│   ├── @company/frontend-team
│   ├── @company/backend-team
│   └── @company/mobile-team
├── @company/product
└── @company/design
```

**3. Conventions de nommage :**
```bash
# ✅ Conventions claires
@company/frontend-team
@company/backend-api
@company/mobile-ios
@company/qa-automation

# ❌ Noms confus
@company/team1
@company/group-a
@company/dev
```

### Sécurisation des accès

**Rotation régulière des accès :**
```bash
#!/bin/bash
# quarterly-access-review.sh

echo "🔄 Révision trimestrielle des accès"

# Lister les membres inactifs (pas de commits récents)
gh api orgs/$ORGANIZATION/members --jq '.[].login' | while read member; do
    LAST_ACTIVITY=$(gh api users/$member/events --jq '.[0].created_at' 2>/dev/null)

    if [ -n "$LAST_ACTIVITY" ]; then
        # Vérifier si activité récente (90 jours)
        if [ $(date -d "$LAST_ACTIVITY" +%s) -lt $(date -d "90 days ago" +%s) ]; then
            echo "⚠️  Membre inactif: $member (dernière activité: $LAST_ACTIVITY)"
        fi
    fi
done
```

**Audit des permissions élevées :**
```bash
#!/bin/bash
# audit-admin-access.sh

echo "🔒 Audit des accès administrateur"

# Lister tous les repos avec les équipes admin
gh api orgs/$ORGANIZATION/repos --paginate --jq '.[].name' | while read repo; do
    ADMIN_TEAMS=$(gh api repos/$ORGANIZATION/$repo/teams --jq '.[] | select(.permission=="admin") | .name')

    if [ -n "$ADMIN_TEAMS" ]; then
        echo "📁 $repo:"
        echo "$ADMIN_TEAMS" | while read team; do
            echo "  👑 Équipe admin: $team"
        done
    fi
done
```

## Points clés à retenir

✅ **Structure hiérarchique** des équipes facilite la gestion et l'héritage des permissions

✅ **Principe de moindre privilège** : accordez seulement les permissions nécessaires

✅ **Automatisation** avec scripts pour gérer efficacement de grandes équipes

✅ **Audit régulier** des permissions et de l'activité des équipes

✅ **CODEOWNERS** pour définir la propriété du code par équipe

✅ **Protection de branches** avec requirements spécifiques par équipe

✅ **Monitoring** des changements pour maintenir la sécurité

## Prochaine étape

Maintenant que vous maîtrisez la gestion des équipes et permissions, nous allons apprendre à optimiser les notifications et mentions pour améliorer la communication au sein de vos équipes.


⏭️
