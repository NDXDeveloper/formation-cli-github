🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 5.2 : Lister et filtrer les PRs (`gh pr list`, `gh pr view`)

## Introduction

Après avoir appris à créer des Pull Requests, il est essentiel de savoir comment les visualiser et les explorer efficacement. GitHub CLI offre des commandes puissantes pour lister toutes les PRs d'un projet et examiner leurs détails en profondeur.

Dans cette section, nous découvrirons comment naviguer parmi les Pull Requests existantes et obtenir toutes les informations nécessaires pour comprendre l'état d'un projet.

## La commande `gh pr list`

### Utilisation de base

La commande la plus simple pour voir toutes les Pull Requests d'un repository :

```bash
gh pr list
```

Cette commande affiche par défaut :
- Le numéro de la PR
- Le titre
- Le statut (Open, Closed, Merged)
- L'auteur
- La date de création

### Comprendre l'affichage

Voici un exemple typique de ce que vous verrez :

```
#142  feat: Add dark mode support          Open    alice    about 2 hours ago
#141  fix: Resolve navigation bug          Merged  bob      about 1 day ago
#140  docs: Update installation guide      Closed  charlie  about 3 days ago
```

Chaque ligne représente une Pull Request avec ses informations essentielles.

## Filtrer les Pull Requests

### Filtrer par statut

Pour voir uniquement les PRs ouvertes (par défaut) :
```bash
gh pr list --state open
```

Pour voir les PRs fermées :
```bash
gh pr list --state closed
```

Pour voir les PRs mergées :
```bash
gh pr list --state merged
```

Pour voir toutes les PRs (tous statuts confondus) :
```bash
gh pr list --state all
```

### Filtrer par auteur

Pour voir les PRs créées par un utilisateur spécifique :
```bash
gh pr list --author username
```

Pour voir vos propres PRs :
```bash
gh pr list --author @me
```

### Filtrer par assigné

Pour voir les PRs assignées à quelqu'un :
```bash
gh pr list --assignee username
```

Pour voir les PRs qui vous sont assignées :
```bash
gh pr list --assignee @me
```

### Filtrer par labels

Pour voir les PRs avec un label spécifique :
```bash
gh pr list --label "bug"
```

Pour voir les PRs avec plusieurs labels :
```bash
gh pr list --label "bug,urgent"
```

### Limiter le nombre de résultats

Pour afficher seulement les 5 dernières PRs :
```bash
gh pr list --limit 5
```

## Combiner les filtres

Les filtres peuvent être combinés pour des recherches plus précises :

```bash
gh pr list --state open --author alice --label "feature"
```

Cette commande affiche toutes les PRs ouvertes, créées par Alice, avec le label "feature".

## La commande `gh pr view`

### Voir les détails d'une PR

Pour examiner une Pull Request en détail :

```bash
gh pr view 142
```

Ou en utilisant l'URL :
```bash
gh pr view https://github.com/owner/repo/pull/142
```

### Informations affichées

La commande `gh pr view` affiche des informations complètes :

- **Titre et description** : Le contenu principal de la PR
- **Statut** : Open, Closed, ou Merged
- **Auteur et assignés** : Qui a créé et qui travaille sur la PR
- **Labels et milestones** : Catégorisation de la PR
- **Reviews** : États des reviews (approved, changes requested, etc.)
- **Checks** : Statut des tests automatiques (CI/CD)
- **Commits** : Liste des commits inclus
- **Files changed** : Fichiers modifiés

### Options d'affichage

Pour voir les commentaires de la PR :
```bash
gh pr view 142 --comments
```

Pour voir le diff (les modifications) :
```bash
gh pr view 142 --diff
```

Pour ouvrir la PR dans le navigateur :
```bash
gh pr view 142 --web
```

## Formats de sortie alternatifs

### Format JSON

Pour obtenir les données en format JSON (utile pour les scripts) :
```bash
gh pr list --json number,title,author,state
```

Vous pouvez spécifier exactement quelles données vous voulez :
```bash
gh pr list --json number,title,author,createdAt,labels
```

### Utiliser jq pour traiter le JSON

Si vous avez `jq` installé, vous pouvez traiter les données JSON :
```bash
gh pr list --json title,author --jq '.[] | select(.author.login == "alice")'
```

## Cas d'usage courants

### Voir l'état général du projet

```bash
gh pr list --state all --limit 20
```

Cette commande donne un aperçu rapide de l'activité récente du projet.

### Vérifier ses contributions

```bash
gh pr list --author @me --state all
```

Idéal pour voir toutes vos contributions au projet.

### Suivre les PRs en cours de review

```bash
gh pr list --state open --label "needs-review"
```

Parfait pour les maintainers qui veulent voir ce qui attend une review.

### Examiner une PR avant de la reviewer

```bash
gh pr view 142 --diff
```

Permet de voir exactement ce qui a changé avant de faire une review.

## Conseils pour une utilisation efficace

### Créer des alias

Vous pouvez créer des raccourcis pour les commandes fréquentes :

```bash
# Dans votre .bashrc ou .zshrc
alias prlist='gh pr list --state open'
alias mypr='gh pr list --author @me'
```

### Utiliser les templates de recherche

Gardez une liste de vos filtres les plus utilisés :
- PRs en attente de review : `gh pr list --state open --label "needs-review"`
- PRs urgentes : `gh pr list --state open --label "urgent"`
- Vos PRs ouvertes : `gh pr list --author @me --state open`

### Navigation rapide

Utilisez `gh pr view` avec les numéros de PR visibles dans `gh pr list` pour naviguer rapidement entre les détails des différentes Pull Requests.

## Résumé

Les commandes `gh pr list` et `gh pr view` sont essentielles pour :

- **`gh pr list`** : Obtenir une vue d'ensemble des Pull Requests avec de nombreuses options de filtrage
- **`gh pr view`** : Examiner en détail une PR spécifique avec toutes ses informations

Ces outils permettent de rester organisé dans les projets avec de nombreuses Pull Requests et de suivre efficacement l'état du développement collaboratif.

La maîtrise de ces commandes améliore significativement votre productivité dans la gestion des projets GitHub, que vous soyez contributeur occasionnel ou maintainer d'un projet important.

⏭️
