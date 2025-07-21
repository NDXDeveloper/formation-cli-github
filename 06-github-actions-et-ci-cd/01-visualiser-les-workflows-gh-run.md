🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 6.1 Visualiser les workflows

## Introduction

La visualisation des workflows est la première étape pour maîtriser GitHub Actions avec la CLI. Cette fonctionnalité vous permet de surveiller l'état de vos automatisations directement depuis votre terminal, sans avoir besoin d'ouvrir votre navigateur.

Dans cette section, nous allons découvrir les deux commandes essentielles : `gh run list` pour obtenir une vue d'ensemble et `gh run view` pour examiner les détails d'un workflow spécifique.

## Comprendre les workflows runs

### Qu'est-ce qu'un "run" ?

Un **run** (ou exécution) est une instance d'un workflow qui s'exécute suite à un événement déclencheur (push, pull request, schedule, etc.). Chaque fois qu'un workflow se lance, GitHub crée un nouveau run avec :

- Un **identifiant unique**
- Un **statut** (en cours, réussi, échoué, annulé)
- Une **durée d'exécution**
- Des **logs détaillés**
- Des **artifacts** éventuels

### Les différents statuts

- 🟡 **Queued** : En attente d'exécution
- 🔵 **In progress** : En cours d'exécution
- ✅ **Completed** : Terminé avec succès
- ❌ **Failed** : Échoué
- ⚪ **Cancelled** : Annulé manuellement
- ⏭️ **Skipped** : Ignoré (conditions non remplies)

## La commande `gh run list`

### Utilisation de base

La commande `gh run list` affiche la liste des exécutions de workflows pour le repository courant :

```bash
gh run list
```

**Sortie typique :**
```
STATUS  TITLE                    WORKFLOW     BRANCH  EVENT  ID          ELAPSED
✓       Update documentation     CI Pipeline  main    push   9876543210  2m 34s
✓       Add user authentication  CI Pipeline  feature pull   9876543209  3m 12s
❌      Fix database connection  CI Pipeline  hotfix  push   9876543208  1m 45s
✓       Initial commit           CI Pipeline  main    push   9876543207  2m 01s
```

### Comprendre la sortie

Chaque ligne représente une exécution avec :

- **STATUS** : Icône indiquant le résultat (✓ succès, ❌ échec, etc.)
- **TITLE** : Titre du commit ou de la PR qui a déclenché le workflow
- **WORKFLOW** : Nom du workflow exécuté
- **BRANCH** : Branche sur laquelle l'exécution a eu lieu
- **EVENT** : Événement déclencheur (push, pull_request, etc.)
- **ID** : Identifiant unique de l'exécution
- **ELAPSED** : Durée totale d'exécution

### Options de filtrage

#### Filtrer par statut

Voir uniquement les exécutions échouées :
```bash
gh run list --status failure
```

Voir les exécutions en cours :
```bash
gh run list --status in_progress
```

Statuts disponibles : `completed`, `action_required`, `cancelled`, `failure`, `neutral`, `skipped`, `stale`, `success`, `timed_out`, `in_progress`, `queued`

#### Filtrer par workflow

Si vous avez plusieurs workflows, filtrez par nom :
```bash
gh run list --workflow "CI Pipeline"
```

Ou par fichier de workflow :
```bash
gh run list --workflow ci.yml
```

#### Filtrer par branche

Voir les exécutions d'une branche spécifique :
```bash
gh run list --branch main
```

#### Filtrer par événement

Voir uniquement les exécutions déclenchées par des pull requests :
```bash
gh run list --event pull_request
```

Événements courants : `push`, `pull_request`, `schedule`, `workflow_dispatch`

#### Limiter le nombre de résultats

Afficher seulement les 5 dernières exécutions :
```bash
gh run list --limit 5
```

### Combinaison de filtres

Vous pouvez combiner plusieurs filtres :
```bash
gh run list --status failure --branch main --limit 10
```

Cette commande affiche les 10 dernières exécutions échouées sur la branche main.

## La commande `gh run view`

### Utilisation de base

Pour examiner les détails d'une exécution spécifique, utilisez `gh run view` avec l'ID du run :

```bash
gh run view 9876543210
```

### Comprendre la sortie détaillée

**Sortie typique :**
```
✓ CI Pipeline · 9876543210
Triggered via push about 2 hours ago

JOBS
✓ test (ID 12345678901)
✓ build (ID 12345678902)
✓ deploy (ID 12345678903)

For more information about a job, try: gh run view --job=<job-id>
View this run on GitHub: https://github.com/user/repo/actions/runs/9876543210
```

### Informations affichées

- **En-tête** : Statut, nom du workflow et ID
- **Déclenchement** : Comment et quand le workflow a été lancé
- **Jobs** : Liste des tâches avec leur statut et ID
- **Suggestions** : Commandes pour approfondir l'analyse
- **Lien web** : URL pour voir l'exécution sur GitHub

### Examiner un job spécifique

Pour voir les détails d'un job particulier :
```bash
gh run view --job 12345678901
```

**Sortie détaillée d'un job :**
```
✓ test in 2m34s (ID 12345678901)

STEPS
✓ Set up job
✓ Checkout code
✓ Setup Node.js
✓ Install dependencies
✓ Run tests
✓ Post Checkout code
✓ Complete job

For more information about the steps in this job, try: gh run view --log --job=<job-id>
```

### Options avancées

#### Affichage des logs

Pour voir directement les logs dans le terminal :
```bash
gh run view --log 9876543210
```

Ou pour un job spécifique :
```bash
gh run view --log --job 12345678901
```

#### Format de sortie personnalisé

Pour obtenir les données au format JSON (utile pour les scripts) :
```bash
gh run view 9876543210 --json
```

#### Utilisation sans ID

Si vous êtes dans un repository et voulez voir la dernière exécution :
```bash
gh run view
```

La CLI sélectionnera automatiquement le run le plus récent.

## Cas d'usage pratiques

### Surveillance rapide après un push

Après avoir poussé du code, vérifiez rapidement si tout se passe bien :
```bash
git push origin feature-branch
gh run list --limit 1
```

### Diagnostic d'échec

Quand un workflow échoue, examinez rapidement le problème :
```bash
# 1. Lister les échecs récents
gh run list --status failure --limit 5

# 2. Examiner l'échec le plus récent
gh run view <ID_DU_RUN_ECHOUE>

# 3. Voir les logs de l'étape qui a échoué
gh run view --log --job <ID_DU_JOB_ECHOUE>
```

### Surveillance continue

Pour surveiller l'avancement d'un workflow en cours :
```bash
# Voir l'état actuel
gh run list --status in_progress

# Puis rafraîchir régulièrement
watch -n 30 'gh run list --limit 5'
```

## Conseils pour débutants

### 🎯 **Commencez simple**
- Utilisez d'abord `gh run list` sans options pour vous familiariser
- Observez les différents statuts et ce qu'ils signifient
- Notez les IDs des runs pour les examiner en détail

### 📊 **Interprétez les résultats**
- Un ✅ vert signifie que tout s'est bien passé
- Un ❌ rouge indique un problème à résoudre
- Les durées longues peuvent indiquer des optimisations possibles

### 🔍 **Explorez progressivement**
- Commencez par `gh run view` avec l'ID d'un run réussi
- Puis examinez un run échoué pour comprendre la différence
- Utilisez `--log` quand vous êtes à l'aise avec les concepts de base

### ⚡ **Raccourcis utiles**
- `gh run list -L 3` : Les 3 dernières exécutions (L = limit)
- `gh run view` sans ID : Voir la dernière exécution
- `gh run list --web` : Ouvrir la page Actions dans le navigateur

## Points clés à retenir

✅ **`gh run list`** vous donne une vue d'ensemble rapide de toutes vos exécutions

✅ **Les filtres** permettent de trouver rapidement ce que vous cherchez

✅ **`gh run view`** offre des détails complets sur une exécution spécifique

✅ **Les IDs** sont essentiels pour naviguer entre les différentes exécutions

✅ **La combinaison des deux commandes** vous donne un contrôle total sur vos workflows

## Prochaine étape

Maintenant que vous savez visualiser vos workflows, nous allons apprendre à télécharger et analyser les logs d'exécution pour un debugging plus approfondi.


⏭️
