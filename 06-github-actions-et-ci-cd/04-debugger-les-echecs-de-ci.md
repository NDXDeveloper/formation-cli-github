🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 6.4 Débugger les échecs de CI

## Introduction

Le debugging des échecs de CI (Intégration Continue) est une compétence essentielle pour tout développeur utilisant GitHub Actions. Les échecs peuvent sembler intimidants au début, mais avec la bonne méthodologie et les outils de la CLI GitHub, vous pouvez rapidement identifier et résoudre la plupart des problèmes.

Dans cette section, nous allons apprendre une approche structurée pour diagnostiquer et corriger les échecs de CI, en utilisant des techniques éprouvées et des exemples concrets.

## Comprendre les échecs de CI

### Qu'est-ce qu'un échec de CI ?

Un **échec de CI** survient quand un ou plusieurs jobs d'un workflow ne se terminent pas avec succès. Cela peut être dû à :

- 🐛 **Erreurs dans le code** (tests qui échouent, bugs)
- 🔧 **Problèmes de configuration** (variables manquantes, mauvaise configuration)
- 📦 **Problèmes de dépendances** (packages manquants, versions incompatibles)
- 🌐 **Problèmes d'environnement** (services indisponibles, timeouts)
- ⚙️ **Problèmes de workflow** (erreurs dans le YAML, permissions)

### Types d'échecs courants

**Échec de test :**
```
❌ Tests failed: 3 passed, 2 failed
```

**Échec de build :**
```
❌ Build failed: compilation error in src/main.js
```

**Échec de déploiement :**
```
❌ Deployment failed: connection refused
```

**Échec de configuration :**
```
❌ Environment variable 'API_KEY' not found
```

## Méthodologie de debugging en 6 étapes

### Étape 1 : Identifier l'échec rapidement

Commencez par obtenir une vue d'ensemble des échecs récents :

```bash
# Voir tous les échecs récents
gh run list --status failure --limit 10
```

**Exemple de sortie :**
```
STATUS  TITLE                WORKFLOW     BRANCH  EVENT  ID          ELAPSED
❌      Fix user login       CI Pipeline  main    push   9876543210  3m 45s
❌      Add new feature      CI Pipeline  feature pull   9876543209  2m 12s
✓       Update docs          CI Pipeline  main    push   9876543208  1m 30s
```

Identifiez le run problématique et notez son ID.

### Étape 2 : Examiner le résumé de l'échec

Regardez les détails généraux de l'exécution échouée :

```bash
gh run view 9876543210
```

**Sortie typique :**
```
❌ CI Pipeline · 9876543210
Triggered via push about 10 minutes ago

JOBS
✓ setup (ID 12345678901)
❌ test (ID 12345678902)
✓ lint (ID 12345678903)
⏭ deploy (ID 12345678904) - skipped
```

Cette vue vous montre immédiatement quel job a échoué.

### Étape 3 : Analyser les logs du job échoué

Concentrez-vous sur le job qui a échoué :

```bash
gh run view --log --job 12345678902
```

**Exemple de logs d'erreur :**
```
❌ test
Run npm test
  npm test
  > myapp@1.0.0 test
  > jest

  FAIL src/auth/login.test.js
    ● Login function › should validate email format

      expect(received).toBe(expected) // Object.is equality

      Expected: true
      Received: false

        at Object.<anonymous> (src/auth/login.test.js:15:23)

  Test Suites: 1 failed, 3 passed, 4 total
  Tests:       1 failed, 23 passed, 24 total

  Error: Process completed with exit code 1.
```

### Étape 4 : Localiser la cause racine

Analysez les informations pour identifier le problème exact :

1. **Quel est le message d'erreur principal ?**
2. **Dans quel fichier l'erreur s'est-elle produite ?**
3. **À quelle ligne exactement ?**
4. **Quel était le comportement attendu vs réel ?**

Dans notre exemple :
- ❌ Test échoué dans `src/auth/login.test.js` ligne 15
- ❌ Fonction de validation d'email retourne `false` au lieu de `true`
- ❌ Le problème est dans la logique de validation

### Étape 5 : Rechercher dans l'historique

Comparez avec les exécutions précédentes pour comprendre quand le problème est apparu :

```bash
# Voir les dernières exécutions réussies
gh run list --status success --limit 5

# Comparer avec un run qui fonctionnait
gh run view 9876543208 --log --job <ID_JOB_TEST>
```

### Étape 6 : Reproduire et corriger

1. **Reproduisez localement** le problème
2. **Corrigez** le code
3. **Testez** la correction en local
4. **Poussez** et vérifiez que le CI passe

## Techniques avancées de debugging

### Utiliser les artifacts pour plus d'informations

Certains workflows génèrent des artifacts utiles pour le debugging :

```bash
# Télécharger tous les artifacts d'un run
gh run download 9876543210

# Examiner le contenu
ls -la 9876543210/
cat 9876543210/test-results/junit.xml
```

### Debugging avec des logs étendus

Activez des logs plus détaillés en ajoutant des variables d'environnement dans votre workflow :

```yaml
env:
  ACTIONS_STEP_DEBUG: true
  ACTIONS_RUNNER_DEBUG: true
```

### Recherche avancée dans les logs

**Chercher des patterns spécifiques :**
```bash
# Chercher toutes les erreurs
gh run view --log | grep -i "error\|exception\|failed"

# Chercher avec contexte (5 lignes avant/après)
gh run view --log | grep -C 5 -i "npm ERR"

# Extraire seulement les messages d'erreur Jest
gh run view --log | grep -A 10 "FAIL"
```

**Sauvegarder pour analyse approfondie :**
```bash
# Sauvegarder les logs dans un fichier
gh run view --log 9876543210 > debug_logs.txt

# Analyser avec votre éditeur favori
code debug_logs.txt  # VS Code
vim debug_logs.txt   # Vim
```

## Patterns d'erreurs courants et solutions

### Erreurs de tests

**Pattern :**
```
FAIL src/components/Button.test.js
  ● Button › should render correctly
    Expected: "Submit"
    Received: "Submitt"
```

**Solutions typiques :**
1. Corriger le test si le comportement est correct
2. Corriger le code si le test est correct
3. Vérifier les snapshots Jest avec `npm test -- -u`

### Erreurs de dépendances

**Pattern :**
```
npm ERR! code ERESOLVE
npm ERR! ERESOLVE unable to resolve dependency tree
```

**Solutions :**
```bash
# Nettoyer et réinstaller
rm -rf node_modules package-lock.json
npm install

# Forcer la résolution
npm install --force

# Mettre à jour les dépendances
npm update
```

### Erreurs d'environnement

**Pattern :**
```
Error: Environment variable 'DATABASE_URL' is not defined
```

**Solutions :**
1. Ajouter la variable dans les secrets du repository
2. Vérifier la configuration du workflow
3. Utiliser des valeurs par défaut pour les tests

```yaml
env:
  DATABASE_URL: ${{ secrets.DATABASE_URL || 'sqlite://test.db' }}
```

### Erreurs de permissions

**Pattern :**
```
Error: EACCES: permission denied, open '/path/to/file'
```

**Solutions :**
```yaml
# Ajouter des permissions explicites
permissions:
  contents: read
  packages: write

# Ou modifier les permissions dans les steps
- name: Fix permissions
  run: chmod +x ./scripts/deploy.sh
```

### Timeouts

**Pattern :**
```
Error: The operation was canceled.
```

**Solutions :**
```yaml
# Augmenter le timeout
jobs:
  test:
    timeout-minutes: 30  # Défaut: 360 minutes

# Ou pour une step spécifique
- name: Long running task
  timeout-minutes: 10
  run: ./long-script.sh
```

## Workflows de debugging spécialisés

### Workflow de debug temporaire

Créez un workflow spécial pour débugger :

```yaml
name: Debug Workflow

on:
  workflow_dispatch:
    inputs:
      debug_level:
        description: 'Niveau de debug'
        required: true
        default: 'info'
        type: choice
        options: ['info', 'debug', 'trace']

jobs:
  debug:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Debug Environment
        run: |
          echo "=== Environment Variables ==="
          env | sort
          echo "=== System Info ==="
          uname -a
          echo "=== Disk Space ==="
          df -h
          echo "=== Memory ==="
          free -h

      - name: Debug Dependencies
        run: |
          echo "=== Node Version ==="
          node --version
          npm --version
          echo "=== Installed Packages ==="
          npm list --depth=0

      - name: Debug Application
        run: |
          echo "=== Application Files ==="
          find . -name "*.js" -type f | head -20
          echo "=== Configuration Files ==="
          ls -la *.json *.yml *.yaml 2>/dev/null || true
```

### Script de diagnostic automatique

Créez un script pour diagnostiquer rapidement les problèmes :

```bash
#!/bin/bash
# debug-ci.sh - Script de diagnostic rapide

echo "🔍 Diagnostic des échecs CI"
echo "=========================="

# Trouver les échecs récents
echo "📊 Échecs récents:"
gh run list --status failure --limit 5

echo ""
echo "🎯 Analyse du dernier échec:"
last_failed=$(gh run list --status failure --limit 1 --json id --jq '.[0].id')

if [ -n "$last_failed" ]; then
    echo "ID du run échoué: $last_failed"

    # Montrer le résumé
    echo ""
    echo "📋 Résumé de l'échec:"
    gh run view $last_failed

    # Extraire les erreurs principales
    echo ""
    echo "❌ Erreurs détectées:"
    gh run view --log $last_failed | grep -i "error\|failed\|exception" | head -10

    echo ""
    echo "💡 Pour plus de détails:"
    echo "gh run view --log $last_failed"
else
    echo "✅ Aucun échec récent trouvé!"
fi
```

Rendez-le exécutable et utilisez-le :
```bash
chmod +x debug-ci.sh
./debug-ci.sh
```

## Prévention des échecs

### Tests locaux avant push

Configurez des hooks pre-push pour éviter les échecs :

```bash
# .git/hooks/pre-push
#!/bin/bash
echo "🧪 Exécution des tests avant push..."
npm test
if [ $? -ne 0 ]; then
    echo "❌ Tests échoués - push annulé"
    exit 1
fi
echo "✅ Tests réussis - push autorisé"
```

### Surveillance proactive

Surveillez la santé de vos workflows :

```bash
#!/bin/bash
# check-ci-health.sh

# Vérifier le taux de succès des 20 derniers runs
total_runs=$(gh run list --limit 20 --json conclusion --jq 'length')
failed_runs=$(gh run list --limit 20 --json conclusion --jq '[.[] | select(.conclusion == "failure")] | length')

success_rate=$(echo "scale=1; ($total_runs - $failed_runs) * 100 / $total_runs" | bc)

echo "📊 Santé du CI:"
echo "Total des runs: $total_runs"
echo "Échecs: $failed_runs"
echo "Taux de succès: $success_rate%"

if (( $(echo "$success_rate < 80" | bc -l) )); then
    echo "⚠️  ATTENTION: Taux de succès faible!"
fi
```

## Conseils pour débutants

### 🎯 **Approche méthodique**

1. **Ne paniquez pas** - Les échecs de CI sont normaux
2. **Lisez attentivement** - Le message d'erreur contient souvent la solution
3. **Reproduisez localement** - C'est souvent plus facile à débugger
4. **Cherchez dans Google** - Vous n'êtes probablement pas le premier à avoir ce problème

### 🔍 **Outils d'aide**

- **GitHub Copilot** peut suggérer des corrections
- **Stack Overflow** pour les erreurs courantes
- **Documentation officielle** des outils utilisés
- **Issues GitHub** du projet concerné

### 📚 **Ressources d'apprentissage**

- Gardez un journal des erreurs rencontrées et leurs solutions
- Partagez avec votre équipe les patterns d'erreurs récurrents
- Créez une base de connaissances des solutions courantes

## Points clés à retenir

✅ **Méthodologie structurée** en 6 étapes pour débugger efficacement

✅ **Analyse des logs** avec grep et autres outils de recherche

✅ **Comparaison historique** pour identifier quand le problème est apparu

✅ **Patterns d'erreurs courants** et leurs solutions typiques

✅ **Prévention** avec tests locaux et surveillance proactive

✅ **Outils spécialisés** pour le diagnostic automatique

## Conclusion de la section GitHub Actions

Félicitations ! Vous maîtrisez maintenant les aspects essentiels de GitHub Actions avec la CLI :

- 👀 **Visualisation** des workflows et de leur état
- 📋 **Téléchargement et analyse** des logs d'exécution
- 🚀 **Déclenchement manuel** de workflows avec paramètres
- 🐛 **Debugging méthodique** des échecs de CI

Ces compétences vous permettront de gérer efficacement vos pipelines d'intégration continue et de résoudre rapidement les problèmes qui peuvent survenir.


⏭️
