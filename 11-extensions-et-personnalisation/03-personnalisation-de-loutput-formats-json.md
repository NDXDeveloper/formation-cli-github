🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 11.3 Personnalisation de l'output (formats JSON, etc.)

## Introduction

La CLI GitHub offre de nombreuses possibilités pour personnaliser l'affichage des résultats. Cette flexibilité vous permet d'adapter la sortie selon vos besoins : lecture humaine, traitement automatisé, intégration dans des scripts, ou création de rapports personnalisés.

## Formats de sortie disponibles

### Vue d'ensemble des formats

La CLI GitHub supporte plusieurs formats de sortie :

- **Format par défaut** : Affichage optimisé pour la lecture humaine
- **JSON** : Format structuré pour le traitement automatisé
- **Template** : Format personnalisé avec des modèles
- **TSV/CSV** : Format tabulaire pour les tableurs
- **Formats spécialisés** : Selon les commandes

### Avantages de chaque format

**Format par défaut :**
- ✅ Facile à lire pour les humains
- ✅ Coloré et bien formaté
- ❌ Difficile à traiter automatiquement

**Format JSON :**
- ✅ Parfait pour les scripts et l'automatisation
- ✅ Structure de données complète
- ✅ Compatible avec de nombreux outils
- ❌ Moins lisible pour les humains

**Format Template :**
- ✅ Entièrement personnalisable
- ✅ Combine lisibilité et structure
- ✅ Flexible pour tous les besoins

## Utilisation du format JSON

### Syntaxe de base

```bash
# Ajouter --json suivi des champs désirés
gh commande --json champ1,champ2,champ3

# Exemples concrets
gh repo list --json name,description,url
gh issue list --json number,title,state,author
gh pr list --json number,title,state,mergeable
```

### Exemples pratiques avec les repositories

```bash
# Informations de base des repositories
gh repo list --json name,description,visibility

# Sortie détaillée avec plus de champs
gh repo list --json name,description,url,stargazerCount,forkCount,language

# Informations complètes (tous les champs disponibles)
gh repo list --json
```

**Exemple de sortie JSON :**
```json
[
  {
    "name": "mon-projet",
    "description": "Description de mon projet",
    "url": "https://github.com/user/mon-projet",
    "stargazerCount": 15,
    "forkCount": 3,
    "language": "JavaScript"
  }
]
```

### Exemples avec les issues

```bash
# Issues avec informations essentielles
gh issue list --json number,title,state,author

# Issues avec plus de détails
gh issue list --json number,title,body,state,author,assignees,labels,createdAt

# Issues d'un état spécifique
gh issue list --state open --json number,title,author
```

### Exemples avec les Pull Requests

```bash
# PRs de base
gh pr list --json number,title,state,author

# PRs avec détails de review
gh pr list --json number,title,state,author,reviewDecision,mergeable

# PRs avec informations de merge
gh pr list --json number,title,headRefName,baseRefName,mergeable,mergeStateStatus
```

## Traitement du JSON avec jq

### Installation et utilisation de jq

`jq` est un outil puissant pour traiter les données JSON en ligne de commande :

```bash
# Installation (selon votre système)
# macOS
brew install jq

# Ubuntu/Debian
sudo apt install jq

# Windows (Chocolatey)
choco install jq
```

### Filtrage simple avec jq

```bash
# Extraire seulement les noms des repositories
gh repo list --json name --jq '.[].name'

# Extraire les titres des issues ouvertes
gh issue list --state open --json title --jq '.[].title'

# Compter le nombre d'éléments
gh repo list --json name --jq 'length'
```

### Filtrage avancé

```bash
# Repositories avec plus de 10 étoiles
gh repo list --json name,stargazerCount --jq '.[] | select(.stargazerCount > 10)'

# Issues assignées à un utilisateur spécifique
gh issue list --json number,title,assignees --jq '.[] | select(.assignees[].login == "username")'

# PRs en état "ready for review"
gh pr list --json number,title,isDraft --jq '.[] | select(.isDraft == false)'
```

### Formatage personnalisé avec jq

```bash
# Créer une liste formatée
gh repo list --json name,stargazerCount --jq '.[] | "\(.name): \(.stargazerCount) ⭐"'

# Créer un rapport simple
gh issue list --json number,title,state --jq '.[] | "Issue #\(.number): \(.title) [\(.state)]"'

# Exporter en CSV
gh repo list --json name,language,stargazerCount --jq -r '.[] | [.name, .language, .stargazerCount] | @csv'
```

## Format Template

### Introduction aux templates

Les templates vous permettent de créer des formats de sortie personnalisés en utilisant la syntaxe Go template :

```bash
# Syntaxe de base
gh commande --template 'template-content'

# Exemple simple
gh repo list --template '{{range .}}{{.name}}: {{.description}}{{"\n"}}{{end}}'
```

### Syntaxe des templates

#### Variables et champs

```bash
# Accéder à un champ
{{.fieldName}}

# Exemples
{{.name}}        # Nom du repository/issue/PR
{{.title}}       # Titre
{{.state}}       # État
{{.author.login}} # Nom d'utilisateur de l'auteur
```

#### Boucles

```bash
# Itérer sur une liste
{{range .}}
  {{.name}}: {{.description}}
{{end}}

# Avec index
{{range $index, $item := .}}
  {{$index}}: {{$item.name}}
{{end}}
```

#### Conditions

```bash
# Condition simple
{{if .description}}{{.description}}{{else}}Pas de description{{end}}

# Comparaison
{{if eq .state "open"}}🟢 Ouvert{{else}}🔴 Fermé{{end}}
```

### Exemples pratiques de templates

#### Template pour les repositories

```bash
# Liste simple avec icônes
gh repo list --template '{{range .}}📁 {{.name}} {{if .description}}- {{.description}}{{end}}
{{end}}'

# Avec statistiques
gh repo list --template '{{range .}}{{.name}} | ⭐{{.stargazerCount}} | 🍴{{.forkCount}} | {{.language}}
{{end}}'
```

#### Template pour les issues

```bash
# Issues avec état coloré
gh issue list --template '{{range .}}#{{.number}} {{.title}} {{if eq .state "open"}}🟢{{else}}🔴{{end}}
{{end}}'

# Issues avec auteur et date
gh issue list --template '{{range .}}Issue #{{.number}}: {{.title}}
   👤 Par: {{.author.login}} | 📅 {{.createdAt}}
{{end}}'
```

#### Template pour les Pull Requests

```bash
# PRs avec statut de review
gh pr list --template '{{range .}}PR #{{.number}}: {{.title}}
   {{if .reviewDecision}}📝 Review: {{.reviewDecision}}{{else}}⏳ En attente{{end}}
{{end}}'
```

### Templates avancés

#### Fonctions utiles

```bash
# Formatage de dates
{{.createdAt | timeago}}  # "il y a 2 jours"

# Couleurs (si supportées par le terminal)
{{.title | color "green"}}

# Raccourcissement de texte
{{.description | truncate 50}}
```

#### Template avec mise en forme

```bash
# Rapport détaillé
gh repo list --template '
📊 RAPPORT REPOSITORIES
=====================
{{range .}}
📁 {{.name}}
   📝 {{.description | default "Pas de description"}}
   🌐 {{.url}}
   ⭐ {{.stargazerCount}} étoiles | 🍴 {{.forkCount}} forks
   💻 Langage: {{.language | default "Non spécifié"}}
   📅 Mis à jour: {{.updatedAt | timeago}}

{{end}}
Total: {{len .}} repositories
'
```

## Combinaison avec d'autres outils

### Intégration avec des scripts

```bash
#!/bin/bash

# Script de sauvegarde des issues
echo "Sauvegarde des issues en cours..."

# Exporter en JSON
gh issue list --json number,title,body,state,author,createdAt > issues_backup.json

# Créer un rapport lisible
gh issue list --template '
RAPPORT ISSUES - {{now | date "2006-01-02"}}
=====================================
{{range .}}
Issue #{{.number}}: {{.title}}
État: {{.state}}
Auteur: {{.author.login}}
Date: {{.createdAt | date "2006-01-02"}}
---
{{end}}
' > issues_report.txt

echo "✅ Sauvegarde terminée !"
```

### Export vers différents formats

```bash
# Export CSV pour Excel
gh repo list --json name,language,stargazerCount,forkCount \
  --jq -r '.[] | [.name, .language, .stargazerCount, .forkCount] | @csv' \
  > repositories.csv

# Export TSV (Tab-separated values)
gh issue list --json number,title,state,author \
  --jq -r '.[] | [.number, .title, .state, .author.login] | @tsv' \
  > issues.tsv

# Export formaté pour Markdown
gh pr list --template '{{range .}}| {{.number}} | {{.title}} | {{.state}} | {{.author.login}} |
{{end}}' > prs.md
```

## Personnalisation globale

### Configuration des formats par défaut

Vous pouvez définir des formats par défaut pour certaines commandes :

```bash
# Configurer un format par défaut pour les repositories
gh config set editor code

# Utiliser des alias avec formats personnalisés
gh alias set repos 'repo list --json name,description,stargazerCount --jq "sort_by(.stargazerCount) | reverse"'
```

### Variables d'environnement utiles

```bash
# Désactiver les couleurs (utile pour les scripts)
export NO_COLOR=1

# Configurer la pagination
export GH_PAGER=cat

# Format par défaut pour certaines sorties
export GH_FORMAT=json
```

## Cas d'usage pratiques

### Monitoring et alertes

```bash
# Vérifier les PRs en attente de review
gh pr list --json number,title,reviewDecision \
  --jq '.[] | select(.reviewDecision == null) | "PR #\(.number): \(.title)"'

# Issues critiques (avec label "bug" par exemple)
gh issue list --label bug --json number,title \
  --jq '.[] | "🐛 Issue #\(.number): \(.title)"'
```

### Rapports automatisés

```bash
# Rapport hebdomadaire d'activité
gh pr list --json number,title,author,createdAt \
  --jq --arg week_ago "$(date -d '7 days ago' '+%Y-%m-%d')" \
  '.[] | select(.createdAt > $week_ago) | "Nouvelle PR: \(.title) par \(.author.login)"'
```

### Intégration avec d'autres outils

```bash
# Envoyer des données à une API
gh issue list --json number,title,state | \
  curl -X POST -H "Content-Type: application/json" \
  -d @- https://api.example.com/issues

# Créer des fichiers de configuration
gh repo list --json name,cloneUrl \
  --jq '.[] | "git clone \(.cloneUrl)"' > clone_all.sh
```

## Astuces et bonnes pratiques

### Performance

- ✅ Limitez les champs JSON aux données nécessaires
- ✅ Utilisez `--limit` pour réduire le nombre de résultats
- ✅ Combinez filtres et formats pour optimiser les requêtes

### Lisibilité

- ✅ Utilisez des templates pour l'affichage humain
- ✅ Ajoutez des icônes et couleurs pour améliorer la lisibilité
- ✅ Structurez vos templates avec des indentations

### Maintenance

- ✅ Documentez vos templates complexes
- ✅ Créez des alias pour les formats fréquemment utilisés
- ✅ Testez vos scripts avec différents jeux de données

## Debugging et résolution de problèmes

### Erreurs courantes

```bash
# Vérifier les champs disponibles
gh issue list --json | jq 'keys'

# Tester un template simple
gh repo list --limit 1 --template '{{.}}'

# Valider du JSON
gh repo list --json name | jq '.'
```

### Outils de debugging

```bash
# Voir la structure complète
gh issue list --limit 1 --json | jq '.'

# Tester des filtres jq
echo '{"name": "test", "count": 5}' | jq '.name'

# Valider des templates
gh repo list --limit 1 --template '{{range .}}{{.name}}{{end}}'
```

---

**🎯 Points clés à retenir :**
- JSON est parfait pour l'automatisation et les scripts
- Les templates offrent un équilibre entre lisibilité et personnalisation
- `jq` est un outil puissant pour traiter les données JSON
- Combinez différents formats selon vos besoins spécifiques
- Utilisez des alias pour sauvegarder vos formats favoris

**➡️ Prochaine étape :** Dans la section 11.4, nous explorerons la configuration avancée et l'optimisation de la CLI GitHub.

⏭️
