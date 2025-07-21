🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 6.2 Télécharger les logs d'exécution

## Introduction

Les logs (journaux d'exécution) sont essentiels pour comprendre ce qui se passe dans vos workflows GitHub Actions. Ils contiennent toutes les informations sur l'exécution : commandes lancées, messages d'erreur, sorties des programmes, et bien plus encore.

Dans cette section, nous allons apprendre à télécharger et analyser ces logs efficacement avec la CLI GitHub, que ce soit pour débugger un problème ou simplement comprendre le comportement de vos automatisations.

## Comprendre les logs GitHub Actions

### Qu'est-ce qu'un log ?

Un **log** est un fichier texte qui enregistre chronologiquement tout ce qui se passe pendant l'exécution d'un workflow. Il contient :

- 📝 **Les commandes exécutées** et leur sortie
- ⚠️ **Les messages d'avertissement** et d'erreur
- 📊 **Les informations de timing** (début/fin de chaque étape)
- 🔧 **Les détails de configuration** de l'environnement
- 💾 **Les variables d'environnement** utilisées

### Structure des logs

Les logs sont organisés hiérarchiquement :
```
Workflow
├── Job 1
│   ├── Step 1 (logs)
│   ├── Step 2 (logs)
│   └── Step 3 (logs)
├── Job 2
│   ├── Step 1 (logs)
│   └── Step 2 (logs)
└── Job 3 (si parallèle)
```

## Afficher les logs dans le terminal

### Commande de base

La méthode la plus simple pour voir les logs d'une exécution :

```bash
gh run view --log
```

Cette commande affiche les logs de la dernière exécution directement dans votre terminal.

### Spécifier une exécution précise

Pour voir les logs d'un run spécifique, utilisez son ID :

```bash
gh run view 9876543210 --log
```

**Exemple de sortie :**
```
✓ CI Pipeline · 9876543210
Triggered via push about 2 hours ago

✓ test	2024-07-20T10:30:00.000Z
Set up job
  Current runner version: '2.317.0'
  Operating System: ubuntu-22.04
  Runner Image: ubuntu22
  Runner Image Provisioner: image-provisioner-ubuntu22

Checkout code
  Syncing repository: username/repo-name
  Fetching the repository
  /usr/bin/git clone --depth 1 --single-branch --branch main https://github.com/username/repo-name /home/runner/work/repo-name/repo-name

Setup Node.js
  Found in cache @ /opt/hostedtoolcache/node/18.17.0/x64
  /opt/hostedtoolcache/node/18.17.0/x64/bin/node
  v18.17.0

Install dependencies
  npm ci
  added 1248 packages in 23s

Run tests
  npm test
  > test
  > jest

  PASS src/utils.test.js
  PASS src/app.test.js

  Test Suites: 2 passed, 2 total
  Tests: 15 passed, 15 total
```

### Filtrer par job

Pour voir uniquement les logs d'un job spécifique :

```bash
gh run view --log --job 12345678901
```

Cela affiche seulement les logs du job avec l'ID spécifié.

## Télécharger les logs en fichiers

### Téléchargement complet

Pour télécharger tous les logs d'une exécution sous forme d'archive :

```bash
gh run download 9876543210
```

Cette commande crée un dossier avec tous les artifacts et logs du run.

### Structure du téléchargement

Après téléchargement, vous obtenez une structure comme :
```
9876543210/
├── logs/
│   ├── job1_log.txt
│   ├── job2_log.txt
│   └── job3_log.txt
└── artifacts/
    ├── build-artifacts.zip
    └── test-reports.zip
```

### Télécharger seulement les logs

Si vous voulez uniquement les logs (sans les artifacts) :

```bash
gh run download 9876543210 --pattern "*.log"
```

## Techniques de recherche dans les logs

### Recherche directe avec grep

Quand les logs sont affichés dans le terminal, vous pouvez les filtrer :

```bash
gh run view --log 9876543210 | grep "ERROR"
```

Cette commande ne montre que les lignes contenant "ERROR".

### Recherche de mots-clés courants

**Rechercher les erreurs :**
```bash
gh run view --log | grep -i "error\|failed\|exception"
```

**Rechercher les avertissements :**
```bash
gh run view --log | grep -i "warning\|warn"
```

**Rechercher une étape spécifique :**
```bash
gh run view --log | grep -A 10 -B 2 "Run tests"
```
(Affiche 10 lignes après et 2 lignes avant "Run tests")

### Sauvegarder les logs pour analyse

Pour sauvegarder les logs dans un fichier pour analyse ultérieure :

```bash
gh run view --log 9876543210 > logs_execution.txt
```

Vous pouvez ensuite analyser ce fichier avec votre éditeur favori.

## Comprendre les logs d'erreur

### Identifier les erreurs

Les erreurs dans les logs sont généralement signalées par :

- ❌ **Icônes d'échec** au début des étapes
- 🔴 **Texte en rouge** (si votre terminal supporte les couleurs)
- **Mots-clés** comme "Error:", "Failed:", "Exception:"
- **Codes de sortie non-nuls** (exit code != 0)

### Exemple d'erreur typique

```
❌ Run tests
npm test
> test
> jest

FAIL src/app.test.js
  ● App › should render correctly

    expect(received).toBe(expected) // Object.is equality

    Expected: "Hello World"
    Received: "Hello Wrold"

Test Suites: 1 failed, 1 passed, 2 total
Tests: 14 passed, 1 failed, 15 total
Error: Process completed with exit code 1.
```

### Interpréter les codes de sortie

- **Exit code 0** : Succès ✅
- **Exit code 1** : Erreur générale ❌
- **Exit code 2** : Mauvaise utilisation d'une commande ❌
- **Exit code 126** : Commande trouvée mais non exécutable ❌
- **Exit code 127** : Commande non trouvée ❌

## Logs pour différents types de workflows

### Workflows de tests

Dans les logs de tests, cherchez :
```
✓ Test results summary
✓ Coverage reports
❌ Failed test details
⚠️ Deprecation warnings
```

### Workflows de build

Dans les logs de build, surveillez :
```
✓ Compilation messages
✓ Bundle size information
❌ Dependency conflicts
⚠️ Build warnings
```

### Workflows de déploiement

Dans les logs de déploiement, vérifiez :
```
✓ Deployment status
✓ Health checks
❌ Connection errors
⚠️ Resource warnings
```

## Conseils pour analyser efficacement

### 🎯 **Lecture stratégique**

1. **Commencez par la fin** : Les erreurs apparaissent souvent à la fin des logs
2. **Cherchez les mots-clés** : "error", "failed", "exception", "denied"
3. **Identifiez l'étape échouée** : Concentrez-vous sur cette section
4. **Remontez le contexte** : Lisez quelques lignes avant l'erreur

### 📊 **Patterns courants d'erreurs**

**Erreur de dépendances :**
```
npm ERR! code ERESOLVE
npm ERR! ERESOLVE unable to resolve dependency tree
```

**Erreur de permissions :**
```
Error: EACCES: permission denied, open '/path/to/file'
```

**Erreur de configuration :**
```
Error: Environment variable 'API_KEY' is not set
```

**Erreur de connexion :**
```
Error: connect ECONNREFUSED 127.0.0.1:5432
```

### 🔍 **Techniques de debugging**

1. **Comparez avec un run réussi** : Téléchargez les logs d'un run qui fonctionnait
2. **Vérifiez les changements récents** : Qu'est-ce qui a changé depuis le dernier succès ?
3. **Reproduisez localement** : Essayez d'exécuter les mêmes commandes sur votre machine

## Gestion des logs volumineux

### Pagination automatique

Quand les logs sont très longs, la CLI GitHub utilise automatiquement un pager (comme `less`). Vous pouvez :

- **Naviguer** : ↑/↓ pour les lignes, Page Up/Down pour les pages
- **Chercher** : `/mot-clé` puis `n` pour suivant, `N` pour précédent
- **Quitter** : `q` pour fermer le pager

### Filtrage intelligent

Pour des logs très volumineux, filtrez dès l'affichage :

```bash
# Voir seulement les erreurs et leur contexte
gh run view --log | grep -C 3 -i "error"

# Voir seulement un job spécifique
gh run view --log --job 12345678901
```

## Automatisation de l'analyse

### Script de monitoring

Vous pouvez créer un script simple pour surveiller les échecs :

```bash
#!/bin/bash
# Vérifie les runs échoués et affiche leurs logs

echo "Recherche des runs échoués..."
failed_runs=$(gh run list --status failure --limit 5 --json id --jq '.[].id')

for run_id in $failed_runs; do
    echo "=== Logs du run $run_id ==="
    gh run view --log $run_id | grep -A 5 -B 5 -i "error"
    echo ""
done
```

### Notifications automatiques

Intégrez la vérification des logs dans vos scripts de déploiement :

```bash
# Vérifier le statut du dernier run
if gh run list --limit 1 --json conclusion --jq '.[0].conclusion' | grep -q "failure"; then
    echo "❌ Le dernier workflow a échoué !"
    gh run view --log | tail -50  # Afficher les 50 dernières lignes
    exit 1
fi
```

## Points clés à retenir

✅ **`gh run view --log`** affiche les logs directement dans le terminal

✅ **`gh run download`** télécharge tous les logs et artifacts en local

✅ **grep et les pipes** permettent de filtrer et chercher dans les logs

✅ **Les codes de sortie** indiquent le type d'erreur rencontré

✅ **L'analyse stratégique** (commencer par la fin) est plus efficace

✅ **La comparaison** avec des runs réussis aide au diagnostic

## Prochaine étape

Maintenant que vous maîtrisez la lecture et l'analyse des logs, nous allons apprendre à déclencher des workflows manuellement pour tester et débugger vos automatisations.


⏭️
