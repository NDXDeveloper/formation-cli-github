🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 6.3 Déclencher des workflows manuellement

## Introduction

Parfois, vous avez besoin d'exécuter un workflow en dehors des déclencheurs automatiques habituels (push, pull request, etc.). C'est là qu'intervient le déclenchement manuel, une fonctionnalité puissante qui vous permet de lancer vos automatisations à la demande.

Dans cette section, nous allons apprendre à configurer et déclencher des workflows manuellement avec la CLI GitHub, que ce soit pour tester une nouvelle fonctionnalité, faire un déploiement d'urgence, ou simplement expérimenter.

## Comprendre le déclenchement manuel

### Qu'est-ce que `workflow_dispatch` ?

Le **workflow_dispatch** est un événement spécial dans GitHub Actions qui permet de déclencher un workflow manuellement. Contrairement aux événements automatiques comme `push` ou `pull_request`, celui-ci nécessite une action humaine délibérée.

### Avantages du déclenchement manuel

- 🎯 **Contrôle précis** : Lancer un workflow quand vous le voulez
- 🧪 **Tests isolés** : Tester des workflows sans modifier le code
- 🚀 **Déploiements planifiés** : Déclencher des mises en production à des moments précis
- 🔧 **Maintenance** : Lancer des tâches de nettoyage ou de maintenance
- 📊 **Rapports à la demande** : Générer des rapports quand nécessaire

## Configuration d'un workflow pour le déclenchement manuel

### Configuration de base

Pour permettre le déclenchement manuel, ajoutez `workflow_dispatch` dans la section `on` de votre workflow :

```yaml
name: Déploiement Manuel

on:
  workflow_dispatch:  # Permet le déclenchement manuel

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Deploy application
        run: |
          echo "Déploiement de l'application..."
          # Vos commandes de déploiement ici
```

### Workflow avec plusieurs déclencheurs

Vous pouvez combiner le déclenchement manuel avec d'autres événements :

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  workflow_dispatch:  # Ajout du déclenchement manuel

jobs:
  # Vos jobs ici...
```

### Ajout de paramètres d'entrée

Le déclenchement manuel peut accepter des paramètres personnalisés :

```yaml
name: Déploiement avec Options

on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environnement de déploiement'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production

      version:
        description: 'Version à déployer'
        required: true
        default: 'latest'
        type: string

      debug:
        description: 'Activer le mode debug'
        required: false
        default: false
        type: boolean

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Afficher les paramètres
        run: |
          echo "Environnement: ${{ github.event.inputs.environment }}"
          echo "Version: ${{ github.event.inputs.version }}"
          echo "Debug: ${{ github.event.inputs.debug }}"

      - name: Déployer
        run: |
          if [ "${{ github.event.inputs.debug }}" == "true" ]; then
            echo "Mode debug activé"
          fi
          echo "Déploiement vers ${{ github.event.inputs.environment }}"
```

## Déclencher un workflow avec la CLI

### Commande de base

Pour déclencher un workflow manuellement :

```bash
gh workflow run "nom-du-workflow"
```

Ou en utilisant le nom du fichier :

```bash
gh workflow run deploy.yml
```

### Spécifier une branche

Par défaut, le workflow s'exécute sur la branche par défaut (généralement `main`). Pour spécifier une autre branche :

```bash
gh workflow run deploy.yml --ref feature-branch
```

### Passer des paramètres

Si votre workflow accepte des paramètres, utilisez l'option `-f` (field) :

```bash
gh workflow run deploy.yml \
  -f environment=production \
  -f version=v2.1.0 \
  -f debug=true
```

### Exemple complet

```bash
# Déclencher un déploiement en production avec debug activé
gh workflow run "Déploiement avec Options" \
  --ref main \
  -f environment=production \
  -f version=v2.1.0 \
  -f debug=true
```

## Lister les workflows disponibles

### Voir tous les workflows

Pour connaître les workflows disponibles dans votre repository :

```bash
gh workflow list
```

**Sortie typique :**
```
NAME                    STATE    ID
CI Pipeline            active   12345678
Déploiement Manuel     active   12345679
Tests de Performance   active   12345680
```

### Obtenir des détails sur un workflow

Pour voir les détails d'un workflow spécifique :

```bash
gh workflow view "Déploiement Manuel"
```

**Sortie typique :**
```
Déploiement Manuel - deploy.yml

ID: 12345679
State: active
Total runs: 15

RUNS
STATUS  TITLE          EVENT              BRANCH  ID          ELAPSED
✓      Manual deploy   workflow_dispatch  main    9876543210  2m 34s
✓      Manual deploy   workflow_dispatch  main    9876543209  2m 12s
```

## Types de paramètres d'entrée

### String (chaîne de caractères)

```yaml
inputs:
  message:
    description: 'Message de commit'
    required: true
    type: string
    default: 'Déploiement automatique'
```

Utilisation :
```bash
gh workflow run deploy.yml -f message="Nouvelle fonctionnalité"
```

### Boolean (booléen)

```yaml
inputs:
  skip_tests:
    description: 'Ignorer les tests'
    required: false
    type: boolean
    default: false
```

Utilisation :
```bash
gh workflow run deploy.yml -f skip_tests=true
```

### Choice (choix multiple)

```yaml
inputs:
  deployment_target:
    description: 'Cible de déploiement'
    required: true
    type: choice
    options:
      - development
      - staging
      - production
    default: 'staging'
```

Utilisation :
```bash
gh workflow run deploy.yml -f deployment_target=production
```

### Environment (environnement)

```yaml
inputs:
  target_env:
    description: 'Environnement cible'
    required: true
    type: environment
```

Utilisation :
```bash
gh workflow run deploy.yml -f target_env=production
```

## Surveillance après déclenchement

### Vérifier que le workflow a démarré

Après avoir déclenché un workflow, vérifiez qu'il s'est bien lancé :

```bash
# Déclencher le workflow
gh workflow run deploy.yml -f environment=staging

# Attendre quelques secondes puis vérifier
sleep 5
gh run list --limit 1
```

### Suivre l'exécution en temps réel

Pour surveiller l'avancement d'un workflow que vous venez de déclencher :

```bash
# Déclencher et suivre immédiatement
gh workflow run deploy.yml -f environment=staging
sleep 10  # Laisser le temps au workflow de démarrer
gh run watch
```

### Workflow complet de déclenchement et surveillance

```bash
#!/bin/bash
# Script pour déclencher et surveiller un déploiement

echo "🚀 Déclenchement du déploiement..."
gh workflow run "Déploiement Manuel" \
  -f environment=production \
  -f version=v1.2.3

echo "⏳ Attente du démarrage..."
sleep 15

echo "📊 Vérification du statut..."
latest_run=$(gh run list --limit 1 --json id --jq '.[0].id')
echo "ID du run: $latest_run"

echo "👀 Surveillance en cours..."
gh run watch $latest_run
```

## Cas d'usage pratiques

### Déploiement d'urgence

Quand vous devez déployer rapidement un hotfix :

```bash
# Basculer sur la branche de hotfix
git checkout hotfix/critical-fix

# Déclencher le déploiement immédiatement
gh workflow run "Emergency Deploy" \
  --ref hotfix/critical-fix \
  -f environment=production \
  -f skip_tests=false  # Toujours tester, même en urgence !
```

### Test de nouvelles fonctionnalités

Pour tester un workflow sur une branche de développement :

```bash
# Tester sur la branche feature
gh workflow run "Integration Tests" \
  --ref feature/new-feature \
  -f test_suite=comprehensive \
  -f debug=true
```

### Génération de rapports périodiques

Pour générer des rapports à la demande :

```bash
# Générer un rapport mensuel
gh workflow run "Monthly Report" \
  -f report_type=monthly \
  -f format=pdf \
  -f email_recipients=team@company.com
```

### Maintenance automatisée

Pour lancer des tâches de maintenance :

```bash
# Nettoyage des logs anciens
gh workflow run "Maintenance Tasks" \
  -f task_type=cleanup \
  -f retention_days=30 \
  -f dry_run=false
```

## Bonnes pratiques

### 🔐 **Sécurité**

- **Limitez les permissions** : N'autorisez le déclenchement manuel que pour les utilisateurs appropriés
- **Validez les entrées** : Toujours valider les paramètres dans votre workflow
- **Loggez les actions** : Gardez une trace de qui déclenche quoi et quand

### 🎯 **Conception**

- **Paramètres explicites** : Donnez des noms clairs et des descriptions détaillées
- **Valeurs par défaut sensées** : Choisissez des défauts sûrs
- **Validation des entrées** : Vérifiez que les paramètres sont valides

### 📊 **Monitoring**

- **Notifications** : Configurez des alertes pour les déclenchements manuels
- **Audit** : Gardez un historique des déclenchements manuels
- **Documentation** : Documentez quand et pourquoi utiliser chaque workflow

## Dépannage courant

### Workflow introuvable

**Erreur :**
```
could not find any workflows named "mon-workflow"
```

**Solutions :**
```bash
# Lister tous les workflows disponibles
gh workflow list

# Utiliser le nom exact ou le nom de fichier
gh workflow run "Mon Workflow Exact"
# ou
gh workflow run mon-workflow.yml
```

### Paramètre invalide

**Erreur :**
```
input 'environment' is not defined in workflow
```

**Solutions :**
- Vérifiez que le paramètre existe dans la configuration du workflow
- Vérifiez l'orthographe exacte du paramètre
- Consultez la définition du workflow : `gh workflow view "nom-workflow"`

### Permissions insuffisantes

**Erreur :**
```
Resource not accessible by integration
```

**Solutions :**
- Vérifiez vos permissions sur le repository
- Assurez-vous d'être authentifié : `gh auth status`
- Contactez l'administrateur du repository si nécessaire

## Points clés à retenir

✅ **`workflow_dispatch`** dans la configuration YAML permet le déclenchement manuel

✅ **`gh workflow run`** déclenche un workflow depuis la CLI

✅ **Les paramètres** permettent de personnaliser l'exécution

✅ **`--ref`** spécifie la branche d'exécution

✅ **La surveillance** après déclenchement est essentielle

✅ **Les bonnes pratiques** de sécurité sont cruciales

## Prochaine étape

Maintenant que vous savez déclencher des workflows manuellement, nous allons apprendre les techniques de debugging pour résoudre efficacement les échecs de CI/CD.


⏭️
