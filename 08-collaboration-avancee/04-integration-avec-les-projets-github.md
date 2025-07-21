🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 8.4 Intégration avec les projets GitHub

## Introduction

Les projets GitHub (GitHub Projects) sont des tableaux de bord flexibles qui permettent d'organiser, suivre et gérer le travail à travers vos repositories. Combinés avec la CLI GitHub, ils deviennent des outils puissants pour coordonner des équipes, planifier des releases et maintenir une visibilité complète sur l'avancement des projets.

Dans cette section, nous allons apprendre à créer, gérer et automatiser des projets GitHub pour optimiser la collaboration et la productivité de votre équipe.

## Comprendre les projets GitHub

### Qu'est-ce qu'un projet GitHub ?

Un **projet GitHub** est un tableau de bord personnalisable qui peut contenir :

- 📋 **Issues** de un ou plusieurs repositories
- 🔄 **Pull requests** en cours de review
- 📝 **Notes** et idées non liées à du code
- 🎯 **Milestones** et objectifs
- 📊 **Métriques** et rapports de progression

### Types de projets

**Projets de repository :**
- Liés à un repository spécifique
- Visibles par tous les contributeurs du repo
- Idéals pour la gestion de fonctionnalités

**Projets d'organisation :**
- Couvrent plusieurs repositories
- Contrôle d'accès granulaire
- Parfaits pour la coordination d'équipes

**Projets personnels :**
- Privés à un utilisateur
- Gestion de tâches personnelles
- Planning individuel

### Vues et layouts

**Tableau (Board) :**
```
📋 To Do    |  🔄 In Progress  |  ✅ Done
Issue #123  |   PR #456        |  Issue #789
Feature A   |   Bug fix B      |  Feature C
Note: ...   |   Issue #101     |  PR #202
```

**Table :**
```
Title       | Status      | Assignee | Priority | Due Date
Issue #123  | To Do       | @alice   | High     | 2024-07-25
PR #456     | In Progress | @bob     | Medium   | 2024-07-22
```

**Timeline :**
```
July 2024
Week 1: ████████░░ Feature A (80%)
Week 2: ██████████ Bug fixes (100%)
Week 3: ████░░░░░░ Feature B (40%)
```

## Gestion des projets avec la CLI

### Lister les projets existants

```bash
# Projets d'une organisation
gh project list --owner ORGANIZATION

# Projets d'un utilisateur
gh project list --owner USERNAME

# Projets avec détails
gh api graphql -f query='
{
  organization(login: "ORGANIZATION") {
    projectsV2(first: 10) {
      nodes {
        id
        title
        shortDescription
        url
        closed
        createdAt
      }
    }
  }
}' --jq '.data.organization.projectsV2.nodes'
```

### Créer un nouveau projet

```bash
# Créer un projet d'organisation
gh project create --owner ORGANIZATION --title "Q3 2024 Roadmap" --body "Planification du trimestre Q3"

# Créer un projet avec template
gh api graphql -f query='
mutation {
  createProjectV2(input: {
    ownerId: "ORGANIZATION_ID"
    title: "Sprint Planning"
    repositoryId: "REPO_ID"
  }) {
    projectV2 {
      id
      url
    }
  }
}'
```

### Obtenir les détails d'un projet

```bash
# Informations de base
gh project view PROJECT_NUMBER --owner ORGANIZATION

# Structure complète via GraphQL
gh api graphql -f query='
{
  organization(login: "ORGANIZATION") {
    projectV2(number: PROJECT_NUMBER) {
      id
      title
      shortDescription
      readme
      closed
      fields(first: 20) {
        nodes {
          ... on ProjectV2Field {
            id
            name
            dataType
          }
          ... on ProjectV2SingleSelectField {
            id
            name
            options {
              id
              name
              color
            }
          }
        }
      }
    }
  }
}' --jq '.data.organization.projectV2'
```

## Gestion des éléments de projet

### Ajouter des issues au projet

```bash
# Ajouter une issue existante
gh project item-add PROJECT_NUMBER --owner ORGANIZATION --url https://github.com/OWNER/REPO/issues/123

# Ajouter plusieurs issues
for issue in 123 124 125; do
    gh project item-add PROJECT_NUMBER --owner ORGANIZATION --url https://github.com/OWNER/REPO/issues/$issue
done
```

### Ajouter des pull requests

```bash
# Ajouter une PR
gh project item-add PROJECT_NUMBER --owner ORGANIZATION --url https://github.com/OWNER/REPO/pull/456

# Script pour ajouter toutes les PR ouvertes
gh pr list --repo OWNER/REPO --json url --jq '.[].url' | while read pr_url; do
    gh project item-add PROJECT_NUMBER --owner ORGANIZATION --url "$pr_url"
done
```

### Créer et ajouter des notes

```bash
# Créer une note dans le projet
gh api graphql -f query='
mutation {
  addProjectV2DraftIssue(input: {
    projectId: "PROJECT_ID"
    title: "Recherche sur la nouvelle architecture"
    body: "Explorer les options pour refactorer le backend"
  }) {
    projectItem {
      id
    }
  }
}'
```

## Automatisation des projets

### Script de configuration de projet complet

```bash
#!/bin/bash
# setup-project.sh - Configuration complète d'un projet

ORGANIZATION=$1
PROJECT_TITLE=$2
REPOS=($3)

if [ -z "$ORGANIZATION" ] || [ -z "$PROJECT_TITLE" ]; then
    echo "Usage: $0 <organization> <project-title> <repos>"
    echo "Exemple: $0 myorg 'Q3 Planning' 'repo1,repo2,repo3'"
    exit 1
fi

echo "🚀 Configuration du projet: $PROJECT_TITLE"

# 1. Créer le projet
echo "📋 Création du projet..."
PROJECT_URL=$(gh project create --owner "$ORGANIZATION" --title "$PROJECT_TITLE" --body "Projet créé automatiquement" --format json | jq -r '.url')
PROJECT_NUMBER=$(basename "$PROJECT_URL")

echo "✅ Projet créé: $PROJECT_URL"

# 2. Configurer les champs personnalisés
echo "⚙️  Configuration des champs..."

# Priority field
gh api graphql -f query="
mutation {
  createProjectV2Field(input: {
    projectId: \"$(gh project view $PROJECT_NUMBER --owner $ORGANIZATION --format json | jq -r '.id')\"
    dataType: SINGLE_SELECT
    name: \"Priority\"
    singleSelectOptions: [
      {name: \"🔴 Critical\", color: RED}
      {name: \"🟠 High\", color: ORANGE}
      {name: \"🟡 Medium\", color: YELLOW}
      {name: \"🟢 Low\", color: GREEN}
    ]
  }) {
    projectV2Field {
      id
    }
  }
}"

# Effort field
gh api graphql -f query="
mutation {
  createProjectV2Field(input: {
    projectId: \"$(gh project view $PROJECT_NUMBER --owner $ORGANIZATION --format json | jq -r '.id')\"
    dataType: SINGLE_SELECT
    name: \"Effort\"
    singleSelectOptions: [
      {name: \"XS (1 day)\", color: GREEN}
      {name: \"S (2-3 days)\", color: YELLOW}
      {name: \"M (1 week)\", color: ORANGE}
      {name: \"L (2-3 weeks)\", color: RED}
      {name: \"XL (1+ month)\", color: PURPLE}
    ]
  }) {
    projectV2Field {
      id
    }
  }
}"

# 3. Ajouter les issues existantes des repositories
echo "📥 Ajout des issues existantes..."
IFS=',' read -ra REPO_ARRAY <<< "$REPOS"
for repo in "${REPO_ARRAY[@]}"; do
    echo "  Processing $ORGANIZATION/$repo..."

    # Ajouter toutes les issues ouvertes
    gh issue list --repo "$ORGANIZATION/$repo" --json url --jq '.[].url' | while read issue_url; do
        gh project item-add $PROJECT_NUMBER --owner $ORGANIZATION --url "$issue_url" &>/dev/null
    done

    # Ajouter toutes les PR ouvertes
    gh pr list --repo "$ORGANIZATION/$repo" --json url --jq '.[].url' | while read pr_url; do
        gh project item-add $PROJECT_NUMBER --owner $ORGANIZATION --url "$pr_url" &>/dev/null
    done
done

# 4. Créer les vues par défaut
echo "👁️  Création des vues..."

# Vue Kanban par statut
gh api graphql -f query="
mutation {
  createProjectV2View(input: {
    projectId: \"$(gh project view $PROJECT_NUMBER --owner $ORGANIZATION --format json | jq -r '.id')\"
    name: \"Board\"
    layout: BOARD_LAYOUT
  }) {
    projectV2View {
      id
    }
  }
}"

# Vue Table pour triage
gh api graphql -f query="
mutation {
  createProjectV2View(input: {
    projectId: \"$(gh project view $PROJECT_NUMBER --owner $ORGANIZATION --format json | jq -r '.id')\"
    name: \"Triage\"
    layout: TABLE_LAYOUT
  }) {
    projectV2View {
      id
    }
  }
}"

echo "✅ Projet configuré avec succès !"
echo "🔗 URL: $PROJECT_URL"
```

### Synchronisation automatique

```bash
#!/bin/bash
# sync-project.sh - Synchronisation automatique des projets

ORGANIZATION=$1
PROJECT_NUMBER=$2

if [ -z "$ORGANIZATION" ] || [ -z "$PROJECT_NUMBER" ]; then
    echo "Usage: $0 <organization> <project-number>"
    exit 1
fi

echo "🔄 Synchronisation du projet $PROJECT_NUMBER"

# Obtenir l'ID du projet
PROJECT_ID=$(gh project view $PROJECT_NUMBER --owner $ORGANIZATION --format json | jq -r '.id')

# 1. Ajouter les nouvelles issues avec label "needs-project"
echo "📋 Ajout des nouvelles issues..."
gh api "orgs/$ORGANIZATION/issues" --paginate --jq '.[] | select(.labels[].name == "needs-project") | .html_url' | while read issue_url; do
    echo "  Ajout: $issue_url"
    gh project item-add $PROJECT_NUMBER --owner $ORGANIZATION --url "$issue_url"

    # Retirer le label après ajout
    issue_number=$(basename "$issue_url")
    repo_path=$(echo "$issue_url" | sed 's|https://github.com/||' | sed 's|/issues/.*||')
    gh issue edit $issue_number --repo $repo_path --remove-label "needs-project"
done

# 2. Mettre à jour le statut des items fermés
echo "✅ Mise à jour des items fermés..."
gh api graphql -f query="
{
  organization(login: \"$ORGANIZATION\") {
    projectV2(number: $PROJECT_NUMBER) {
      items(first: 100) {
        nodes {
          id
          content {
            ... on Issue {
              number
              state
              repository {
                name
              }
            }
            ... on PullRequest {
              number
              state
              repository {
                name
              }
            }
          }
        }
      }
    }
  }
}" | jq -r '.data.organization.projectV2.items.nodes[] | select(.content.state == "CLOSED" or .content.state == "MERGED") | .id' | while read item_id; do
    echo "  Fermé: $item_id"

    # Déplacer vers "Done" (nécessite l'ID du champ Status)
    gh api graphql -f query="
    mutation {
      updateProjectV2ItemFieldValue(input: {
        projectId: \"$PROJECT_ID\"
        itemId: \"$item_id\"
        fieldId: \"STATUS_FIELD_ID\"
        value: {
          singleSelectOptionId: \"DONE_OPTION_ID\"
        }
      }) {
        projectV2Item {
          id
        }
      }
    }"
done

echo "✅ Synchronisation terminée"
```

## Workflows avancés de gestion de projet

### Automatisation basée sur les labels

```bash
#!/bin/bash
# label-automation.sh - Automatisation basée sur les labels

ORGANIZATION=$1
PROJECT_NUMBER=$2

# Mapping label -> propriétés du projet
declare -A LABEL_PRIORITY
LABEL_PRIORITY[critical]="🔴 Critical"
LABEL_PRIORITY[high]="🟠 High"
LABEL_PRIORITY[medium]="🟡 Medium"
LABEL_PRIORITY[low]="🟢 Low"

declare -A LABEL_EFFORT
LABEL_EFFORT[effort/xs]="XS (1 day)"
LABEL_EFFORT[effort/s]="S (2-3 days)"
LABEL_EFFORT[effort/m]="M (1 week)"
LABEL_EFFORT[effort/l]="L (2-3 weeks)"
LABEL_EFFORT[effort/xl]="XL (1+ month)"

echo "🏷️  Automatisation basée sur les labels"

# Obtenir les IDs des champs
PROJECT_ID=$(gh project view $PROJECT_NUMBER --owner $ORGANIZATION --format json | jq -r '.id')

# Fonction pour mettre à jour un champ
update_project_field() {
    local item_id=$1
    local field_id=$2
    local option_id=$3

    gh api graphql -f query="
    mutation {
      updateProjectV2ItemFieldValue(input: {
        projectId: \"$PROJECT_ID\"
        itemId: \"$item_id\"
        fieldId: \"$field_id\"
        value: {
          singleSelectOptionId: \"$option_id\"
        }
      }) {
        projectV2Item {
          id
        }
      }
    }" &>/dev/null
}

# Traiter tous les items du projet
gh api graphql -f query="
{
  organization(login: \"$ORGANIZATION\") {
    projectV2(number: $PROJECT_NUMBER) {
      items(first: 100) {
        nodes {
          id
          content {
            ... on Issue {
              number
              labels(first: 20) {
                nodes {
                  name
                }
              }
              repository {
                name
              }
            }
          }
        }
      }
    }
  }
}" | jq -r '.data.organization.projectV2.items.nodes[] | "\(.id)|\(.content.repository.name)|\(.content.number)|\(.content.labels.nodes[].name)"' | while IFS='|' read -r item_id repo issue_number labels; do

    echo "📋 Processing: $repo#$issue_number"

    # Traiter les labels de priorité
    for label in $labels; do
        if [[ -n "${LABEL_PRIORITY[$label]}" ]]; then
            echo "  🎯 Priorité: ${LABEL_PRIORITY[$label]}"
            # update_project_field "$item_id" "$PRIORITY_FIELD_ID" "$PRIORITY_OPTION_ID"
        fi

        if [[ -n "${LABEL_EFFORT[$label]}" ]]; then
            echo "  ⚡ Effort: ${LABEL_EFFORT[$label]}"
            # update_project_field "$item_id" "$EFFORT_FIELD_ID" "$EFFORT_OPTION_ID"
        fi
    done
done
```

### Reporting et métriques

```bash
#!/bin/bash
# project-metrics.sh - Métriques et rapports de projet

ORGANIZATION=$1
PROJECT_NUMBER=$2

if [ -z "$ORGANIZATION" ] || [ -z "$PROJECT_NUMBER" ]; then
    echo "Usage: $0 <organization> <project-number>"
    exit 1
fi

echo "📊 Rapport de métriques - Projet $PROJECT_NUMBER"
echo "================================================"

# Obtenir les données du projet
PROJECT_DATA=$(gh api graphql -f query="
{
  organization(login: \"$ORGANIZATION\") {
    projectV2(number: $PROJECT_NUMBER) {
      title
      items(first: 100) {
        totalCount
        nodes {
          content {
            ... on Issue {
              state
              createdAt
              closedAt
              labels(first: 10) {
                nodes {
                  name
                }
              }
            }
            ... on PullRequest {
              state
              createdAt
              mergedAt
              closedAt
            }
          }
        }
      }
    }
  }
}")

PROJECT_TITLE=$(echo "$PROJECT_DATA" | jq -r '.data.organization.projectV2.title')
TOTAL_ITEMS=$(echo "$PROJECT_DATA" | jq -r '.data.organization.projectV2.items.totalCount')

echo "📋 Projet: $PROJECT_TITLE"
echo "📈 Total des items: $TOTAL_ITEMS"
echo ""

# Statistiques par statut
echo "📊 Répartition par statut:"
OPEN_ISSUES=$(echo "$PROJECT_DATA" | jq '[.data.organization.projectV2.items.nodes[].content | select(.state == "OPEN")] | length')
CLOSED_ISSUES=$(echo "$PROJECT_DATA" | jq '[.data.organization.projectV2.items.nodes[].content | select(.state == "CLOSED")] | length')
MERGED_PRS=$(echo "$PROJECT_DATA" | jq '[.data.organization.projectV2.items.nodes[].content | select(.state == "MERGED")] | length')

echo "  🟢 Ouvert: $OPEN_ISSUES"
echo "  ✅ Fermé: $CLOSED_ISSUES"
echo "  🔀 Mergé: $MERGED_PRS"

# Taux de completion
if [ $TOTAL_ITEMS -gt 0 ]; then
    COMPLETION_RATE=$(echo "scale=1; ($CLOSED_ISSUES + $MERGED_PRS) * 100 / $TOTAL_ITEMS" | bc)
    echo "  📈 Taux de completion: $COMPLETION_RATE%"
fi

echo ""

# Analyse des labels (pour les issues)
echo "🏷️  Top des labels:"
echo "$PROJECT_DATA" | jq -r '.data.organization.projectV2.items.nodes[].content.labels.nodes[]?.name' | sort | uniq -c | sort -nr | head -5 | while read count label; do
    echo "  $label: $count"
done

echo ""

# Métriques temporelles
echo "⏱️  Métriques temporelles:"
CURRENT_DATE=$(date +%s)
WEEK_AGO=$((CURRENT_DATE - 604800))

RECENT_CREATED=$(echo "$PROJECT_DATA" | jq --arg week_ago "$(date -d '@'$WEEK_AGO +%Y-%m-%dT%H:%M:%SZ)" '[.data.organization.projectV2.items.nodes[].content | select(.createdAt > $week_ago)] | length')
RECENT_CLOSED=$(echo "$PROJECT_DATA" | jq --arg week_ago "$(date -d '@'$WEEK_AGO +%Y-%m-%dT%H:%M:%SZ)" '[.data.organization.projectV2.items.nodes[].content | select(.closedAt > $week_ago or .mergedAt > $week_ago)] | length')

echo "  📅 Créés cette semaine: $RECENT_CREATED"
echo "  ✅ Fermés cette semaine: $RECENT_CLOSED"

# Velocity (items fermés par semaine)
if [ $RECENT_CLOSED -gt 0 ]; then
    echo "  🚀 Vélocité: $RECENT_CLOSED items/semaine"
fi
```

## Intégration avec les workflows

### Automation GitHub Actions

```yaml
# .github/workflows/project-automation.yml
name: Project Automation

on:
  issues:
    types: [opened, closed, labeled]
  pull_request:
    types: [opened, closed, merged]

jobs:
  update-project:
    runs-on: ubuntu-latest
    steps:
      - name: Add to project
        if: github.event.action == 'opened'
        run: |
          gh project item-add ${{ vars.PROJECT_NUMBER }} \
            --owner ${{ github.repository_owner }} \
            --url ${{ github.event.issue.html_url || github.event.pull_request.html_url }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      - name: Move to Done
        if: github.event.action == 'closed' || github.event.action == 'merged'
        run: |
          # Script pour déplacer l'item vers "Done"
          ./scripts/move-to-done.sh ${{ github.event.issue.number || github.event.pull_request.number }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Webhook pour synchronisation temps réel

```bash
#!/bin/bash
# setup-project-webhook.sh - Configuration webhook pour projets

ORGANIZATION=$1
WEBHOOK_URL=$2

if [ -z "$ORGANIZATION" ] || [ -z "$WEBHOOK_URL" ]; then
    echo "Usage: $0 <organization> <webhook-url>"
    exit 1
fi

echo "🔗 Configuration webhook pour projets"

# Configurer le webhook au niveau organisation
gh api orgs/$ORGANIZATION/hooks \
  --method POST \
  --field name="web" \
  --field active=true \
  --field events='["issues","pull_request","project","project_card"]' \
  --field config="{
    \"url\":\"$WEBHOOK_URL\",
    \"content_type\":\"json\",
    \"secret\":\"$WEBHOOK_SECRET\"
  }"

echo "✅ Webhook configuré pour la synchronisation automatique"
```

## Points clés à retenir

✅ **Projets GitHub** offrent une vue unifiée du travail à travers plusieurs repositories

✅ **Automatisation CLI** permet de maintenir les projets synchronisés sans effort manuel

✅ **Champs personnalisés** ajoutent du contexte et permettent un suivi granulaire

✅ **Vues multiples** (Board, Table, Timeline) s'adaptent aux différents besoins

✅ **Intégration workflows** avec GitHub Actions automatise complètement la gestion

✅ **Métriques et rapports** fournissent des insights sur la performance de l'équipe

✅ **Scripts réutilisables** standardisent la configuration et maintenance des projets

## Conclusion de la section Collaboration Avancée

Félicitations ! Vous maîtrisez maintenant tous les aspects avancés de la collaboration avec GitHub :

- 👥 **Gestion d'équipes** avec permissions granulaires et sécurisées
- 🔔 **Notifications intelligentes** pour une communication optimisée
- 📝 **Templates standardisés** pour améliorer la qualité des contributions
- 📋 **Projets intégrés** pour coordonner le travail d'équipe efficacement

Ces compétences vous permettront de créer un environnement de collaboration moderne, scalable et productif pour des équipes de toute taille.


⏭️
