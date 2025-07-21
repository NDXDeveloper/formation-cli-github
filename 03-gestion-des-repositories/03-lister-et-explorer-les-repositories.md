🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 3.3 - Lister et explorer les repositories

## 🎯 Introduction

Maintenant que vous savez créer, cloner et forker des repositories, il est temps d'apprendre à naviguer et explorer l'immense écosystème GitHub ! La CLI GitHub vous offre des outils puissants pour découvrir des projets, rechercher du code et obtenir des informations détaillées sans jamais quitter votre terminal.

**Analogie** : Si GitHub était une immense bibliothèque, les commandes d'exploration seraient votre système de catalogage personnel - vous permettant de trouver exactement le livre (repository) que vous cherchez parmi des millions d'ouvrages !

## 🔍 Vue d'ensemble des commandes d'exploration

### Commandes principales

| Commande | Usage | Description |
|----------|-------|-------------|
| `gh repo list` | Lister vos repositories | Voir tous vos repos ou ceux d'une organisation |
| `gh repo view` | Examiner un repository | Obtenir des informations détaillées |
| `gh search repos` | Rechercher des repositories | Trouver des projets par critères |
| `gh browse` | Ouvrir dans le navigateur | Navigation hybride CLI/Web |

### Pourquoi explorer en CLI ?

**Avantages de l'exploration en terminal** :
- ⚡ **Rapidité** : Pas d'interface web à charger
- 🔍 **Recherche précise** : Filtres avancés disponibles
- 📊 **Données structurées** : Format JSON pour scripts
- 🔄 **Workflow unifié** : Reste dans votre environnement de développement
- 🤖 **Automatisation** : Scriptable pour des tâches répétitives

## 📂 Lister vos repositories avec `gh repo list`

### Syntaxe de base

```bash
gh repo list
```

**Cette commande simple** affiche tous vos repositories personnels.

### Lister les repositories d'un utilisateur ou organisation

```bash
# Repositories d'un utilisateur spécifique
gh repo list microsoft

# Repositories d'une organisation
gh repo list nodejs

# Vos repositories (explicitement)
gh repo list votre-username
```

### Options de filtrage

#### Limiter le nombre de résultats

```bash
# Afficher seulement les 10 premiers
gh repo list --limit 10

# Afficher les 50 premiers (par défaut : 30)
gh repo list --limit 50
```

#### Filtrer par type

```bash
# Seulement vos repositories (pas les forks)
gh repo list --source

# Seulement vos forks
gh repo list --fork

# Repositories publics seulement
gh repo list --public

# Repositories privés seulement
gh repo list --private
```

#### Filtrer par langage

```bash
# Repositories JavaScript
gh repo list --language javascript

# Repositories Python
gh repo list --language python

# Repositories TypeScript
gh repo list --language typescript
```

### Tri et organisation

```bash
# Trier par date de mise à jour (plus récent en premier)
gh repo list --sort updated

# Trier par date de création
gh repo list --sort created

# Trier par nom
gh repo list --sort full_name

# Trier par popularité (nombre d'étoiles)
gh repo list --sort stargazers
```

### Format de sortie personnalisé

#### Format par défaut

**Sortie standard** :
```
Showing 30 of 156 repositories in @votre-username

name                    description                         visibility   updated
awesome-project         Un projet génial en JavaScript     public       about 2 hours ago
personal-website        Mon site web personnel              public       about 1 day ago
private-notes          Mes notes personnelles              private      about 3 days ago
```

#### Format JSON pour scripts

```bash
# Sortie complète en JSON
gh repo list --json name,description,url,isPrivate,primaryLanguage

# Exemple de résultat
[
  {
    "name": "awesome-project",
    "description": "Un projet génial en JavaScript",
    "url": "https://github.com/username/awesome-project",
    "isPrivate": false,
    "primaryLanguage": {
      "name": "JavaScript"
    }
  }
]
```

#### Champs JSON disponibles

**Champs les plus utiles** :
```bash
--json name,description,url,isPrivate,primaryLanguage,stargazerCount,updatedAt,createdAt,topics
```

**Liste complète** :
- `name` : Nom du repository
- `description` : Description
- `url` : URL GitHub
- `isPrivate` : Public ou privé
- `primaryLanguage` : Langage principal
- `stargazerCount` : Nombre d'étoiles
- `forkCount` : Nombre de forks
- `updatedAt` : Dernière mise à jour
- `createdAt` : Date de création
- `topics` : Tags/sujets
- `owner` : Propriétaire
- `defaultBranch` : Branche par défaut

## 🔍 Examiner un repository avec `gh repo view`

### Syntaxe de base

```bash
# Repository actuel (si vous êtes dans un dossier Git)
gh repo view

# Repository spécifique
gh repo view owner/repository

# Votre repository
gh repo view mon-repository
```

### Informations affichées

**Vue par défaut** :
```
microsoft/vscode
Visual Studio Code

  Visual Studio Code is a lightweight but powerful source code editor which
  runs on your desktop and is available for Windows, macOS and Linux.

View this repository on GitHub: https://github.com/microsoft/vscode

  Open Issues    8,234
  Open PRs       368
  Stars          163,423
  Forks          28,765
  Language       TypeScript
  License        MIT License
  Updated        about 2 hours ago
```

### Options d'affichage

#### Ouvrir dans le navigateur

```bash
# Voir ET ouvrir dans le navigateur
gh repo view microsoft/vscode --web
```

#### Format JSON pour analyse

```bash
# Informations complètes en JSON
gh repo view microsoft/vscode --json name,description,stargazerCount,forkCount,topics,languages
```

#### Informations spécifiques

```bash
# Voir seulement la description
gh repo view microsoft/vscode --json description -q .description

# Voir le nombre d'étoiles
gh repo view microsoft/vscode --json stargazerCount -q .stargazerCount

# Voir les langages utilisés
gh repo view microsoft/vscode --json languages -q '.languages | keys[]'
```

## 🔎 Rechercher des repositories avec `gh search repos`

### Recherche de base

```bash
# Recherche simple par nom
gh search repos "machine learning"

# Recherche dans les descriptions
gh search repos "web framework"
```

### Filtres de recherche

#### Par langage

```bash
# Projets JavaScript
gh search repos --language=javascript "todo app"

# Projets Python machine learning
gh search repos --language=python "machine learning"

# Projets Go
gh search repos --language=go "web server"
```

#### Par propriétaire

```bash
# Projets de Microsoft
gh search repos --owner=microsoft

# Projets de Google
gh search repos --owner=google

# Projets personnels d'un développeur
gh search repos --owner=tj  # TJ Holowaychuk
```

#### Par popularité

```bash
# Projets avec plus de 1000 étoiles
gh search repos --stars=">1000"

# Projets avec plus de 10000 étoiles
gh search repos --stars=">10000" --language=javascript

# Projets récents et populaires
gh search repos --stars=">500" --created=">2023-01-01"
```

#### Par taille

```bash
# Projets de petite taille (< 1MB)
gh search repos --size="<1000"

# Projets de taille moyenne (1-10MB)
gh search repos --size="1000..10000"

# Gros projets (> 100MB)
gh search repos --size=">100000"
```

#### Par date

```bash
# Projets créés cette année
gh search repos --created=">2024-01-01"

# Projets mis à jour récemment
gh search repos --pushed=">2024-01-01"

# Projets actifs (mis à jour cette semaine)
gh search repos --pushed=">$(date -d '7 days ago' +%Y-%m-%d)"
```

### Tri des résultats

```bash
# Trier par nombre d'étoiles (populaires d'abord)
gh search repos "react" --sort=stars --order=desc

# Trier par date de mise à jour (récents d'abord)
gh search repos "vue" --sort=updated --order=desc

# Trier par date de création
gh search repos "svelte" --sort=created --order=desc

# Trier par nombre de forks
gh search repos "angular" --sort=forks --order=desc
```

### Recherches avancées combinées

```bash
# Framework web JavaScript populaire et récent
gh search repos \
  --language=javascript \
  --stars=">1000" \
  --created=">2022-01-01" \
  "web framework"

# Outils CLI en Go avec documentation
gh search repos \
  --language=go \
  --stars=">100" \
  "cli tool" \
  --sort=stars

# Projets Python machine learning actifs
gh search repos \
  --language=python \
  --pushed=">2024-01-01" \
  --stars=">500" \
  "machine learning" \
  --limit=20
```

## 🎯 Cas d'usage pratiques

### Découvrir de nouveaux outils

**Trouver des outils CLI modernes** :
```bash
# Outils CLI populaires en Rust
gh search repos --language=rust "cli" --stars=">1000" --sort=stars

# Alternatives modernes aux outils Unix
gh search repos "modern unix" --sort=stars --limit=10

# Outils de productivité
gh search repos "productivity tool" --language=go --stars=">100"
```

### Explorer un domaine technique

**Découvrir l'écosystème React** :
```bash
# Bibliothèques React populaires
gh search repos --language=javascript "react" --stars=">5000"

# Composants React UI
gh search repos "react components ui" --stars=">1000"

# Outils de développement React
gh search repos "react developer tools" --sort=updated
```

### Analyser la concurrence

**Étudier des projets similaires** :
```bash
# Projets similaires au vôtre
gh search repos "project management tool" --language=typescript

# Voir les détails d'un concurrent
gh repo view linear/linear

# Analyser leurs topics/tags
gh repo view notion/notion --json topics -q '.topics[]'
```

### Recherche d'inspiration

**Projets de portfolio/CV** :
```bash
# Sites personnels en Gatsby
gh search repos "portfolio" --language=javascript "gatsby"

# CV interactifs
gh search repos "interactive resume" --sort=stars

# Sites personnels populaires
gh search repos "personal website" --stars=">100"
```

## 🎨 Scripts d'exploration avancés

### Script de découverte par langage

```bash
#!/bin/bash
# discover-language.sh

LANGUAGE=$1
if [ -z "$LANGUAGE" ]; then
    echo "Usage: $0 <language>"
    echo "Exemple: $0 rust"
    exit 1
fi

echo "🔍 Découverte de l'écosystème $LANGUAGE"
echo "======================================="

echo ""
echo "📊 Top 10 des projets les plus populaires :"
gh search repos --language="$LANGUAGE" --sort=stars --limit=10 \
  --json name,description,stargazerCount,url | \
  jq -r '.[] | "⭐ \(.stargazerCount) - \(.name): \(.description // "Pas de description")"'

echo ""
echo "🆕 Top 5 des projets récents et prometteurs :"
gh search repos --language="$LANGUAGE" --created=">2023-01-01" --stars=">100" \
  --sort=stars --limit=5 \
  --json name,description,stargazerCount,createdAt | \
  jq -r '.[] | "⭐ \(.stargazerCount) - \(.name) (créé en \(.createdAt[:4]))"'

echo ""
echo "🔥 Projets actifs (mis à jour cette semaine) :"
WEEK_AGO=$(date -d '7 days ago' +%Y-%m-%d)
gh search repos --language="$LANGUAGE" --pushed=">$WEEK_AGO" --stars=">50" \
  --limit=5 --json name,stargazerCount | \
  jq -r '.[] | "⭐ \(.stargazerCount) - \(.name)"'
```

### Script d'analyse de repository

```bash
#!/bin/bash
# analyze-repo.sh

REPO=$1
if [ -z "$REPO" ]; then
    echo "Usage: $0 <owner/repo>"
    exit 1
fi

echo "🔍 Analyse complète de $REPO"
echo "============================="

# Informations générales
echo ""
echo "📊 Informations générales :"
gh repo view "$REPO" --json name,description,stargazerCount,forkCount,primaryLanguage,license,topics | \
  jq -r '"Nom: " + .name,
         "Description: " + (.description // "Aucune"),
         "Étoiles: " + (.stargazerCount | tostring),
         "Forks: " + (.forkCount | tostring),
         "Langage: " + (.primaryLanguage.name // "Non spécifié"),
         "Licence: " + (.license.name // "Aucune"),
         "Topics: " + (.topics | join(", "))'

# Activité récente
echo ""
echo "📈 Activité récente :"
gh repo view "$REPO" --json pushedAt,createdAt | \
  jq -r '"Créé: " + .createdAt[:10],
         "Dernière MAJ: " + .pushedAt[:10]'

# Issues et PRs
echo ""
echo "🎫 Issues et Pull Requests :"
gh repo view "$REPO" --json issues,pullRequests | \
  jq -r '"Issues ouvertes: " + (.issues.totalCount | tostring),
         "PRs ouvertes: " + (.pullRequests.totalCount | tostring)'

# Langages utilisés
echo ""
echo "💻 Langages utilisés :"
gh repo view "$REPO" --json languages | \
  jq -r '.languages | to_entries[] | .key + ": " + (.value.size | tostring) + " bytes"'
```

### Script de veille technologique

```bash
#!/bin/bash
# tech-watch.sh

echo "📡 Veille technologique - $(date)"
echo "================================="

# Nouvelles bibliothèques JavaScript populaires
echo ""
echo "🆕 Nouvelles bibliothèques JavaScript (cette année) :"
gh search repos --language=javascript --created=">2024-01-01" --stars=">500" \
  --limit=5 --json name,description,stargazerCount,createdAt | \
  jq -r '.[] | "⭐ \(.stargazerCount) - \(.name): \(.description[:60])..."'

# Outils DevOps tendance
echo ""
echo "🛠️ Outils DevOps tendance :"
gh search repos "devops tool" --stars=">1000" --pushed=">2024-01-01" \
  --limit=3 --json name,description,stargazerCount | \
  jq -r '.[] | "⭐ \(.stargazerCount) - \(.name)"'

# Frameworks web émergents
echo ""
echo "🌐 Frameworks web émergents :"
gh search repos "web framework" --created=">2023-01-01" --stars=">200" \
  --limit=3 --json name,description,primaryLanguage,stargazerCount | \
  jq -r '.[] | "⭐ \(.stargazerCount) - \(.name) (\(.primaryLanguage.name // "N/A"))"'

# Projets IA/ML actifs
echo ""
echo "🤖 Projets IA/ML actifs :"
gh search repos "machine learning" --language=python --pushed=">2024-01-01" \
  --stars=">1000" --limit=3 --json name,stargazerCount | \
  jq -r '.[] | "⭐ \(.stargazerCount) - \(.name)"'
```

## 🔗 Navigation hybride avec `gh browse`

### Ouvrir dans le navigateur

```bash
# Ouvrir le repository actuel
gh browse

# Ouvrir un repository spécifique
gh browse microsoft/vscode

# Ouvrir une section spécifique
gh browse --issues          # Page des issues
gh browse --pulls           # Page des pull requests
gh browse --wiki             # Wiki du projet
gh browse --releases         # Page des releases
gh browse --settings         # Paramètres (si propriétaire)
```

### Navigation contextuelle

```bash
# Dans un repository cloné
cd mon-projet
gh browse                    # Ouvre le repository sur GitHub

# Ouvrir un fichier spécifique
gh browse src/main.js        # Ouvre le fichier dans GitHub

# Ouvrir à une ligne spécifique
gh browse src/main.js:42     # Ouvre le fichier à la ligne 42
```

## 📊 Analyse et statistiques

### Analyser vos repositories

```bash
# Vos repositories les plus populaires
gh repo list --sort=stargazers --limit=10 \
  --json name,stargazerCount,description | \
  jq -r '.[] | "⭐ \(.stargazerCount) - \(.name): \(.description // "Pas de description")"'

# Répartition par langages
gh repo list --json primaryLanguage | \
  jq -r '.[].primaryLanguage.name // "Inconnu"' | \
  sort | uniq -c | sort -nr
```

### Statistiques d'une organisation

```bash
# Analyser les repositories de Microsoft
gh repo list microsoft --limit=50 --json name,stargazerCount,primaryLanguage | \
  jq 'group_by(.primaryLanguage.name) |
      map({language: .[0].primaryLanguage.name, count: length, total_stars: map(.stargazerCount) | add}) |
      sort_by(.total_stars) | reverse'
```

## 💡 Conseils d'exploration efficace

### Optimiser vos recherches

#### Utiliser les topics GitHub

```bash
# Rechercher par topics
gh search repos --topic=react --topic=typescript

# Projets avec topic "awesome"
gh search repos --topic=awesome --sort=stars
```

#### Combiner plusieurs critères

```bash
# Projets CLI populaires et récents
gh search repos \
  "command line" \
  --language=go \
  --stars=">500" \
  --created=">2022-01-01" \
  --sort=stars \
  --limit=10
```

### Automatiser votre veille

#### Surveillance quotidienne

```bash
#!/bin/bash
# daily-watch.sh

# Ajouter à votre crontab : 0 9 * * * /path/to/daily-watch.sh

echo "📊 Veille quotidienne - $(date)" >> ~/github-watch.log

# Nouveaux projets intéressants
gh search repos --created=">$(date -d '1 day ago' +%Y-%m-%d)" \
  --stars=">10" --limit=5 \
  --json name,description,stargazerCount,createdAt >> ~/github-watch.log
```

#### Alertes personnalisées

```bash
#!/bin/bash
# custom-alerts.sh

# Surveiller les nouveaux projets dans vos domaines d'intérêt
KEYWORDS=("kubernetes" "docker" "react" "machine learning")

for keyword in "${KEYWORDS[@]}"; do
    echo "🔍 Nouveaux projets : $keyword"
    gh search repos "$keyword" \
      --created=">$(date -d '7 days ago' +%Y-%m-%d)" \
      --stars=">50" \
      --limit=3 \
      --json name,description,stargazerCount
    echo ""
done
```

## 📋 Récapitulatif des commandes essentielles

### Lister et filtrer

```bash
# Lister vos repositories
gh repo list

# Filtrer par type et langage
gh repo list --fork --language=javascript --limit=20

# Trier par popularité
gh repo list --sort=stargazers
```

### Examiner

```bash
# Voir un repository
gh repo view owner/repo

# Informations en JSON
gh repo view owner/repo --json name,description,stargazerCount

# Ouvrir dans le navigateur
gh repo view owner/repo --web
```

### Rechercher

```bash
# Recherche simple
gh search repos "web framework"

# Recherche avancée
gh search repos --language=python --stars=">1000" "machine learning"

# Trier les résultats
gh search repos "react" --sort=stars --limit=10
```

### Navigation

```bash
# Ouvrir le repository actuel
gh browse

# Ouvrir une section spécifique
gh browse --issues

# Ouvrir un fichier
gh browse src/main.js
```

## 🎯 Prochaine étape

Parfait ! Vous savez maintenant explorer et découvrir l'écosystème GitHub comme un expert. Il est temps d'apprendre à configurer et paramétrer finement vos repositories pour optimiser leur fonctionnement.

👉 **Continuez avec** : [Paramètres et configuration des repos](04-parametres-et-configuration-des-repos.md)

---

*Vous avez terminé la section 3.3 ! Vous maîtrisez maintenant l'exploration et la découverte de repositories. Dans la prochaine section, nous verrons comment configurer et paramétrer vos repositories de manière avancée.*

⏭️
