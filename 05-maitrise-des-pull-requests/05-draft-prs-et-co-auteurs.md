🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 5.5 : Draft PRs et co-auteurs

## Introduction

Dans le développement collaboratif, il existe des situations où vous voulez partager votre travail en cours sans qu'il soit considéré comme prêt pour la review ou le merge. C'est là qu'interviennent les **Draft Pull Requests** (PRs brouillon). De plus, quand plusieurs développeurs travaillent ensemble sur une même fonctionnalité, il est important de reconnaître leur contribution via le système des **co-auteurs**.

Ces deux fonctionnalités avancées de GitHub permettent une collaboration plus nuancée et transparente, adaptée aux réalités du développement en équipe.

## Les Draft Pull Requests

### Qu'est-ce qu'une Draft PR ?

Une Draft PR est une Pull Request marquée comme "travail en cours" (Work In Progress). Elle permet de :

- **Partager le progrès** sans demander de review
- **Obtenir des retours précoces** sur l'approche choisie
- **Collaborer** sur une fonctionnalité en développement
- **Montrer l'avancement** d'un travail complexe
- **Éviter les merges accidentels** de code non finalisé

### Différences avec une PR normale

| Draft PR | PR normale |
|----------|------------|
| Non mergeable par défaut | Mergeable si approuvée |
| Reviews non requises | Reviews généralement requises |
| Checks CI optionnels | Checks CI obligatoires |
| Signal "travail en cours" | Signal "prêt pour review" |

## Créer une Draft PR

### Lors de la création

Pour créer directement une Draft PR :

```bash
gh pr create --draft --title "feat: Add user authentication system" --body "
🚧 **Work in Progress** 🚧

## Ce qui est fait :
- [ ] Base de données users
- [x] Modèles utilisateur
- [ ] Controllers auth
- [ ] Tests unitaires
- [ ] Documentation

## Prochaines étapes :
1. Implémenter JWT tokens
2. Ajouter middleware d'authentification
3. Créer les tests
4. Mettre à jour la documentation

## Questions/Feedback souhaité :
- Est-ce que l'architecture proposée vous semble correcte ?
- Des suggestions pour l'implémentation JWT ?

N'hésitez pas à commenter même si ce n'est pas fini !
"
```

### Mode interactif

Vous pouvez aussi utiliser le mode interactif qui vous demandera si vous voulez créer une draft :

```bash
gh pr create
```

Le processus interactif vous proposera :
1. Le titre de la PR
2. Le corps/description
3. **"Submit as draft?"** - Répondez "Y" pour une draft

### Avec un template

Si votre projet a des templates de PR, vous pouvez les utiliser même pour les drafts :

```bash
gh pr create --draft --template feature.md
```

## Convertir entre Draft et PR normale

### De Draft vers PR normale

Quand votre travail est prêt pour review :

```bash
gh pr ready 142
```

Cette commande :
- Retire le statut "draft"
- Active les règles de protection de branche
- Permet les reviews et le merge
- Notifie les reviewers potentiels

### De PR normale vers Draft

Si vous réalisez que votre PR n'est pas prête :

```bash
gh pr ready 142 --undo
```

**Note :** Cette conversion est possible uniquement si la PR n'a pas encore été mergée.

## Workflow avec les Draft PRs

### 1. Développement itératif

```bash
# Début du travail
git checkout -b feature/user-dashboard
# ... développement initial ...
git add .
git commit -m "Initial dashboard structure"
git push origin feature/user-dashboard

# Créer une draft pour partager le progrès
gh pr create --draft --title "feat: User dashboard with analytics"

# Continuer le développement
# ... plus de commits ...
git push

# Quand c'est prêt
gh pr ready [numéro]
```

### 2. Collaboration sur une feature complexe

Les Draft PRs sont parfaites pour les grosses fonctionnalités où plusieurs développeurs contribuent :

```bash
# Developer A crée la draft
gh pr create --draft --title "feat: Advanced reporting system"

# Developer B peut checkout et contribuer
gh pr checkout 142
# ... modifications ...
git commit -m "Add data export functionality"
git push

# Developer A continue
# ... plus de développement ...

# Quand tout est fini
gh pr ready 142
```

### 3. Feedback précoce

```bash
# Créer une draft pour obtenir des avis sur l'architecture
gh pr create --draft --title "RFC: New authentication architecture" --body "
# Proposition d'architecture

## Contexte
Nous devons refactorer notre système d'auth...

## Solution proposée
...

## Questions ouvertes
1. Cette approche vous semble-t-elle correcte ?
2. Y a-t-il des cas d'usage que j'ai oubliés ?

**Note : Code non finalisé, feedback sur l'approche souhaité**
"
```

## Les co-auteurs (Co-authored-by)

### Qu'est-ce qu'un co-auteur ?

Le système de co-auteurs permet de créditer plusieurs personnes pour un même commit. C'est utile quand :

- **Pair programming** : Deux développeurs travaillent ensemble
- **Collaboration étroite** : Plusieurs personnes contribuent à une même feature
- **Mentorat** : Un senior guide un junior sur du code
- **Review avec modifications** : Le reviewer fait des changements directs

### Reconnaître les contributions

GitHub affiche les co-auteurs dans :
- L'historique des commits
- Les statistiques de contribution
- Les profils des contributeurs
- Les graphiques d'activité

## Ajouter des co-auteurs

### Lors du commit local

La syntaxe pour ajouter des co-auteurs dans le message de commit :

```bash
git commit -m "feat: Add user search functionality

Co-authored-by: Alice Smith <alice@exemple.com>
Co-authored-by: Bob Johnson <bob@exemple.com>"
```

### Format exact requis

```
Titre du commit

Description optionnelle du commit.

Co-authored-by: Nom Complet <email@domaine.com>
Co-authored-by: Autre Nom <autre@email.com>
```

**Points importants :**
- Ligne vide avant les co-auteurs
- Format exact : `Co-authored-by: Nom <email>`
- Email doit correspondre à un compte GitHub
- Sensible à la casse et à la ponctuation

### Trouver les informations des co-auteurs

Pour obtenir le nom et email corrects d'un collaborateur :

```bash
# Voir les contributeurs du projet
gh api repos/:owner/:repo/contributors

# Ou consulter les commits précédents
git log --pretty=format:"%an <%ae>" | sort | uniq
```

Vous pouvez aussi demander directement à vos collègues leur configuration Git :

```bash
git config user.name
git config user.email
```

## Workflow avec co-auteurs

### 1. Pair programming

Quand deux développeurs travaillent ensemble sur le même ordinateur :

```bash
# Developer A et B travaillent ensemble
# ... développement en pair programming ...

git add .
git commit -m "feat: Implement real-time notifications

Worked together on WebSocket implementation and UI updates.

Co-authored-by: Bob Developer <bob@company.com>"
```

### 2. Contributions multiples

Pour une feature où plusieurs personnes ont contribué significativement :

```bash
git commit -m "feat: Complete payment integration

- Alice: Stripe API integration
- Bob: Frontend payment form
- Charlie: Error handling and validation

Co-authored-by: Alice Smith <alice@company.com>
Co-authored-by: Charlie Brown <charlie@company.com>"
```

### 3. Review avec modifications

Quand un reviewer fait des changements directs :

```bash
# Le reviewer modifie directement et commit
git commit -m "fix: Resolve performance issues in data processing

Original implementation by @junior-dev with optimizations
for large datasets.

Co-authored-by: Junior Dev <junior@company.com>"
```

## Combiner Draft PRs et co-auteurs

### Workflow collaboratif complet

```bash
# Alice démarre une draft PR
gh pr create --draft --title "feat: Advanced user analytics"

# Bob rejoint la collaboration
gh pr checkout 142

# Ils travaillent ensemble (pair programming)
git add .
git commit -m "Add analytics data collection

Implemented event tracking and data aggregation pipeline.

Co-authored-by: Bob Johnson <bob@company.com>"

# Charlie ajoute des tests
git add tests/
git commit -m "Add comprehensive analytics tests

Co-authored-by: Alice Smith <alice@company.com>"

# Quand tout est prêt
gh pr ready 142
```

### Documenter la collaboration

Dans une Draft PR avec plusieurs contributeurs :

```bash
gh pr create --draft --title "feat: Multi-tenant architecture" --body "
## 👥 Collaboration

**Lead:** @alice (Architecture & Backend)
**Frontend:** @bob (UI Components)
**Testing:** @charlie (Test Suite)
**DevOps:** @dave (Deployment)

## 📋 Progress

- [x] Database schema (Alice)
- [x] API endpoints (Alice + Bob)
- [ ] Frontend components (Bob)
- [ ] Integration tests (Charlie)
- [ ] Deployment config (Dave)

## 🔄 How we're working

- Daily standup at 9:00
- Pair programming sessions: Alice+Bob (backend), Bob+Charlie (testing)
- Code reviews on each major milestone

**All significant commits will include co-author attribution.**
"
```

## Bonnes pratiques

### Pour les Draft PRs

#### Titres explicites
```bash
# ✅ Bon
gh pr create --draft --title "🚧 WIP: User authentication system"

# ✅ Bon aussi
gh pr create --draft --title "[DRAFT] Refactor database layer"

# ❌ Éviter
gh pr create --draft --title "Authentication"
```

#### Descriptions détaillées
Incluez toujours :
- État actuel du travail
- Ce qui reste à faire
- Questions/feedback souhaités
- Timeline estimée

#### Communication régulière
- Mettez à jour la description au fur et à mesure
- Commentez vos progrès
- Mentionnez les blockers éventuels

### Pour les co-auteurs

#### Soyez généreux avec l'attribution
- Créditez même les contributions mineures significatives
- Incluez les personnes qui ont aidé à débugger
- Reconnaissez les bonnes idées, pas seulement le code

#### Demandez l'accord
Avant d'ajouter quelqu'un comme co-auteur, assurez-vous qu'il est d'accord.

#### Vérifiez les informations
- Email correct (lié au compte GitHub)
- Orthographe du nom exacte
- Format respecté à la lettre

## Cas d'usage avancés

### Release collaboration

Pour une release majeure impliquant toute l'équipe :

```bash
gh pr create --draft --title "🚀 Release v2.0 preparation" --body "
## 🎯 Release Goals

- [ ] API v2 migration (Team Backend)
- [ ] UI redesign (Team Frontend)
- [ ] Performance optimizations (Team DevOps)
- [ ] Documentation update (Team Docs)

## 👥 Coordination

**Release Manager:** @project-lead
**Backend Lead:** @backend-senior
**Frontend Lead:** @frontend-senior
**QA Lead:** @qa-senior

All commits should include appropriate co-author attribution for pair work.

## 📅 Timeline

- Week 1-2: Core features
- Week 3: Integration & testing
- Week 4: Documentation & polish
- Release: End of month
"
```

### Cross-team features

Quand une feature implique plusieurs équipes :

```bash
# Commits avec attribution cross-team
git commit -m "feat: Add real-time chat integration

Backend WebSocket implementation with frontend real-time UI.
Cross-team effort between Backend and Frontend teams.

Co-authored-by: Frontend Lead <frontend@company.com>
Co-authored-by: Backend Developer <backend@company.com>"
```

## Résumé

Les Draft PRs et co-auteurs sont des outils puissants pour la collaboration moderne :

### Draft PRs
- **Créer** : `gh pr create --draft`
- **Convertir** : `gh pr ready [numéro]`
- **Retour en draft** : `gh pr ready [numéro] --undo`
- **Usage** : Partager le travail en cours, collaboration, feedback précoce

### Co-auteurs
- **Format** : `Co-authored-by: Nom <email@domaine.com>`
- **Placement** : À la fin du message de commit
- **Effet** : Attribution partagée, reconnaissance des contributions

### Avantages combinés
- Collaboration transparente
- Reconnaissance équitable des contributions
- Meilleure communication en équipe
- Workflow adapté aux projets complexes

Ces fonctionnalités transforment GitHub d'un simple outil de versioning en une véritable plateforme de collaboration, où chaque contribution est valorisée et où le travail en équipe est facilité à tous les niveaux.

⏭️
