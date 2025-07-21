🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 4.2 - Assigner et labelliser les issues

## 🎯 Introduction

Créer des issues, c'est bien. Les organiser intelligemment, c'est révolutionnaire ! L'assignation et les labels sont les deux outils qui vont transformer votre chaos d'issues en système d'organisation ultra-efficace. Cette section vous apprendra à maîtriser ces fonctionnalités pour une productivité maximale.

**Analogie** : Si les issues sont vos tâches, les labels sont vos dossiers colorés et les assignations sont vos noms sur les Post-it. Sans organisation, c'est le désordre total !

## 🏷️ Comprendre les labels

### Qu'est-ce qu'un label ?

Un **label** est une **étiquette colorée** que vous pouvez attacher à une issue pour :
- 🎯 **Catégoriser** le type de travail
- 🚨 **Indiquer la priorité**
- 👥 **Identifier l'équipe** responsable
- 📊 **Faciliter le filtrage** et reporting

### Pourquoi les labels sont essentiels

**Sans labels** :
- ❌ Toutes les issues se ressemblent
- ❌ Impossible de prioriser rapidement
- ❌ Difficile de répartir le travail
- ❌ Reporting impossible

**Avec un système de labels** :
- ✅ **Classification claire** de chaque issue
- ✅ **Priorisation visuelle** immédiate
- ✅ **Filtrage rapide** par catégorie
- ✅ **Reporting automatisé** possible

### Types de labels recommandés

#### Labels de type (nature du travail)

```bash
bug          # 🐛 Correction de problème
feature      # ✨ Nouvelle fonctionnalité
enhancement  # 🚀 Amélioration existante
documentation# 📚 Mise à jour documentation
task         # 📋 Tâche organisationnelle
question     # ❓ Demande d'information
```

#### Labels de priorité

```bash
critical     # 🔥 Blocage complet - traiter immédiatement
high         # ⚡ Important et urgent
medium       # 📊 Important mais peut attendre
low          # 🐌 Peut attendre longtemps
```

#### Labels de complexité

```bash
beginner     # 🌱 Parfait pour débuter
intermediate # 🛠️ Nécessite de l'expérience
advanced     # 🎯 Complexe, expertise requise
```

#### Labels de statut

```bash
ready        # ✅ Prêt à être développé
in-progress  # 🔄 En cours de développement
blocked      # 🚫 Bloqué par dépendance externe
needs-review # 👀 Attend validation
```

## 🎨 Gérer les labels avec la CLI

### Voir les labels existants

```bash
# Lister tous les labels du repository
gh label list
```

**Sortie typique** :
```
bug          Something isn't working                     #d73a49
documentation Improvements or additions to documentation  #0075ca
enhancement  New feature or request                      #a2eeef
good first issue Good for newcomers                     #7057ff
help wanted  Extra attention is needed                   #008672
```

### Créer de nouveaux labels

```bash
# Créer un label simple
gh label create "high-priority"

# Créer un label avec description
gh label create "frontend" --description "Interface utilisateur et UX"

# Créer un label avec couleur personnalisée
gh label create "critical" --color "ff0000" --description "Problème critique à traiter immédiatement"
```

**Codes couleur utiles** :
- `ff0000` - Rouge (critique)
- `ffa500` - Orange (priorité haute)
- `ffff00` - Jaune (attention)
- `00ff00` - Vert (prêt/terminé)
- `0080ff` - Bleu (information)
- `800080` - Violet (en cours)

### Modifier des labels existants

```bash
# Changer la description
gh label edit "bug" --description "🐛 Quelque chose ne fonctionne pas"

# Changer la couleur
gh label edit "enhancement" --color "00ff00"

# Renommer un label
gh label edit "old-name" --name "new-name"
```

### Supprimer des labels

```bash
# Supprimer un label
gh label delete "obsolete-label"

# Supprimer avec confirmation automatique
gh label delete "old-label" --yes
```

## 🏷️ Appliquer des labels aux issues

### Ajouter des labels lors de la création

```bash
# Un seul label
gh issue create --title "Corriger le bug de connexion" --label bug

# Plusieurs labels
gh issue create --title "Optimiser la page d'accueil" --label enhancement,performance,frontend

# Avec priorité
gh issue create --title "Sécurité: Faille XSS détectée" --label bug,critical,security
```

### Ajouter des labels à une issue existante

```bash
# Ajouter un label
gh issue edit 42 --add-label "high-priority"

# Ajouter plusieurs labels
gh issue edit 42 --add-label "frontend,ui,mobile"

# Remplacer tous les labels
gh issue edit 42 --label "bug,critical"
```

### Supprimer des labels d'une issue

```bash
# Supprimer un label spécifique
gh issue edit 42 --remove-label "low-priority"

# Supprimer plusieurs labels
gh issue edit 42 --remove-label "old-tag,obsolete"

# Supprimer tous les labels
gh issue edit 42 --label ""
```

## 👥 Comprendre les assignations

### Qu'est-ce que l'assignation ?

L'**assignation** indique **qui est responsable** de traiter une issue. C'est crucial pour :
- 👤 **Clarifier les responsabilités**
- 📊 **Répartir la charge de travail**
- 🎯 **Suivre l'avancement individuel**
- 💬 **Savoir qui contacter** pour des questions

### Types d'assignation

#### Auto-assignation

```bash
# S'assigner une issue
gh issue edit 42 --assignee @me
```

**Quand s'auto-assigner** :
- ✅ Vous allez travailler dessus immédiatement
- ✅ C'est dans votre domaine d'expertise
- ✅ Vous avez le temps disponible

#### Assignation à un collaborateur

```bash
# Assigner à un membre de l'équipe
gh issue edit 42 --assignee alice

# Assigner à plusieurs personnes
gh issue edit 42 --assignee alice,bob
```

**Bonnes pratiques d'assignation** :
- ✅ Assigner selon l'expertise (frontend/backend)
- ✅ Répartir équitablement la charge
- ✅ Considérer la disponibilité des personnes

#### Désassignation

```bash
# Supprimer toute assignation
gh issue edit 42 --assignee ""

# Se désassigner
gh issue edit 42 --remove-assignee @me
```

## 🎯 Workflows d'organisation

### Workflow développeur solo

#### Classification de vos idées

```bash
# Capturer une idée avec classification immédiate
gh issue create \
  --title "Ajouter animations à l'interface" \
  --label "enhancement,frontend,low-priority" \
  --assignee @me

# Signaler un bug critique
gh issue create \
  --title "Site inaccessible en production" \
  --label "bug,critical,production" \
  --assignee @me
```

#### Organisation de votre backlog

```bash
# Voir vos tâches par priorité
gh issue list --assignee @me --label "high-priority" --state open

# Tâches rapides pour combler les temps morts
gh issue list --assignee @me --label "beginner,quick-fix" --state open

# Planification de la semaine
gh issue list --assignee @me --label "ready" --state open --limit 10
```

### Workflow équipe

#### Répartition du travail par expertise

```bash
# Créer une tâche frontend
gh issue create \
  --title "Responsive design pour mobile" \
  --label "frontend,enhancement,mobile" \
  --assignee alice

# Créer une tâche backend
gh issue create \
  --title "Optimiser les requêtes de base de données" \
  --label "backend,performance,database" \
  --assignee bob

# Tâche DevOps
gh issue create \
  --title "Configurer le monitoring" \
  --label "devops,infrastructure,monitoring" \
  --assignee charlie
```

#### Daily standup avec filtrage

```bash
# Ce que fait Alice aujourd'hui
gh issue list --assignee alice --state open --label "in-progress"

# Nouvelles tâches pour Bob
gh issue list --assignee bob --state open --label "ready"

# Blocages de l'équipe
gh issue list --label "blocked" --state open
```

#### Sprint planning

```bash
# Bugs à traiter en priorité
gh issue list --label "bug,high-priority" --state open

# Features prêtes pour le développement
gh issue list --label "feature,ready" --state open

# Tâches pour développeurs juniors
gh issue list --label "beginner,good-first-issue" --state open
```

### Workflow open source

#### Accueil des nouveaux contributeurs

```bash
# Créer des issues pour débutants
gh issue create \
  --title "Améliorer les messages d'erreur" \
  --label "good-first-issue,documentation,beginner" \
  --body "Perfect task for first-time contributors! Improve error messages in the user interface."

# Issues nécessitant de l'aide
gh issue create \
  --title "Implement dark mode" \
  --label "help-wanted,feature,frontend" \
  --body "Community help needed to implement dark mode support."
```

#### Triage des contributions externes

```bash
# Marquer pour triage
gh issue edit 156 --add-label "needs-triage"

# Valider une contribution
gh issue edit 156 --remove-label "needs-triage" --add-label "validated,ready"

# Identifier les doublons
gh issue edit 156 --add-label "duplicate" --assignee @me
```

## 🎨 Système de labels avancé

### Système de labels par projet web

#### Labels techniques

```bash
# Frontend
gh label create "frontend" --color "1f77b4" --description "Interface utilisateur"
gh label create "backend" --color "ff7f0e" --description "Serveur et APIs"
gh label create "database" --color "2ca02c" --description "Base de données"
gh label create "devops" --color "d62728" --description "Infrastructure et déploiement"

# Technologies spécifiques
gh label create "react" --color "61dafb" --description "Composants React"
gh label create "nodejs" --color "339933" --description "Backend Node.js"
gh label create "css" --color "1572b6" --description "Styles et mise en page"
```

#### Labels fonctionnels

```bash
# Par domaine métier
gh label create "auth" --color "ff9800" --description "Authentification et sécurité"
gh label create "payment" --color "4caf50" --description "Système de paiement"
gh label create "notification" --color "9c27b0" --description "Notifications utilisateur"
gh label create "analytics" --color "607d8b" --description "Métriques et analyse"
```

#### Labels de processus

```bash
# Workflow de développement
gh label create "needs-design" --color "e91e63" --description "Attend maquettes/designs"
gh label create "needs-api" --color "ff5722" --description "Attend API backend"
gh label create "needs-testing" --color "795548" --description "Attend tests"
gh label create "needs-review" --color "3f51b5" --description "Attend code review"
```

### Système de labels par équipe

#### Labels par domaine d'expertise

```bash
# Équipes
gh label create "team-frontend" --color "1f77b4" --description "Équipe frontend"
gh label create "team-backend" --color "ff7f0e" --description "Équipe backend"
gh label create "team-design" --color "e91e63" --description "Équipe design/UX"
gh label create "team-qa" --color "9c27b0" --description "Équipe qualité"

# Rôles spécifiques
gh label create "senior-review" --color "ffc107" --description "Nécessite review senior"
gh label create "architect-input" --color "673ab7" --description "Avis architecte requis"
```

### Automation avec les labels

#### Script de labellisation automatique

```bash
#!/bin/bash
# auto-label.sh

echo "🏷️ Labellisation automatique des issues..."

# Labelliser les bugs selon les mots-clés
gh issue list --search "crash OR error OR bug OR broken" --state open --json number,title | \
  jq -r '.[] | "\(.number)|\(.title)"' | \
  while IFS='|' read -r number title; do
    if [[ $title =~ (crash|error) ]]; then
      echo "🐛 Issue #$number identifiée comme bug critique"
      gh issue edit "$number" --add-label "bug,high-priority"
    else
      echo "🐛 Issue #$number identifiée comme bug"
      gh issue edit "$number" --add-label "bug"
    fi
  done

# Labelliser les demandes de fonctionnalités
gh issue list --search "feature OR add OR implement" --state open --json number,title | \
  jq -r '.[] | "\(.number)|\(.title)"' | \
  while IFS='|' read -r number title; do
    echo "✨ Issue #$number identifiée comme feature"
    gh issue edit "$number" --add-label "feature"
  done

echo "✅ Labellisation automatique terminée"
```

#### Script de répartition automatique

```bash
#!/bin/bash
# auto-assign.sh

echo "👥 Répartition automatique des tâches..."

# Définir les spécialités
FRONTEND_DEVS=("alice" "bob")
BACKEND_DEVS=("charlie" "diana")
DEVOPS_DEVS=("eve")

# Assigner les tâches frontend
gh issue list --label "frontend" --assignee "" --state open --json number | \
  jq -r '.[].number' | \
  while read number; do
    # Rotation entre les développeurs frontend
    dev_index=$((RANDOM % ${#FRONTEND_DEVS[@]}))
    dev=${FRONTEND_DEVS[$dev_index]}
    echo "🎨 Assignation issue #$number à $dev (frontend)"
    gh issue edit "$number" --assignee "$dev"
  done

# Assigner les tâches backend
gh issue list --label "backend" --assignee "" --state open --json number | \
  jq -r '.[].number' | \
  while read number; do
    dev_index=$((RANDOM % ${#BACKEND_DEVS[@]}))
    dev=${BACKEND_DEVS[$dev_index]}
    echo "⚙️ Assignation issue #$number à $dev (backend)"
    gh issue edit "$number" --assignee "$dev"
  done

echo "✅ Répartition automatique terminée"
```

## 📊 Reporting et analyse

### Analyse de la répartition des tâches

```bash
#!/bin/bash
# team-workload.sh

echo "📊 Analyse de la charge de travail"
echo "================================="

# Issues par assigné
echo ""
echo "👥 Issues par personne :"
gh issue list --state open --json assignees | \
  jq -r '.[] | .assignees[]?.login // "non-assigné"' | \
  sort | uniq -c | sort -nr

# Issues par label
echo ""
echo "🏷️ Issues par type :"
gh issue list --state open --json labels | \
  jq -r '.[] | .labels[]?.name' | \
  grep -E "(bug|feature|enhancement|documentation)" | \
  sort | uniq -c | sort -nr

# Issues par priorité
echo ""
echo "🚨 Issues par priorité :"
gh issue list --state open --json labels | \
  jq -r '.[] | .labels[]?.name' | \
  grep -E "(critical|high|medium|low)" | \
  sort | uniq -c | sort -nr
```

### Dashboard de projet

```bash
#!/bin/bash
# project-dashboard.sh

echo "📈 Dashboard du projet - $(date +%Y-%m-%d)"
echo "========================================="

# Métriques générales
TOTAL_OPEN=$(gh issue list --state open --json number | jq length)
TOTAL_CLOSED=$(gh issue list --state closed --json number | jq length)
CRITICAL_BUGS=$(gh issue list --label "bug,critical" --state open --json number | jq length)

echo ""
echo "📊 Métriques générales :"
echo "  Issues ouvertes: $TOTAL_OPEN"
echo "  Issues fermées: $TOTAL_CLOSED"
echo "  Bugs critiques: $CRITICAL_BUGS"

# Top priorités
echo ""
echo "🔥 Top priorités (critical + high) :"
gh issue list --label "critical" --state open --limit 3 --json number,title | \
  jq -r '.[] | "#\(.number) - \(.title)"'

gh issue list --label "high" --state open --limit 3 --json number,title | \
  jq -r '.[] | "#\(.number) - \(.title)"'

# Équipe la plus chargée
echo ""
echo "👥 Charge par équipe :"
echo "  Frontend: $(gh issue list --label 'frontend' --state open --json number | jq length) issues"
echo "  Backend: $(gh issue list --label 'backend' --state open --json number | jq length) issues"
echo "  DevOps: $(gh issue list --label 'devops' --state open --json number | jq length) issues"

# Issues anciennes
OLD_ISSUES=$(gh issue list --search "created:<$(date -d '30 days ago' +%Y-%m-%d)" --state open --json number | jq length)
echo ""
echo "⏰ Issues anciennes (>30 jours): $OLD_ISSUES"
if [ "$OLD_ISSUES" -gt 0 ]; then
  echo "   ⚠️ Attention: Backlog qui s'accumule"
fi
```

## 🎯 Bonnes pratiques d'organisation

### Règles de labellisation

#### Labels obligatoires

**Chaque issue doit avoir** :
1. **Un type** : `bug`, `feature`, `enhancement`, `documentation`, `task`
2. **Une priorité** : `critical`, `high`, `medium`, `low`
3. **Un domaine** : `frontend`, `backend`, `design`, `devops`

```bash
# Vérifier les issues sans labels obligatoires
gh issue list --search "-label:bug -label:feature -label:enhancement -label:documentation -label:task" --state open
```

#### Conventions de nommage

**Labels techniques** :
- Préfixe par domaine : `frontend:`, `backend:`, `design:`
- Couleurs cohérentes par catégorie
- Descriptions claires et utiles

```bash
# Exemple de système cohérent
gh label create "frontend:ui" --color "1f77b4" --description "Interface utilisateur"
gh label create "frontend:mobile" --color "1f77b4" --description "Responsive mobile"
gh label create "backend:api" --color "ff7f0e" --description "APIs REST"
gh label create "backend:database" --color "ff7f0e" --description "Base de données"
```

### Règles d'assignation

#### Assignation immédiate

```bash
# Issues critiques : assigner immédiatement
gh issue create \
  --title "Production en panne" \
  --label "bug,critical,production" \
  --assignee @me

# Issues pour débuter : laisser non-assignées
gh issue create \
  --title "Améliorer les messages d'aide" \
  --label "enhancement,beginner,good-first-issue"
```

#### Rotation équitable

```bash
# Script de rotation équitable
#!/bin/bash
# fair-rotation.sh

TEAM_MEMBERS=("alice" "bob" "charlie" "diana")
ROTATION_FILE=".rotation_index"

# Lire l'index de rotation
if [ -f "$ROTATION_FILE" ]; then
  INDEX=$(cat "$ROTATION_FILE")
else
  INDEX=0
fi

# Assigner à la personne suivante
ASSIGNEE=${TEAM_MEMBERS[$INDEX]}
echo "Assignation à: $ASSIGNEE"

# Mettre à jour l'index pour la prochaine fois
NEXT_INDEX=$(( (INDEX + 1) % ${#TEAM_MEMBERS[@]} ))
echo "$NEXT_INDEX" > "$ROTATION_FILE"

# Assigner l'issue
gh issue edit "$1" --assignee "$ASSIGNEE"
```

## 📋 Récapitulatif des commandes essentielles

### Gestion des labels

```bash
# Créer des labels
gh label create "nom" --color "ff0000" --description "Description"

# Lister les labels
gh label list

# Modifier un label
gh label edit "nom" --description "Nouvelle description"

# Supprimer un label
gh label delete "nom"
```

### Application des labels

```bash
# Ajouter lors de la création
gh issue create --title "Titre" --label "bug,high-priority"

# Ajouter à une issue existante
gh issue edit 42 --add-label "frontend"

# Supprimer un label
gh issue edit 42 --remove-label "old-label"

# Remplacer tous les labels
gh issue edit 42 --label "bug,critical"
```

### Gestion des assignations

```bash
# S'assigner
gh issue edit 42 --assignee @me

# Assigner à quelqu'un
gh issue edit 42 --assignee alice

# Assigner à plusieurs personnes
gh issue edit 42 --assignee alice,bob

# Désassigner
gh issue edit 42 --assignee ""
```

### Filtrage par labels et assignations

```bash
# Filtrer par label
gh issue list --label "bug"

# Filtrer par assigné
gh issue list --assignee alice

# Combinaisons
gh issue list --label "bug,high-priority" --assignee @me --state open
```

## 🎯 Prochaine étape

Excellent ! Vous savez maintenant organiser vos issues comme un pro avec les labels et assignations. Il est temps d'apprendre à gérer leur cycle de vie complet : fermeture, réouverture, et maintien d'un historique propre.

👉 **Continuez avec** : [Fermer et rouvrir des issues](03-fermer-et-rouvrir-des-issues.md)

---

*Vous avez terminé la section 4.2 ! Vos issues sont maintenant parfaitement organisées et structurées. Dans la prochaine section, nous verrons comment gérer leur cycle de vie de bout en bout.*

⏭️
