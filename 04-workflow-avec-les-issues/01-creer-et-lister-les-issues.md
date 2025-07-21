🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 4.1 - Créer et lister les issues (`gh issue create`, `gh issue list`)

## 🎯 Introduction

Les issues sont le cœur de l'organisation de vos projets GitHub. Apprendre à les créer efficacement et à naviguer dans votre backlog est la première étape vers une productivité décuplée. Cette section vous apprendra tout ce qu'il faut savoir sur `gh issue create` et `gh issue list` - les deux commandes qui transformeront votre façon de travailler.

**Analogie** : Si votre projet était un restaurant, les issues seraient votre système de commandes - chaque plat (tâche) y est noté avec ses détails, son statut et sa priorité !

## 📝 Qu'est-ce qu'une issue GitHub ?

### Définition simple

Une **issue** est un élément de suivi qui représente :
- 🐛 **Un bug** à corriger
- ✨ **Une fonctionnalité** à développer
- 📚 **Une amélioration** à apporter
- ❓ **Une question** à élucider
- 📋 **Une tâche** à accomplir

### Structure d'une issue

Chaque issue contient :
- **Titre** : Description courte et claire
- **Corps** : Description détaillée (optionnelle)
- **Labels** : Tags pour catégoriser
- **Assignés** : Qui travaille dessus
- **Milestone** : À quelle version/sprint elle appartient
- **État** : Ouverte ou fermée

### Pourquoi utiliser les issues ?

**Avantages concrets** :
- ✅ **Mémoire externalisée** : Plus rien n'est oublié
- ✅ **Priorisation claire** : Vous savez quoi faire en premier
- ✅ **Collaboration facilitée** : Discussions centralisées
- ✅ **Traçabilité** : Historique de toutes les décisions
- ✅ **Motivation** : Satisfaction de fermer les issues

## 🎨 Créer des issues avec `gh issue create`

### Syntaxe de base

```bash
gh issue create
```

**Cette commande simple** lance un mode interactif qui vous guide pour créer votre issue.

### Mode interactif vs mode direct

#### Mode interactif (recommandé pour débuter)

```bash
gh issue create
```

**Ce qui se passe** :
1. CLI vous demande le titre
2. CLI ouvre votre éditeur pour le corps
3. CLI vous propose d'ajouter des labels
4. CLI vous propose d'assigner quelqu'un
5. Issue créée automatiquement

#### Mode direct (plus rapide)

```bash
gh issue create --title "Titre de l'issue" --body "Description détaillée"
```

### Création d'issues simples

#### Issue basique

```bash
gh issue create --title "Corriger le bug du bouton de connexion"
```

**Résultat** : Issue créée avec juste un titre, corps vide.

#### Issue avec description

```bash
gh issue create \
  --title "Ajouter support du mode sombre" \
  --body "Les utilisateurs demandent un mode sombre pour réduire la fatigue oculaire en soirée."
```

#### Issue depuis un fichier

```bash
# Créer un fichier avec la description
echo "## Description
L'application plante quand on clique sur 'Sauvegarder'.

## Étapes pour reproduire
1. Ouvrir l'application
2. Remplir le formulaire
3. Cliquer sur 'Sauvegarder'
4. L'app se ferme

## Comportement attendu
L'application devrait sauvegarder et afficher un message de confirmation." > bug-report.md

# Créer l'issue depuis le fichier
gh issue create --title "Bug: Application plante à la sauvegarde" --body-file bug-report.md
```

### Types d'issues courantes

#### Issues de bug

```bash
gh issue create \
  --title "Bug: Le carrousel d'images ne fonctionne pas sur mobile" \
  --body "## Problème
Le carrousel d'images ne répond pas aux gestes tactiles sur les appareils mobiles.

## Environnement
- Device: iPhone 12
- Browser: Safari 15.0
- OS: iOS 15.2

## Étapes pour reproduire
1. Ouvrir le site sur mobile
2. Aller à la galerie
3. Essayer de faire défiler les images
4. Rien ne se passe

## Comportement attendu
Les images devraient défiler avec les gestes tactiles."
```

#### Issues de fonctionnalité

```bash
gh issue create \
  --title "Feature: Ajouter une fonction de recherche" \
  --body "## Besoin utilisateur
Les utilisateurs ont besoin de pouvoir rechercher rapidement dans le contenu.

## Description de la fonctionnalité
Ajouter une barre de recherche en haut de page qui permet de :
- Rechercher dans les titres
- Rechercher dans le contenu
- Filtrer par catégorie
- Afficher les résultats avec highlighting

## Critères d'acceptation
- [ ] Barre de recherche visible
- [ ] Recherche en temps réel
- [ ] Résultats pertinents
- [ ] Interface mobile-friendly"
```

#### Issues de documentation

```bash
gh issue create \
  --title "Docs: Mettre à jour le guide d'installation" \
  --body "Le guide d'installation ne mentionne pas les nouvelles dépendances Node.js 18+ requises depuis la v2.0.

À mettre à jour :
- [ ] Prérequis système
- [ ] Instructions d'installation
- [ ] Troubleshooting commun
- [ ] Exemples de configuration"
```

#### Issues de tâche

```bash
gh issue create \
  --title "Tâche: Préparer la présentation demo pour le client" \
  --body "## Contexte
Présentation prévue vendredi 15 décembre à 14h.

## Éléments à préparer
- [ ] Slides de présentation
- [ ] Démo de l'interface utilisateur
- [ ] Métriques de performance
- [ ] Questions/réponses probables
- [ ] Backup plan si problème technique

## Deadline
Jeudi 14 décembre 18h (pour révision équipe)"
```

## 📋 Lister et naviguer dans les issues avec `gh issue list`

### Syntaxe de base

```bash
gh issue list
```

**Affichage par défaut** :
```
Showing 30 of 156 issues in username/repository

#123  feat: Add dark mode support          feature, enhancement    about 2 hours ago
#122  bug: Login button not working        bug, high-priority      about 1 day ago
#121  docs: Update installation guide      documentation           about 3 days ago
#120  task: Prepare client presentation    task                    about 1 week ago
```

### Filtrage des issues

#### Par état

```bash
# Issues ouvertes (par défaut)
gh issue list --state open

# Issues fermées
gh issue list --state closed

# Toutes les issues
gh issue list --state all
```

#### Par assignation

```bash
# Issues assignées à vous
gh issue list --assignee @me

# Issues assignées à un utilisateur spécifique
gh issue list --assignee alice

# Issues non assignées
gh issue list --assignee ""
```

#### Par labels

```bash
# Issues avec le label "bug"
gh issue list --label bug

# Issues avec le label "high-priority"
gh issue list --label high-priority

# Issues avec plusieurs labels (ET logique)
gh issue list --label "bug,high-priority"
```

#### Par auteur

```bash
# Issues créées par vous
gh issue list --author @me

# Issues créées par un utilisateur spécifique
gh issue list --author bob
```

#### Par milestone

```bash
# Issues du milestone "v2.0"
gh issue list --milestone "v2.0"

# Issues sans milestone
gh issue list --milestone ""
```

### Tri et limitation

#### Tri des résultats

```bash
# Trier par date de création (plus récent d'abord)
gh issue list --sort created --order desc

# Trier par date de mise à jour
gh issue list --sort updated --order desc

# Trier par nombre de commentaires
gh issue list --sort comments --order desc
```

#### Limiter le nombre de résultats

```bash
# Afficher seulement les 10 premières
gh issue list --limit 10

# Afficher 50 issues (plus que le défaut)
gh issue list --limit 50
```

### Recherche avancée

#### Recherche dans le titre et le contenu

```bash
# Rechercher "authentication" dans les issues
gh issue list --search "authentication"

# Rechercher avec plusieurs mots
gh issue list --search "dark mode"

# Rechercher dans les commentaires aussi
gh issue list --search "in:comments bug"
```

#### Filtres de date

```bash
# Issues créées cette semaine
gh issue list --search "created:>=$(date -d '7 days ago' +%Y-%m-%d)"

# Issues mises à jour aujourd'hui
gh issue list --search "updated:$(date +%Y-%m-%d)"

# Issues anciennes (plus de 30 jours)
gh issue list --search "created:<$(date -d '30 days ago' +%Y-%m-%d)"
```

## 🎯 Workflows de gestion quotidienne

### Workflow développeur solo

#### Capturer une idée rapidement

```bash
# Idée rapide
gh issue create --title "Améliorer la performance du loader"

# Avec un peu plus de contexte
gh issue create \
  --title "Optimiser les images pour le web" \
  --body "Les images sont trop lourdes et ralentissent le chargement. Implémenter lazy loading et compression."
```

#### Planifier sa journée

```bash
# Voir ce qui m'est assigné
gh issue list --assignee @me --state open

# Voir les priorités
gh issue list --label "high-priority" --state open

# Voir les tâches rapides
gh issue list --label "quick-fix" --state open
```

#### Faire le point sur l'avancement

```bash
# Ce qui a été terminé cette semaine
gh issue list --state closed --search "closed:>=$(date -d '7 days ago' +%Y-%m-%d)"

# Ce qui reste à faire pour la prochaine version
gh issue list --milestone "v1.2" --state open
```

### Workflow équipe

#### Daily standup

```bash
# Ce que chacun fait
gh issue list --assignee alice --state open
gh issue list --assignee bob --state open
gh issue list --assignee charlie --state open

# Les blocages (issues anciennes encore ouvertes)
gh issue list --search "created:<$(date -d '7 days ago' +%Y-%m-%d)" --state open
```

#### Sprint planning

```bash
# Backlog des bugs prioritaires
gh issue list --label "bug" --label "high-priority" --state open

# Nouvelles fonctionnalités demandées
gh issue list --label "enhancement" --sort created --order desc

# Estimations des tâches
gh issue list --label "story-points-3" --state open
```

#### Sprint review

```bash
# Ce qui a été livré ce sprint
gh issue list --milestone "Sprint 12" --state closed

# Ce qui reste en cours
gh issue list --milestone "Sprint 12" --state open
```

### Workflow open source

#### Triage des nouvelles contributions

```bash
# Nouvelles issues à examiner
gh issue list --label "needs-triage" --sort created --order desc

# Issues pour débutants
gh issue list --label "good first issue" --state open

# Issues qui attendent de l'aide
gh issue list --label "help wanted" --state open
```

#### Gestion de la communauté

```bash
# Questions des utilisateurs
gh issue list --label "question" --state open

# Rapports de bugs externes
gh issue list --label "bug" --author-not @me --state open

# Demandes de fonctionnalités
gh issue list --label "feature-request" --state open
```

## 🎨 Formats de sortie et automatisation

### Format JSON pour scripts

```bash
# Informations complètes en JSON
gh issue list --json number,title,state,labels,assignees,createdAt

# Exemple de sortie
[
  {
    "number": 123,
    "title": "Add dark mode support",
    "state": "OPEN",
    "labels": [
      {"name": "feature"},
      {"name": "enhancement"}
    ],
    "assignees": [
      {"login": "alice"}
    ],
    "createdAt": "2024-01-15T10:30:00Z"
  }
]
```

### Champs JSON disponibles

**Champs les plus utiles** :
```bash
--json number,title,body,state,labels,assignees,milestone,createdAt,updatedAt,closedAt
```

**Liste complète** :
- `number` : Numéro de l'issue
- `title` : Titre
- `body` : Description
- `state` : État (OPEN/CLOSED)
- `labels` : Labels appliqués
- `assignees` : Personnes assignées
- `milestone` : Milestone associé
- `author` : Créateur de l'issue
- `createdAt` : Date de création
- `updatedAt` : Dernière modification
- `closedAt` : Date de fermeture
- `comments` : Nombre de commentaires
- `url` : URL de l'issue

### Scripts d'automatisation

#### Script de reporting quotidien

```bash
#!/bin/bash
# daily-issue-report.sh

echo "📊 Rapport quotidien des issues - $(date +%Y-%m-%d)"
echo "================================================="

echo ""
echo "🆕 Nouvelles issues aujourd'hui :"
gh issue list --search "created:$(date +%Y-%m-%d)" --json number,title,author | \
  jq -r '.[] | "#\(.number) - \(.title) (par @\(.author.login))"'

echo ""
echo "✅ Issues fermées aujourd'hui :"
gh issue list --state closed --search "closed:$(date +%Y-%m-%d)" --json number,title | \
  jq -r '.[] | "#\(.number) - \(.title)"'

echo ""
echo "🔥 Issues prioritaires ouvertes :"
gh issue list --label "high-priority" --state open --limit 5 --json number,title | \
  jq -r '.[] | "#\(.number) - \(.title)"'

echo ""
echo "👤 Mes issues en cours :"
gh issue list --assignee @me --state open --json number,title | \
  jq -r '.[] | "#\(.number) - \(.title)"'
```

#### Script de création d'issue depuis template

```bash
#!/bin/bash
# create-bug-issue.sh

TITLE=$1
if [ -z "$TITLE" ]; then
    echo "Usage: $0 <titre-du-bug>"
    exit 1
fi

TEMPLATE="## 🐛 Description du bug
Décrivez clairement le problème rencontré.

## 🔄 Étapes pour reproduire
1. Aller à '...'
2. Cliquer sur '...'
3. Faire défiler jusqu'à '...'
4. Voir l'erreur

## ✅ Comportement attendu
Décrivez ce qui devrait se passer normalement.

## 🖼️ Captures d'écran
Si applicable, ajoutez des captures d'écran.

## 🌍 Environnement
- OS: [ex: Windows 10, macOS 12.0, Ubuntu 20.04]
- Navigateur: [ex: Chrome 96, Firefox 95, Safari 15]
- Version de l'app: [ex: 1.2.3]

## ℹ️ Informations supplémentaires
Ajoutez toute autre information pertinente."

echo "$TEMPLATE" | gh issue create --title "🐛 $TITLE" --body-file - --label bug
echo "✅ Issue de bug créée avec le template standard"
```

#### Script de tri automatique

```bash
#!/bin/bash
# auto-triage.sh

echo "🔄 Tri automatique des issues..."

# Marquer les vieilles issues comme stale
gh issue list --search "updated:<$(date -d '30 days ago' +%Y-%m-%d)" --state open --json number | \
  jq -r '.[].number' | \
  while read issue_number; do
    echo "Marquage de l'issue #$issue_number comme inactive"
    gh issue edit "$issue_number" --add-label "stale"
  done

# Identifier les issues sans label
gh issue list --search "-label:*" --state open --json number,title | \
  jq -r '.[] | "#\(.number) - \(.title)"' | \
  while read issue_info; do
    echo "Issue sans label détectée: $issue_info"
  done

echo "✅ Tri automatique terminé"
```

## 💡 Bonnes pratiques de rédaction

### Titres d'issues efficaces

#### ✅ Bons titres

```bash
# Spécifique et actionnable
gh issue create --title "Fix: Login button not working on mobile Safari"

# Avec catégorie claire
gh issue create --title "Feature: Add export to PDF functionality"

# Avec contexte technique
gh issue create --title "Performance: Optimize image loading on homepage"

# Avec priorité implicite
gh issue create --title "Security: Fix XSS vulnerability in comment system"
```

#### ❌ Mauvais titres

```bash
# Trop vague
gh issue create --title "Bug"
gh issue create --title "Fix this"
gh issue create --title "Problem with the app"

# Pas d'action claire
gh issue create --title "The site is slow"
gh issue create --title "Users complaining"
gh issue create --title "Something wrong"
```

### Descriptions structurées

#### Template pour bugs

```markdown
## 🐛 Description
Description claire et concise du bug.

## 🔄 Étapes pour reproduire
1. Étape 1
2. Étape 2
3. Étape 3

## ✅ Comportement attendu
Ce qui devrait se passer.

## ❌ Comportement actuel
Ce qui se passe réellement.

## 🌍 Environnement
- OS:
- Navigateur:
- Version:
```

#### Template pour fonctionnalités

```markdown
## 🎯 Problème à résoudre
Quel besoin cette fonctionnalité adresse-t-elle ?

## 💡 Solution proposée
Description de la solution envisagée.

## 🔄 Alternatives considérées
Autres solutions évaluées et pourquoi elles ont été écartées.

## ✅ Critères d'acceptation
- [ ] Critère 1
- [ ] Critère 2
- [ ] Critère 3

## 📝 Notes supplémentaires
Informations techniques, contraintes, etc.
```

### Labels et catégorisation

#### Système de labels recommandé

**Type** :
- `bug` - Correction de problème
- `feature` - Nouvelle fonctionnalité
- `enhancement` - Amélioration existante
- `documentation` - Mise à jour docs
- `task` - Tâche organisationnelle

**Priorité** :
- `critical` - Blocage complet
- `high-priority` - Important et urgent
- `medium-priority` - Important non urgent
- `low-priority` - Peut attendre

**Difficulté** :
- `beginner-friendly` - Parfait pour débuter
- `intermediate` - Nécessite de l'expérience
- `advanced` - Complexe, expertise requise

**Status** :
- `needs-triage` - À examiner
- `ready` - Prêt à être développé
- `in-progress` - En cours de développement
- `needs-review` - Attend validation
- `blocked` - Bloqué par dépendance externe

## 🔍 Recherche et filtrage avancés

### Syntaxe de recherche GitHub

```bash
# Recherche par contenu
gh issue list --search "authentication"

# Recherche par titre uniquement
gh issue list --search "in:title login"

# Recherche par auteur
gh issue list --search "author:alice"

# Recherche par assigné
gh issue list --search "assignee:bob"

# Recherche par label
gh issue list --search "label:bug"

# Recherche par milestone
gh issue list --search "milestone:v2.0"

# Recherche par date
gh issue list --search "created:>2024-01-01"

# Recherche combinée
gh issue list --search "label:bug assignee:alice created:>2024-01-01"
```

### Filtres de productivité

```bash
# Mes tâches du jour
gh issue list --assignee @me --label "today" --state open

# Issues bloquantes
gh issue list --label "blocked" --state open

# Quick wins (tâches rapides)
gh issue list --label "quick-fix" --state open --limit 5

# Issues en attente de review
gh issue list --label "needs-review" --state open

# Bugs critiques
gh issue list --label "bug" --label "critical" --state open
```

## 📋 Récapitulatif des commandes essentielles

### Création d'issues

```bash
# Création interactive
gh issue create

# Création rapide
gh issue create --title "Titre de l'issue"

# Création complète
gh issue create --title "Titre" --body "Description" --label bug --assignee @me

# Depuis un fichier
gh issue create --title "Titre" --body-file description.md
```

### Listing et filtrage

```bash
# Listing de base
gh issue list

# Filtres principaux
gh issue list --assignee @me --state open --label bug

# Tri et limitation
gh issue list --sort created --order desc --limit 10

# Format JSON
gh issue list --json number,title,state,labels
```

### Recherche avancée

```bash
# Recherche simple
gh issue list --search "authentication"

# Recherche avec filtres
gh issue list --search "label:bug assignee:alice"

# Recherche par date
gh issue list --search "created:>2024-01-01"
```

## 🎯 Prochaine étape

Parfait ! Vous savez maintenant créer des issues claires et naviguer efficacement dans votre backlog. Il est temps d'apprendre à organiser tout cela avec les labels et les assignations pour une productivité maximale.

---

*Vous avez terminé la section 4.1 ! Vous maîtrisez maintenant la création et la consultation d'issues comme un pro. Dans la prochaine section, nous verrons comment les organiser efficacement avec labels et assignations.*

⏭️
