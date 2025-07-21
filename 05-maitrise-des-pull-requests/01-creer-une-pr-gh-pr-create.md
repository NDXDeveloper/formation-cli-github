🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 5.1 - Créer une PR (`gh pr create`)

## 🎯 Introduction

Créer une Pull Request, c'est bien plus que fusionner du code ! C'est l'art de présenter vos modifications de manière claire, convaincante et collaborative. Une PR bien créée facilite la review, accélère l'intégration et améliore la qualité du projet. Cette section vous apprendra à maîtriser `gh pr create` pour créer des PRs exceptionnelles.

**Analogie** : Si votre code était un plat que vous présentez à un chef étoilé, la PR serait votre présentation : les ingrédients (changements), la recette (description), et la mise en scène (structure) qui font la différence !

## 🔍 Qu'est-ce qu'une Pull Request ?

### Définition simple

Une **Pull Request (PR)** est une **demande de fusion** de vos modifications dans la branche principale d'un projet. C'est l'occasion de :

- 📝 **Présenter** vos changements
- 💬 **Discuter** avec l'équipe
- 👀 **Faire réviser** le code
- 🧪 **Tester** automatiquement
- ✅ **Valider** avant intégration

### Structure d'une Pull Request

Chaque PR contient :
- **Titre** : Résumé des changements
- **Description** : Contexte et détails
- **Commits** : Historique des modifications
- **Fichiers** : Code modifié
- **Reviews** : Retours de l'équipe
- **Checks** : Tests et validations automatiques

### Cycle de vie d'une PR

```
Création → Review → Corrections → Approbation → Merge
    ↑                                           ↓
    └── Feedback et améliorations continues ←───┘
```

## 🎨 Syntaxe de base de `gh pr create`

### Commande simple

```bash
gh pr create
```

**Cette commande** :
1. Détecte automatiquement votre branche courante
2. Lance un mode interactif pour titre et description
3. Propose la branche de destination
4. Crée la PR avec vos réponses

### Mode interactif vs direct

#### Mode interactif (recommandé pour débuter)

```bash
gh pr create
```

**Processus guidé** :
1. CLI détecte votre branche (`feature/user-auth`)
2. Propose un titre basé sur vos commits
3. Ouvre votre éditeur pour la description
4. Demande la branche de destination (`main`)
5. Crée la PR automatiquement

#### Mode direct (plus rapide)

```bash
gh pr create --title "Add user authentication" --body "Implements secure login system"
```

### Anatomie complète de la commande

```bash
gh pr create \
  --title "Titre descriptif" \
  --body "Description détaillée" \
  --base main \
  --head feature/my-feature \
  --draft \
  --assignee @me \
  --reviewer alice,bob \
  --label enhancement,frontend
```

## 📝 Créer des PRs avec différents niveaux de détail

### PR basique

```bash
# Depuis votre branche feature
git checkout feature/user-profile
gh pr create --title "Add user profile page"
```

**Résultat** : PR créée avec titre minimal, description vide.

### PR avec description

```bash
gh pr create \
  --title "Implement user authentication system" \
  --body "Adds secure login functionality with JWT tokens and session management"
```

### PR avec description détaillée depuis fichier

```bash
# Créer un fichier avec la description complète
cat > pr-description.md << 'EOF'
## Description

This PR implements a comprehensive user authentication system.

## Changes Made

- Add JWT token generation and validation
- Implement secure password hashing with bcrypt
- Create login/logout endpoints
- Add user session management
- Update frontend with login forms

## Testing

- Unit tests for all authentication functions
- Integration tests for login flow
- Manual testing on Chrome, Firefox, Safari

## Security Considerations

- Passwords are hashed with bcrypt (12 rounds)
- JWT tokens expire after 24 hours
- CSRF protection enabled
- Rate limiting on login attempts

## Breaking Changes

None - this is purely additive functionality.

Fixes #123
EOF

# Créer la PR avec cette description
gh pr create --title "Implement user authentication system" --body-file pr-description.md
```

## 🎯 Types de PRs et leurs spécificités

### PR de fonctionnalité (Feature)

```bash
gh pr create \
  --title "Feature: Add dark mode support" \
  --body "## What this PR does

Implements dark mode for the entire application.

## Changes
- Add dark mode toggle in settings
- Create dark theme CSS variables
- Update all components to support themes
- Add user preference persistence
- Include system theme detection

## Screenshots
[Before/After screenshots would be here]

## Testing
- Tested on all major browsers
- Responsive design verified
- Accessibility contrast checked

Fixes #234" \
  --label "feature,frontend,enhancement"
```

### PR de correction de bug

```bash
gh pr create \
  --title "Fix: Mobile navigation menu not closing" \
  --body "## Problem

Mobile navigation menu stays open after clicking menu items.

## Root Cause

Event listeners were not properly cleaning up, causing state inconsistency.

## Solution

- Add proper event cleanup in useEffect
- Implement backdrop click detection
- Fix state management for menu visibility

## Testing

- Reproduced bug on iOS Safari, Android Chrome
- Verified fix works across all mobile browsers
- Added regression test

## Impact

- Fixes navigation UX on mobile devices
- Improves user engagement metrics
- No breaking changes

Fixes #456" \
  --label "bug,mobile,high-priority"
```

### PR de refactoring

```bash
gh pr create \
  --title "Refactor: Optimize database queries in user service" \
  --body "## Motivation

Current user service has N+1 query problems causing performance issues.

## Changes

- Replace multiple single queries with batch queries
- Add query result caching layer
- Optimize database indexes
- Refactor user data loading logic

## Performance Impact

- Reduced query count from avg 50 to 3 per page load
- Page load time improved from 2.1s to 400ms
- Database CPU usage reduced by 60%

## Backward Compatibility

All public APIs remain unchanged. This is purely internal optimization.

## Testing

- All existing tests pass
- Added performance regression tests
- Load tested with 10x current traffic

Related to #789 (performance optimization initiative)" \
  --label "refactoring,performance,backend"
```

### PR de documentation

```bash
gh pr create \
  --title "Docs: Add comprehensive API authentication guide" \
  --body "## What's Added

Comprehensive guide for API authentication covering all use cases.

## Content

- Getting started with API keys
- OAuth2 flow step-by-step
- JWT token management
- Rate limiting and best practices
- Troubleshooting common issues
- Code examples in multiple languages

## Target Audience

- New developers integrating with our API
- Existing users upgrading to v2.0
- Support team for common questions

## Quality Checks

- Technical accuracy reviewed by @api-team
- Writing style consistent with existing docs
- All code examples tested
- Screenshots updated to latest UI

Closes #567" \
  --label "documentation,api"
```

## 🎨 Options avancées de création

### PR en mode draft (brouillon)

```bash
# Créer une PR draft pour feedback précoce
gh pr create --draft \
  --title "WIP: User dashboard redesign" \
  --body "Early implementation of new dashboard.

⚠️ **This is a work in progress**

Seeking feedback on:
- Overall layout and UX
- Component structure
- Data visualization approach

TODO:
- [ ] Implement responsive design
- [ ] Add loading states
- [ ] Optimize performance
- [ ] Write tests

Feel free to leave early feedback!"
```

**Avantages des draft PRs** :
- ✅ Feedback précoce sans pression
- ✅ Collaboration pendant le développement
- ✅ Visibilité du travail en cours
- ✅ Discussions techniques avant finalisation

### PR avec assignations et reviewers

```bash
gh pr create \
  --title "Implement payment processing" \
  --body "Complete payment system implementation" \
  --assignee @me \
  --reviewer alice,bob \
  --reviewer @backend-team \
  --label "feature,payment,high-priority"
```

### PR vers une branche spécifique

```bash
# PR vers develop au lieu de main
gh pr create \
  --title "Feature: Add user analytics" \
  --body "Analytics dashboard for user behavior" \
  --base develop \
  --head feature/user-analytics
```

### PR depuis/vers un autre repository

```bash
# Contributeur externe vers projet principal
gh pr create \
  --title "Fix typo in documentation" \
  --body "Corrects spelling error in installation guide" \
  --repo original-owner/project-name \
  --head my-username:fix-typo \
  --base main
```

## 🔗 Intégration avec les issues

### Lier une PR à une issue

```bash
gh pr create \
  --title "Implement user search functionality" \
  --body "Adds comprehensive search feature.

## Implementation

- Real-time search with debouncing
- Full-text search in content
- Filter by categories and tags
- Search result highlighting
- Mobile-optimized interface

## Performance

- Search results in < 200ms
- Efficient database indexing
- Client-side result caching

Fixes #123
Addresses #124
Related to #125"
```

### Créer une PR depuis une issue existante

```bash
# Voir l'issue d'abord
gh issue view 123

# Créer une branche liée
git checkout -b feature/search-functionality

# Développer...
git commit -m "Implement search UI components

Progress on #123"

# Créer PR avec référence automatique
gh pr create \
  --title "$(gh issue view 123 --json title -q .title)" \
  --body "Implements the search functionality described in #123.

[Description des changements...]

Fixes #123"
```

## 🎭 Templates et standardisation

### Template de PR personnalisé

```bash
# Créer un template dans .github/pull_request_template.md
mkdir -p .github
cat > .github/pull_request_template.md << 'EOF'
## Description

Brief description of what this PR accomplishes.

## Type of Change

- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] This change requires a documentation update

## Changes Made

- List the main changes
- Be specific about modifications
- Include any new dependencies

## Testing

- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed
- [ ] Cross-browser testing (if applicable)

## Screenshots (if applicable)

Add screenshots to help explain your changes.

## Checklist

- [ ] My code follows the code style of this project
- [ ] I have performed a self-review of my own code
- [ ] I have commented my code, particularly in hard-to-understand areas
- [ ] I have made corresponding changes to the documentation
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix is effective or that my feature works
- [ ] New and existing unit tests pass locally with my changes

## Related Issues

Fixes #(issue number)
EOF

# Maintenant chaque PR utilisera ce template automatiquement
gh pr create --title "My awesome feature"
# L'éditeur s'ouvrira avec le template pré-rempli
```

### Templates spécialisés par équipe

#### Template pour équipe frontend

```markdown
## Frontend Changes Checklist

### UI/UX
- [ ] Design matches mockups/wireframes
- [ ] Responsive design implemented
- [ ] Accessibility considerations addressed
- [ ] Cross-browser compatibility tested

### Performance
- [ ] Bundle size impact analyzed
- [ ] Images optimized
- [ ] Lazy loading implemented where appropriate
- [ ] Core Web Vitals maintained

### Code Quality
- [ ] Components are reusable
- [ ] PropTypes/TypeScript definitions added
- [ ] Storybook stories updated
- [ ] Unit tests for components
```

#### Template pour équipe backend

```markdown
## Backend Changes Checklist

### API Design
- [ ] RESTful conventions followed
- [ ] Input validation implemented
- [ ] Error handling comprehensive
- [ ] API documentation updated

### Performance & Security
- [ ] Database queries optimized
- [ ] Security vulnerabilities addressed
- [ ] Rate limiting considered
- [ ] Logging and monitoring added

### Testing
- [ ] Unit tests for business logic
- [ ] Integration tests for APIs
- [ ] Load testing if applicable
- [ ] Security testing completed
```

## 🤖 Automatisation et workflows

### Script de création de PR intelligente

```bash
#!/bin/bash
# smart-pr.sh

set -e

# Détecter le type de changements
current_branch=$(git branch --show-current)
files_changed=$(git diff main --name-only)

echo "🔍 Analyse des changements..."
echo "Branche: $current_branch"
echo "Fichiers modifiés: $(echo "$files_changed" | wc -l)"

# Suggérer des labels basés sur les fichiers
labels=""
if echo "$files_changed" | grep -q "\.md$\|docs/"; then
    labels="$labels,documentation"
fi
if echo "$files_changed" | grep -q "test\|spec"; then
    labels="$labels,tests"
fi
if echo "$files_changed" | grep -q "package\.json\|requirements\.txt"; then
    labels="$labels,dependencies"
fi
if echo "$files_changed" | grep -q "\.css\|\.scss\|components/"; then
    labels="$labels,frontend"
fi
if echo "$files_changed" | grep -q "api/\|server/\|backend/"; then
    labels="$labels,backend"
fi

# Nettoyer les labels (supprimer la virgule initiale)
labels=${labels#,}

# Suggérer un titre basé sur les commits récents
suggested_title=$(git log main..HEAD --oneline | head -1 | cut -d' ' -f2-)

echo ""
echo "💡 Suggestions:"
echo "  Titre suggéré: $suggested_title"
echo "  Labels suggérés: $labels"

# Demander confirmation et détails
echo ""
read -p "📝 Titre de la PR [$suggested_title]: " title
title=${title:-$suggested_title}

read -p "🎯 Numéro d'issue (optionnel): " issue_number

# Générer le corps de la PR
if [ -n "$issue_number" ]; then
    body="## Description

Implements changes described in #$issue_number.

## Changes Made

$(git log main..HEAD --oneline | sed 's/^[a-f0-9]* /- /')

## Testing

- [ ] Manual testing completed
- [ ] Existing tests pass

Fixes #$issue_number"
else
    body="## Description

Brief description of the changes.

## Changes Made

$(git log main..HEAD --oneline | sed 's/^[a-f0-9]* /- /')

## Testing

- [ ] Manual testing completed
- [ ] Existing tests pass"
fi

# Créer la PR
echo ""
echo "🚀 Création de la PR..."

pr_cmd="gh pr create --title \"$title\" --body \"$body\""
if [ -n "$labels" ]; then
    pr_cmd="$pr_cmd --label \"$labels\""
fi

echo "Commande: $pr_cmd"
eval "$pr_cmd"

echo "✅ PR créée avec succès!"
```

### Workflow avec GitHub Actions

```yaml
# .github/workflows/pr-checks.yml
name: PR Checks

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  validate-pr:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Check PR title format
        run: |
          title="${{ github.event.pull_request.title }}"
          if [[ ! $title =~ ^(feat|fix|docs|refactor|test|chore)(\(.+\))?: .+ ]]; then
            echo "❌ PR title must follow conventional commits format"
            echo "Examples: feat: add user authentication"
            echo "          fix(api): resolve login issue"
            exit 1
          fi

      - name: Check PR has description
        run: |
          body="${{ github.event.pull_request.body }}"
          if [[ ${#body} -lt 50 ]]; then
            echo "❌ PR description is too short (minimum 50 characters)"
            exit 1
          fi

      - name: Check PR links to issue
        run: |
          body="${{ github.event.pull_request.body }}"
          if [[ ! $body =~ (Fixes|Closes|Resolves|Addresses)\ #[0-9]+ ]]; then
            echo "⚠️ PR should link to an issue (Fixes #123)"
          fi
```

## 💡 Bonnes pratiques de création

### Timing de création

#### ✅ Créer une PR quand :
- **Fonctionnalité complète** et fonctionnelle
- **Tests ajoutés** et passants
- **Commits atomiques** et bien organisés
- **Prêt pour feedback** constructif

#### 🎯 Créer une draft PR quand :
- **Besoin de feedback précoce** sur l'approche
- **Travail collaboratif** en cours
- **Changements architecturaux** importants
- **Exploration** de nouvelles idées

### Structure des commits avant PR

```bash
# Commits bien organisés pour une PR claire
git commit -m "feat: add user authentication base structure

- Create User model
- Add authentication middleware
- Setup JWT configuration

Progress on #123"

git commit -m "feat: implement login endpoint

- Add POST /api/auth/login
- Validate credentials
- Return JWT token

Progress on #123"

git commit -m "feat: add logout and token refresh

- Implement logout endpoint
- Add token refresh mechanism
- Update authentication middleware

Fixes #123"
```

### Taille optimale d'une PR

#### ✅ PR de bonne taille :
- **< 400 lignes** de code modifiées
- **1-3 fonctionnalités** liées
- **Review possible en < 30 minutes**
- **Contexte clair** et focus limité

#### ❌ PR trop grosse :
- **> 1000 lignes** de changements
- **Multiple domaines** non liés
- **Difficile à réviser** entièrement
- **Risque de conflits** élevé

**Solution pour grosse PR** :
```bash
# Diviser en plusieurs PRs plus petites
git checkout -b feature/auth-step1
# Commits pour la base de l'auth
gh pr create --title "Auth foundation" --base main

git checkout -b feature/auth-step2
# Commits pour l'interface login
gh pr create --title "Login UI" --base feature/auth-step1

git checkout -b feature/auth-step3
# Commits pour les tests
gh pr create --title "Auth tests" --base feature/auth-step2
```

## 📋 Récapitulatif des commandes essentielles

### Création de base

```bash
# Création interactive
gh pr create

# Création directe
gh pr create --title "Add feature X" --body "Description"

# Draft PR
gh pr create --draft --title "WIP: Feature Y"

# Avec description depuis fichier
gh pr create --title "Feature Z" --body-file description.md
```

### Options courantes

```bash
# Avec assignation et reviewers
gh pr create --title "Fix bug" --assignee @me --reviewer alice,bob

# Vers branche spécifique
gh pr create --title "Hotfix" --base develop

# Avec labels
gh pr create --title "New feature" --label "feature,frontend"

# Depuis une autre branche
gh pr create --head feature/my-branch --base main
```

### Intégration avec issues

```bash
# Référencer une issue
gh pr create --title "Fix login" --body "Fixes #123"

# Créer depuis issue
gh issue view 123
gh pr create --title "$(gh issue view 123 --json title -q .title)"
```

### Vérification

```bash
# Voir la PR créée
gh pr view

# Voir le statut
gh pr status

# Lister vos PRs
gh pr list --author @me
```

## 🎯 Prochaine étape

Parfait ! Vous savez maintenant créer des Pull Requests exceptionnelles qui facilitent la collaboration et accélèrent les reviews. Il est temps d'apprendre à naviguer efficacement dans l'écosystème des PRs pour rester au courant de toute l'activité du projet.

👉 **Continuez avec** : [Lister et filtrer les PRs (`gh pr list`, `gh pr view`)](02-lister-filtrer-prs-gh-pr-list-view.md)

---

*Vous avez terminé la section 5.1 ! Vous maîtrisez maintenant la création de Pull Requests professionnelles. Dans la prochaine section, nous verrons comment naviguer efficacement dans l'écosystème des PRs.*

⏭️
