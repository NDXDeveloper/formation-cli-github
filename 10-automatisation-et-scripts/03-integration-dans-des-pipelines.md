🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 10.3 Intégration dans des pipelines

## Qu'est-ce qu'un pipeline ?

Un pipeline est comme une chaîne de montage automatisée pour votre code. Imaginez une usine où chaque étape se déclenche automatiquement après la précédente : tests → compilation → déploiement → notifications. GitHub CLI s'intègre parfaitement dans ces processus pour automatiser les interactions avec GitHub.

**Exemple simple de pipeline :**
```
Code modifié → Tests automatiques → Build réussi → Déploiement → Notification équipe
     ↓              ↓                  ↓              ↓              ↓
  git push    →  npm test       →   docker build  →  deploy.sh  →  gh issue comment
```

## Types de pipelines

### CI/CD (Continuous Integration/Continuous Deployment)

**Continuous Integration (CI) :**
- Tests automatiques à chaque push
- Vérification de la qualité du code
- Validation des PRs avant merge

**Continuous Deployment (CD) :**
- Déploiement automatique après validation
- Création de releases
- Notifications d'équipe

### Pipelines GitHub Actions

GitHub Actions est le système de CI/CD intégré à GitHub. GitHub CLI peut y être utilisé pour :
- Créer des issues automatiquement
- Commenter des PRs
- Gérer les releases
- Synchroniser avec d'autres outils

### Pipelines externes

GitHub CLI fonctionne aussi avec :
- **Jenkins** : Serveur CI/CD populaire
- **GitLab CI** : Si vous migrez ou synchronisez
- **CircleCI** : Service CI/CD cloud
- **Azure DevOps** : Solution Microsoft

## GitHub Actions avec GitHub CLI

### Pipeline de base

Créez le fichier `.github/workflows/ci.yml` :

```yaml
name: CI Pipeline avec GitHub CLI

# Déclencher le pipeline
on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test-and-deploy:
    runs-on: ubuntu-latest

    steps:
    # 1. Récupérer le code
    - name: Checkout code
      uses: actions/checkout@v3

    # 2. Configurer Node.js (exemple)
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'

    # 3. Installer les dépendances
    - name: Install dependencies
      run: npm install

    # 4. Lancer les tests
    - name: Run tests
      run: npm test

    # 5. Utiliser GitHub CLI pour commenter la PR
    - name: Comment PR on success
      if: github.event_name == 'pull_request' && success()
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: |
        gh pr comment ${{ github.event.number }} \
          --body "✅ Tests passés avec succès! Ready for review."

    # 6. Créer une issue en cas d'échec
    - name: Create issue on failure
      if: failure()
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: |
        gh issue create \
          --title "❌ Build failed for commit ${{ github.sha }}" \
          --body "Le build a échoué sur la branche ${{ github.ref_name }}.

        **Commit:** ${{ github.sha }}
        **Auteur:** ${{ github.actor }}
        **Workflow:** ${{ github.workflow }}

        Merci de vérifier les logs: ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}" \
          --assignee ${{ github.actor }} \
          --label "bug,ci-failure"
```

### Pipeline de release automatique

```yaml
name: Auto Release

on:
  push:
    tags:
      - 'v*'  # Déclenché par les tags comme v1.0.0

jobs:
  create-release:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v3
      with:
        fetch-depth: 0  # Récupérer tout l'historique

    # Générer les notes de release automatiquement
    - name: Generate changelog
      id: changelog
      run: |
        # Récupérer le tag précédent
        PREVIOUS_TAG=$(git describe --tags --abbrev=0 HEAD^)
        CURRENT_TAG=${GITHUB_REF#refs/tags/}

        echo "## What's Changed" > CHANGELOG.md
        echo "" >> CHANGELOG.md

        # Générer la liste des commits
        git log ${PREVIOUS_TAG}..HEAD --pretty=format:"- %s by @%an" >> CHANGELOG.md

        # Lire le changelog généré
        CHANGELOG=$(cat CHANGELOG.md)
        echo "changelog<<EOF" >> $GITHUB_OUTPUT
        echo "$CHANGELOG" >> $GITHUB_OUTPUT
        echo "EOF" >> $GITHUB_OUTPUT

    # Créer la release avec GitHub CLI
    - name: Create GitHub Release
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: |
        TAG_NAME=${GITHUB_REF#refs/tags/}

        gh release create "$TAG_NAME" \
          --title "Release $TAG_NAME" \
          --notes "${{ steps.changelog.outputs.changelog }}" \
          --latest

    # Notifier l'équipe
    - name: Notify team
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: |
        TAG_NAME=${GITHUB_REF#refs/tags/}

        gh issue create \
          --title "🚀 Release $TAG_NAME deployed" \
          --body "La version $TAG_NAME a été déployée avec succès!

        **Release Notes:** https://github.com/${{ github.repository }}/releases/tag/$TAG_NAME

        Merci à tous les contributeurs! 🎉" \
          --label "release,announcement" \
          --assignee "@team-leads"
```

### Pipeline de validation des PRs

```yaml
name: PR Validation

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  validate-pr:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v3

    # Vérifier le format du titre de la PR
    - name: Validate PR title
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        PR_TITLE: ${{ github.event.pull_request.title }}
      run: |
        # Vérifier que le titre suit le format conventionnel
        if echo "$PR_TITLE" | grep -E "^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .+"; then
          echo "✅ Titre de PR valide: $PR_TITLE"

          gh pr comment ${{ github.event.number }} \
            --body "✅ **Format du titre validé**

            Votre titre de PR suit les conventions! Merci."
        else
          echo "❌ Titre de PR invalide: $PR_TITLE"

          gh pr comment ${{ github.event.number }} \
            --body "❌ **Format du titre incorrect**

            Veuillez utiliser le format: \`type: description\`

            **Types acceptés:** feat, fix, docs, style, refactor, test, chore

            **Exemples:**
            - \`feat: add user authentication\`
            - \`fix: resolve login bug\`
            - \`docs: update API documentation\`"

          exit 1
        fi

    # Vérifier la taille de la PR
    - name: Check PR size
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: |
        # Compter les changements
        CHANGES=$(gh pr diff ${{ github.event.number }} --name-only | wc -l)
        ADDITIONS=$(gh pr view ${{ github.event.number }} --json additions --jq .additions)

        echo "📊 Statistiques de la PR:"
        echo "   Fichiers modifiés: $CHANGES"
        echo "   Lignes ajoutées: $ADDITIONS"

        if [ "$ADDITIONS" -gt 500 ]; then
          gh pr comment ${{ github.event.number }} \
            --body "⚠️ **PR importante détectée**

            Cette PR contient plus de 500 lignes ajoutées ($ADDITIONS lignes).

            **Recommandations:**
            - Considérez diviser en plusieurs PRs plus petites
            - Assurez-vous que la description est détaillée
            - Demandez une review approfondie"
        fi

    # Assigner automatiquement des reviewers
    - name: Auto-assign reviewers
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: |
        # Récupérer l'auteur de la PR
        PR_AUTHOR=${{ github.event.pull_request.user.login }}

        # Lister les reviewers possibles (à adapter selon votre équipe)
        POSSIBLE_REVIEWERS=("alice" "bob" "charlie")

        # Filtrer l'auteur de la liste
        REVIEWERS=()
        for reviewer in "${POSSIBLE_REVIEWERS[@]}"; do
          if [ "$reviewer" != "$PR_AUTHOR" ]; then
            REVIEWERS+=("$reviewer")
          fi
        done

        # Assigner les 2 premiers reviewers disponibles
        if [ ${#REVIEWERS[@]} -gt 0 ]; then
          REVIEWER_LIST=$(printf ",%s" "${REVIEWERS[@]:0:2}")
          REVIEWER_LIST=${REVIEWER_LIST:1}  # Supprimer la première virgule

          gh pr edit ${{ github.event.number }} \
            --add-reviewer "$REVIEWER_LIST"

          echo "👥 Reviewers assignés: $REVIEWER_LIST"
        fi
```

## Intégration avec Jenkins

### Script Jenkins utilisant GitHub CLI

```bash
#!/bin/bash
# jenkins-github-integration.sh

set -e

# === Configuration Jenkins ===
# Variables d'environnement configurées dans Jenkins:
# - GITHUB_TOKEN (credential)
# - GITHUB_REPOSITORY (ex: "user/repo")
# - BUILD_NUMBER (fourni par Jenkins)

echo "🏗️  Démarrage du build Jenkins #$BUILD_NUMBER"

# === Étape 1: Tests ===
echo "🧪 Lancement des tests..."

if npm test; then
    echo "✅ Tests réussis"
    TEST_STATUS="success"
    TEST_MESSAGE="Tous les tests sont passés avec succès"
else
    echo "❌ Tests échoués"
    TEST_STATUS="failure"
    TEST_MESSAGE="Certains tests ont échoué"
fi

# === Étape 2: Build ===
echo "📦 Build de l'application..."

if npm run build; then
    echo "✅ Build réussi"
    BUILD_STATUS="success"
    BUILD_MESSAGE="Build généré avec succès"
else
    echo "❌ Build échoué"
    BUILD_STATUS="failure"
    BUILD_MESSAGE="Erreur lors du build"
fi

# === Étape 3: Mise à jour GitHub ===
echo "📤 Mise à jour du statut sur GitHub..."

# Si c'est une PR, commenter avec les résultats
if [ -n "$CHANGE_ID" ]; then  # Variable Jenkins pour les PRs
    COMMENT_BODY="## 🏗️ Build Jenkins #$BUILD_NUMBER

**Tests:** $TEST_STATUS
$TEST_MESSAGE

**Build:** $BUILD_STATUS
$BUILD_MESSAGE

**Timestamp:** $(date)
**Jenkins URL:** $BUILD_URL"

    gh pr comment "$CHANGE_ID" --body "$COMMENT_BODY"
fi

# === Étape 4: Déploiement (si tout est OK) ===
if [ "$TEST_STATUS" = "success" ] && [ "$BUILD_STATUS" = "success" ]; then
    echo "🚀 Déploiement..."

    # Script de déploiement spécifique à votre projet
    ./deploy.sh

    # Créer une issue de notification de déploiement
    gh issue create \
        --title "🚀 Deployment successful - Build #$BUILD_NUMBER" \
        --body "Deployment completed successfully.

**Build Number:** $BUILD_NUMBER
**Branch:** $GIT_BRANCH
**Commit:** $GIT_COMMIT
**Deployed by:** Jenkins

All systems are operational." \
        --label "deployment,success"

    echo "✅ Déploiement terminé avec succès"
else
    echo "❌ Déploiement annulé à cause des erreurs"

    # Créer une issue pour signaler le problème
    gh issue create \
        --title "❌ Build failed - Build #$BUILD_NUMBER" \
        --body "Build failed and deployment was cancelled.

**Build Number:** $BUILD_NUMBER
**Branch:** $GIT_BRANCH
**Commit:** $GIT_COMMIT
**Test Status:** $TEST_STATUS
**Build Status:** $BUILD_STATUS

Please check Jenkins logs: $BUILD_URL" \
        --label "ci-failure,urgent" \
        --assignee "@devops-team"

    exit 1
fi
```

### Configuration Jenkins Pipeline (Jenkinsfile)

```groovy
pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('github-token')
        NODE_VERSION = '18'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm

                script {
                    // Commenter le début du build sur la PR
                    if (env.CHANGE_ID) {
                        sh '''
                            gh pr comment ${CHANGE_ID} \
                                --body "🏗️ **Build started** - Jenkins #${BUILD_NUMBER}

                            Build in progress... Please wait.

                            Jenkins URL: ${BUILD_URL}"
                        '''
                    }
                }
            }
        }

        stage('Setup') {
            steps {
                sh '''
                    echo "📦 Installing dependencies..."
                    npm install
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    echo "🧪 Running tests..."
                    npm test
                '''
            }

            post {
                always {
                    // Publier les résultats des tests
                    publishTestResults testResultsPattern: 'test-results.xml'
                }
            }
        }

        stage('Build') {
            steps {
                sh '''
                    echo "🔨 Building application..."
                    npm run build
                '''
            }

            post {
                success {
                    archiveArtifacts artifacts: 'dist/**', fingerprint: true
                }
            }
        }

        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                sh '''
                    echo "🚀 Deploying to production..."
                    ./scripts/deploy.sh
                '''

                script {
                    // Créer une release si sur main
                    sh '''
                        VERSION=$(node -p "require('./package.json').version")

                        gh release create "v$VERSION" \
                            --title "Release v$VERSION" \
                            --notes "Automated release from Jenkins build #${BUILD_NUMBER}" \
                            --target main
                    '''
                }
            }
        }
    }

    post {
        success {
            script {
                if (env.CHANGE_ID) {
                    sh '''
                        gh pr comment ${CHANGE_ID} \
                            --body "✅ **Build successful** - Jenkins #${BUILD_NUMBER}

                        All checks passed! This PR is ready for review.

                        Build artifacts: ${BUILD_URL}artifact/"
                    '''
                }
            }
        }

        failure {
            script {
                if (env.CHANGE_ID) {
                    sh '''
                        gh pr comment ${CHANGE_ID} \
                            --body "❌ **Build failed** - Jenkins #${BUILD_NUMBER}

                        Something went wrong during the build process.

                        Check the logs: ${BUILD_URL}console

                        Please fix the issues and push a new commit."
                    '''
                } else {
                    // Créer une issue pour les échecs sur main
                    sh '''
                        gh issue create \
                            --title "❌ Build failure on main branch - #${BUILD_NUMBER}" \
                            --body "Build failed on main branch.

                        **Build:** #${BUILD_NUMBER}
                        **Commit:** ${GIT_COMMIT}
                        **Logs:** ${BUILD_URL}console

                        This needs immediate attention!" \
                            --label "urgent,ci-failure" \
                            --assignee "@devops-team"
                    '''
                }
            }
        }

        always {
            // Nettoyer les artifacts temporaires
            cleanWs()
        }
    }
}
```

## Intégration avec d'autres services

### Pipeline GitLab CI avec GitHub CLI

```yaml
# .gitlab-ci.yml
stages:
  - test
  - build
  - deploy
  - notify

variables:
  # Configuration GitLab CI
  GITHUB_REPO: "username/project"

before_script:
  # Installer GitHub CLI dans le conteneur GitLab
  - apt-get update -qq && apt-get install -y -qq git curl
  - curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | gpg --dearmor -o /usr/share/keyrings/githubcli-archive-keyring.gpg
  - echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null
  - apt-get update && apt-get install gh

test:
  stage: test
  script:
    - npm install
    - npm test
  after_script:
    # Créer une issue si les tests échouent
    - |
      if [ $CI_JOB_STATUS = "failed" ]; then
        gh issue create \
          --title "❌ Tests failed in GitLab CI - Pipeline $CI_PIPELINE_ID" \
          --body "Tests failed in GitLab CI pipeline.

        **Pipeline:** $CI_PIPELINE_URL
        **Commit:** $CI_COMMIT_SHA
        **Branch:** $CI_COMMIT_REF_NAME
        **Author:** $CI_COMMIT_AUTHOR

        Please check the GitLab CI logs for details." \
          --label "ci-failure,gitlab" \
          --assignee "$CI_COMMIT_AUTHOR"
      fi

deploy:
  stage: deploy
  script:
    - echo "Deploying application..."
    - ./deploy.sh
  after_script:
    # Notifier le déploiement réussi
    - |
      gh issue create \
        --title "🚀 Deployment successful - GitLab Pipeline $CI_PIPELINE_ID" \
        --body "Application deployed successfully from GitLab CI.

      **Pipeline:** $CI_PIPELINE_URL
      **Environment:** production
      **Deployed by:** $CI_COMMIT_AUTHOR
      **Version:** $CI_COMMIT_TAG

      All systems operational!" \
        --label "deployment,success"
  only:
    - main
```

### Script de synchronisation multi-plateformes

```bash
#!/bin/bash
# sync-platforms.sh - Synchroniser GitHub avec d'autres plateformes

set -e

echo "🔄 Synchronisation multi-plateformes"

# === Configuration ===
GITHUB_REPO="$1"
JIRA_PROJECT="$2"
SLACK_CHANNEL="$3"

if [ -z "$GITHUB_REPO" ]; then
    echo "❌ Usage: $0 <github-repo> [jira-project] [slack-channel]"
    exit 1
fi

# === Récupérer les dernières PRs mergées ===
echo "📋 Récupération des PRs récemment mergées..."

MERGED_PRS=$(gh pr list \
    --repo "$GITHUB_REPO" \
    --state merged \
    --limit 5 \
    --json number,title,author,mergedAt \
    --jq '.[] | select(.mergedAt > (now - 86400 | strftime("%Y-%m-%dT%H:%M:%SZ")))')

if [ -z "$MERGED_PRS" ]; then
    echo "ℹ️  Aucune PR mergée dans les dernières 24h"
    exit 0
fi

# === Traitement de chaque PR ===
echo "$MERGED_PRS" | jq -r '@base64' | while read -r pr_data; do
    PR_INFO=$(echo "$pr_data" | base64 --decode)

    PR_NUMBER=$(echo "$PR_INFO" | jq -r '.number')
    PR_TITLE=$(echo "$PR_INFO" | jq -r '.title')
    PR_AUTHOR=$(echo "$PR_INFO" | jq -r '.author.login')

    echo "🔄 Traitement de la PR #$PR_NUMBER: $PR_TITLE"

    # Synchronisation avec JIRA (si configuré)
    if [ -n "$JIRA_PROJECT" ]; then
        echo "  📝 Création de ticket JIRA..."

        # Extraire le numéro de ticket JIRA du titre (si présent)
        JIRA_TICKET=$(echo "$PR_TITLE" | grep -oE "$JIRA_PROJECT-[0-9]+" || true)

        if [ -n "$JIRA_TICKET" ]; then
            # Commenter le ticket JIRA existant
            curl -X POST \
                -H "Authorization: Bearer $JIRA_TOKEN" \
                -H "Content-Type: application/json" \
                "https://your-domain.atlassian.net/rest/api/3/issue/$JIRA_TICKET/comment" \
                -d "{
                    \"body\": {
                        \"content\": [
                            {
                                \"content\": [
                                    {
                                        \"text\": \"✅ PR #$PR_NUMBER merged: $PR_TITLE\n\nAuthor: @$PR_AUTHOR\nGitHub: https://github.com/$GITHUB_REPO/pull/$PR_NUMBER\",
                                        \"type\": \"text\"
                                    }
                                ],
                                \"type\": \"paragraph\"
                            }
                        ],
                        \"type\": \"doc\",
                        \"version\": 1
                    }
                }"
        else
            echo "  ⚠️  Aucun ticket JIRA trouvé dans le titre"
        fi
    fi

    # Notification Slack (si configuré)
    if [ -n "$SLACK_CHANNEL" ] && [ -n "$SLACK_WEBHOOK_URL" ]; then
        echo "  💬 Notification Slack..."

        curl -X POST \
            -H "Content-type: application/json" \
            "$SLACK_WEBHOOK_URL" \
            -d "{
                \"channel\": \"$SLACK_CHANNEL\",
                \"username\": \"GitHub Bot\",
                \"icon_emoji\": \":github:\",
                \"text\": \"✅ PR merged in $GITHUB_REPO\",
                \"attachments\": [
                    {
                        \"color\": \"good\",
                        \"title\": \"#$PR_NUMBER: $PR_TITLE\",
                        \"title_link\": \"https://github.com/$GITHUB_REPO/pull/$PR_NUMBER\",
                        \"fields\": [
                            {
                                \"title\": \"Author\",
                                \"value\": \"@$PR_AUTHOR\",
                                \"short\": true
                            },
                            {
                                \"title\": \"Repository\",
                                \"value\": \"$GITHUB_REPO\",
                                \"short\": true
                            }
                        ]
                    }
                ]
            }"
    fi
done

echo "✅ Synchronisation terminée"
```

## Monitoring et debugging des pipelines

### Script de monitoring de pipelines

```bash
#!/bin/bash
# monitor-pipelines.sh - Surveiller l'état des pipelines

echo "📊 Monitoring des pipelines GitHub"

# === Configuration ===
REPOS=("org/repo1" "org/repo2" "user/personal-project")
ALERT_THRESHOLD_MINUTES=30

# === Fonction de vérification ===
check_workflow_runs() {
    local repo="$1"

    echo "🔍 Vérification de $repo..."

    # Récupérer les derniers workflow runs
    RECENT_RUNS=$(gh run list \
        --repo "$repo" \
        --limit 10 \
        --json status,conclusion,createdAt,workflowName,htmlUrl)

    # Vérifier les runs en cours depuis trop longtemps
    echo "$RECENT_RUNS" | jq -r '.[] | select(.status == "in_progress")' | while read -r run; do
        CREATED_AT=$(echo "$run" | jq -r '.createdAt')
        WORKFLOW_NAME=$(echo "$run" | jq -r '.workflowName')
        RUN_URL=$(echo "$run" | jq -r '.htmlUrl')

        # Calculer la durée en minutes
        CREATED_TIMESTAMP=$(date -d "$CREATED_AT" +%s)
        CURRENT_TIMESTAMP=$(date +%s)
        DURATION_MINUTES=$(( (CURRENT_TIMESTAMP - CREATED_TIMESTAMP) / 60 ))

        if [ $DURATION_MINUTES -gt $ALERT_THRESHOLD_MINUTES ]; then
            echo "⚠️  Workflow '$WORKFLOW_NAME' en cours depuis ${DURATION_MINUTES}min"

            # Créer une issue d'alerte
            gh issue create \
                --repo "$repo" \
                --title "⚠️ Long-running workflow detected: $WORKFLOW_NAME" \
                --body "A workflow has been running for more than $ALERT_THRESHOLD_MINUTES minutes.

**Workflow:** $WORKFLOW_NAME
**Duration:** ${DURATION_MINUTES} minutes
**URL:** $RUN_URL
**Started:** $CREATED_AT

This might indicate a stuck process or infinite loop." \
                --label "monitoring,alert" \
                --assignee "@devops-team"
        fi
    done

    # Compter les échecs récents
    FAILED_COUNT=$(echo "$RECENT_RUNS" | jq '[.[] | select(.conclusion == "failure")] | length')

    if [ "$FAILED_COUNT" -gt 3 ]; then
        echo "❌ $FAILED_COUNT échecs récents détectés pour $repo"

        gh issue create \
            --repo "$repo" \
            --title "❌ Multiple workflow failures detected" \
            --body "Multiple workflow failures detected in the last 10 runs.

**Failed runs:** $FAILED_COUNT/10
**Repository:** $repo

This requires immediate attention to ensure CI/CD stability." \
            --label "ci-failure,urgent" \
            --assignee "@devops-team"
    fi
}

# === Vérification de tous les repos ===
for repo in "${REPOS[@]}"; do
    check_workflow_runs "$repo"
    echo ""
done

echo "✅ Monitoring terminé"
```

### Debugging d'un pipeline échoué

```bash
#!/bin/bash
# debug-pipeline.sh - Débugger un pipeline échoué

REPO="$1"
RUN_ID="$2"

if [ -z "$REPO" ] || [ -z "$RUN_ID" ]; then
    echo "❌ Usage: $0 <repo> <run-id>"
    echo "   Exemple: $0 user/project 1234567890"
    exit 1
fi

echo "🔍 Debug du pipeline $RUN_ID dans $REPO"

# === Informations générales ===
echo "📋 Informations générales:"
gh run view "$RUN_ID" --repo "$REPO"

echo ""
echo "📝 Jobs et étapes:"
gh run view "$RUN_ID" --repo "$REPO" --log

# === Télécharger les logs ===
echo ""
echo "💾 Téléchargement des logs..."
gh run download "$RUN_ID" --repo "$REPO" --dir "logs-$RUN_ID"

echo "✅ Logs téléchargés dans logs-$RUN_ID/"

# === Analyse automatique des erreurs communes ===
echo ""
echo "🔍 Analyse des erreurs communes..."

if grep -r "npm ERR!" "logs-$RUN_ID/" 2>/dev/null; then
    echo "⚠️  Erreurs npm détectées"
fi

if grep -r "Error: ENOSPC" "logs-$RUN_ID/" 2>/dev/null; then
    echo "⚠️  Erreur d'espace disque détectée"
fi

if grep -r "timeout" "logs-$RUN_ID/" 2>/dev/null; then
    echo "⚠️  Timeouts détectés"
fi

# === Créer un rapport de debug ===
DEBUG_REPORT="debug-report-$RUN_ID.md"

cat > "$DEBUG_REPORT" << EOF
# Debug Report - Pipeline $RUN_ID

**Repository:** $REPO
**Run ID:** $RUN_ID
**Generated:** $(date)

## Pipeline Information

\`\`\`
$(gh run view "$RUN_ID" --repo "$REPO")
\`\`\`

## Common Errors Found

$(grep -r "ERROR\|Error\|error" "logs-$RUN_ID/" 2>/dev/null | head -20 || echo "No obvious errors found")

## Recommendations

- Check the full logs in the logs-$RUN_ID/ directory
- Verify environment variables and secrets
- Check for dependency version conflicts
- Ensure sufficient resources (disk space, memory)

## Next Steps

1. Review the logs above
2. Check recent changes that might have caused the failure
3. Re-run the pipeline if it was a transient error
4. Update dependencies or configuration if needed
EOF

echo "📄 Rapport de debug généré: $DEBUG_REPORT"
```

## Bonnes pratiques pour les pipelines

### 1. Gestion des erreurs robuste

```bash
#!/bin/bash
# Pipeline avec gestion d'erreurs

set -euo pipefail  # Arrêt en cas d'erreur, variable non définie, ou erreur dans un pipe

# Fonction de nettoyage en cas d'interruption
cleanup() {
    echo "🧹 Nettoyage en cours..."

    # Supprimer les fichiers temporaires
    rm -rf /tmp/pipeline-*

    # Notifier l'échec sur GitHub
    if [ -n "${PR_NUMBER:-}" ]; then
        gh pr comment "$PR_NUMBER" \
            --body "❌ **Pipeline interrompu**

        Le pipeline a été interrompu de manière inattendue.

        **Build ID:** ${BUILD_ID:-unknown}
        **Timestamp:** $(date)

        Veuillez relancer le pipeline." || true
    fi

    exit 1
}

# Capturer les signaux d'interruption
trap cleanup INT TERM

# Fonction de logging avec GitHub CLI
log_to_github() {
    local level="$1"
    local message="$2"
    local pr_number="${3:-}"

    echo "[$level] $message"

    # Commenter la PR si spécifiée
    if [ -n "$pr_number" ]; then
        case "$level" in
            "ERROR")
                gh pr comment "$pr_number" --body "❌ **Error:** $message" || true
                ;;
            "SUCCESS")
                gh pr comment "$pr_number" --body "✅ **Success:** $message" || true
                ;;
            "INFO")
                gh pr comment "$pr_number" --body "ℹ️ **Info:** $message" || true
                ;;
        esac
    fi
}

# Exemple d'utilisation
run_tests() {
    log_to_github "INFO" "Lancement des tests..." "$PR_NUMBER"

    if npm test; then
        log_to_github "SUCCESS" "Tous les tests sont passés" "$PR_NUMBER"
        return 0
    else
        log_to_github "ERROR" "Des tests ont échoué" "$PR_NUMBER"
        return 1
    fi
}
```

### 2. Configuration sécurisée

```bash
#!/bin/bash
# Configuration sécurisée pour pipelines

# === Validation des variables sensibles ===
validate_secrets() {
    local missing_secrets=()

    # Liste des secrets requis
    local required_secrets=("GITHUB_TOKEN" "DEPLOY_KEY" "API_SECRET")

    for secret in "${required_secrets[@]}"; do
        if [ -z "${!secret:-}" ]; then
            missing_secrets+=("$secret")
        fi
    done

    if [ ${#missing_secrets[@]} -gt 0 ]; then
        echo "❌ Secrets manquants: ${missing_secrets[*]}"

        # Créer une issue sécurisée (sans exposer les noms des secrets)
        gh issue create \
            --title "🔒 Pipeline configuration issue" \
            --body "Some required configuration is missing for the pipeline.

**Pipeline ID:** ${PIPELINE_ID:-unknown}
**Environment:** ${ENVIRONMENT:-unknown}

Please check the pipeline configuration and secrets." \
            --label "configuration,security" \
            --assignee "@devops-team"

        return 1
    fi

    echo "✅ Tous les secrets requis sont présents"
    return 0
}

# === Masquage des données sensibles ===
safe_log() {
    local message="$1"

    # Masquer les tokens et clés dans les logs
    local safe_message=$(echo "$message" | \
        sed 's/ghp_[a-zA-Z0-9_]*/***GITHUB_TOKEN***/g' | \
        sed 's/ssh-rsa [A-Za-z0-9+\/=]*/***SSH_KEY***/g' | \
        sed 's/Bearer [A-Za-z0-9_-]*/Bearer ***TOKEN***/g')

    echo "$safe_message"
}

# === Environnement isolé ===
setup_isolated_environment() {
    # Créer un environnement temporaire isolé
    PIPELINE_WORKSPACE="/tmp/pipeline-$(date +%s)-$"
    mkdir -p "$PIPELINE_WORKSPACE"
    cd "$PIPELINE_WORKSPACE"

    # Nettoyer les variables d'environnement potentiellement dangereuses
    unset SSH_AUTH_SOCK
    unset SSH_AGENT_PID

    echo "🔒 Environnement isolé créé: $PIPELINE_WORKSPACE"
}
```

### 3. Optimisation des performances

```bash
#!/bin/bash
# Pipeline optimisé pour les performances

# === Cache intelligent ===
setup_cache() {
    local cache_key="$1"
    local cache_paths="$2"

    echo "📦 Configuration du cache: $cache_key"

    # Vérifier si le cache existe sur GitHub
    if gh api "/repos/$GITHUB_REPOSITORY/actions/caches" \
        --jq ".actions_caches[] | select(.key == \"$cache_key\")" | grep -q key; then

        echo "✅ Cache trouvé pour $cache_key"

        # Télécharger le cache (simulation)
        echo "⬇️  Restauration du cache..."
        # Dans un vrai pipeline, ici on restaurerait le cache

    else
        echo "ℹ️  Aucun cache trouvé pour $cache_key"
    fi
}

# === Parallélisation des tâches ===
run_parallel_tests() {
    echo "🔄 Lancement des tests en parallèle..."

    # Lancer les tests en arrière-plan
    {
        echo "🧪 Tests unitaires..."
        npm run test:unit
        echo "✅ Tests unitaires terminés"
    } &
    UNIT_TEST_PID=$!

    {
        echo "🔍 Tests d'intégration..."
        npm run test:integration
        echo "✅ Tests d'intégration terminés"
    } &
    INTEGRATION_TEST_PID=$!

    {
        echo "🎨 Linting..."
        npm run lint
        echo "✅ Linting terminé"
    } &
    LINT_PID=$!

    # Attendre que tous les tests se terminent
    local exit_code=0

    if ! wait $UNIT_TEST_PID; then
        echo "❌ Tests unitaires échoués"
        exit_code=1
    fi

    if ! wait $INTEGRATION_TEST_PID; then
        echo "❌ Tests d'intégration échoués"
        exit_code=1
    fi

    if ! wait $LINT_PID; then
        echo "❌ Linting échoué"
        exit_code=1
    fi

    if [ $exit_code -eq 0 ]; then
        echo "✅ Tous les tests parallèles réussis"
    else
        echo "❌ Certains tests parallèles ont échoué"
    fi

    return $exit_code
}

# === Déploiement conditionnel ===
conditional_deploy() {
    local branch="$1"
    local environment="$2"

    echo "🚀 Déploiement conditionnel pour $branch → $environment"

    case "$branch" in
        "main")
            if [ "$environment" = "production" ]; then
                echo "🔄 Déploiement en production..."
                deploy_to_production
            else
                echo "⚠️  Branche main mais environnement non-production"
            fi
            ;;
        "develop")
            if [ "$environment" = "staging" ]; then
                echo "🔄 Déploiement en staging..."
                deploy_to_staging
            fi
            ;;
        "feature/"*)
            echo "🔄 Déploiement en environnement de review..."
            deploy_to_review_environment "$branch"
            ;;
        *)
            echo "ℹ️  Aucun déploiement pour la branche: $branch"
            ;;
    esac
}
```

### 4. Monitoring et métriques

```bash
#!/bin/bash
# Pipeline avec monitoring intégré

# === Métriques de performance ===
track_performance() {
    local stage="$1"
    local start_time="$2"
    local end_time="${3:-$(date +%s)}"

    local duration=$((end_time - start_time))

    echo "📊 Performance - $stage: ${duration}s"

    # Envoyer les métriques à GitHub (via issue ou comment)
    if [ "$duration" -gt 300 ]; then  # Plus de 5 minutes
        gh issue create \
            --title "⚠️ Slow pipeline stage detected: $stage" \
            --body "Pipeline stage '$stage' took $duration seconds.

**Stage:** $stage
**Duration:** ${duration}s ($(($duration / 60))m $(($duration % 60))s)
**Threshold:** 300s (5m)
**Pipeline:** ${PIPELINE_ID:-unknown}

This might indicate performance issues that need optimization." \
            --label "performance,monitoring" \
            --assignee "@devops-team"
    fi
}

# === Health check complet ===
comprehensive_health_check() {
    echo "🏥 Health check complet du système"

    local health_report=""
    local overall_status="healthy"

    # Check 1: État des services
    echo "🔍 Vérification des services..."
    if ! curl -sf "$API_HEALTH_ENDPOINT" > /dev/null; then
        health_report+="❌ API endpoint unreachable\n"
        overall_status="unhealthy"
    else
        health_report+="✅ API endpoint responding\n"
    fi

    # Check 2: Base de données
    echo "🔍 Vérification de la base de données..."
    if ! timeout 10 nc -z "$DB_HOST" "$DB_PORT"; then
        health_report+="❌ Database connection failed\n"
        overall_status="unhealthy"
    else
        health_report+="✅ Database connection successful\n"
    fi

    # Check 3: Espace disque
    echo "🔍 Vérification de l'espace disque..."
    DISK_USAGE=$(df / | awk 'NR==2 {print $5}' | sed 's/%//')
    if [ "$DISK_USAGE" -gt 85 ]; then
        health_report+="❌ Disk usage high: ${DISK_USAGE}%\n"
        overall_status="unhealthy"
    else
        health_report+="✅ Disk usage normal: ${DISK_USAGE}%\n"
    fi

    # Rapport final
    if [ "$overall_status" = "unhealthy" ]; then
        gh issue create \
            --title "🚨 System health check failed" \
            --body "System health check revealed issues:

$health_report

**Timestamp:** $(date)
**Environment:** ${ENVIRONMENT:-unknown}

Immediate attention required!" \
            --label "urgent,health-check" \
            --assignee "@sre-team"
    else
        echo "✅ Système en bonne santé"

        # Optionnel: commenter une issue existante
        EXISTING_HEALTH_ISSUE=$(gh issue list --label "health-check" --state open --limit 1 --json number --jq '.[0].number' 2>/dev/null || echo "")

        if [ -n "$EXISTING_HEALTH_ISSUE" ]; then
            gh issue comment "$EXISTING_HEALTH_ISSUE" \
                --body "✅ **Health check passed** - $(date)

$health_report

System is operating normally."
        fi
    fi
}
```

## Templates de pipelines prêts à l'emploi

### Template basique pour projets web

```yaml
# .github/workflows/web-app-pipeline.yml
name: Web Application Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

env:
  NODE_VERSION: '18'
  CACHE_VERSION: v1

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v3

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: ${{ env.NODE_VERSION }}
        cache: 'npm'

    - name: Install dependencies
      run: npm ci

    - name: Run tests
      run: |
        npm run test:coverage
        npm run lint

    - name: Upload coverage
      uses: codecov/codecov-action@v3

    - name: Comment PR with test results
      if: github.event_name == 'pull_request'
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: |
        COVERAGE=$(npm run test:coverage:summary 2>/dev/null | grep -E "Lines.*%" | tail -1 || echo "Coverage info not available")

        gh pr comment ${{ github.event.number }} \
          --body "## 🧪 Test Results

        ✅ All tests passed!

        **Coverage:** $COVERAGE
        **Node Version:** ${{ env.NODE_VERSION }}
        **Commit:** ${{ github.sha }}

        Ready for review! 🚀"

  build:
    needs: test
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v3

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: ${{ env.NODE_VERSION }}
        cache: 'npm'

    - name: Install dependencies
      run: npm ci

    - name: Build application
      run: npm run build

    - name: Upload build artifacts
      uses: actions/upload-artifact@v3
      with:
        name: build-files
        path: dist/
        retention-days: 7

  deploy:
    needs: [test, build]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment: production

    steps:
    - name: Download build artifacts
      uses: actions/download-artifact@v3
      with:
        name: build-files
        path: dist/

    - name: Deploy to production
      run: |
        echo "🚀 Deploying to production..."
        # Votre script de déploiement ici
        ./scripts/deploy.sh production

    - name: Create deployment issue
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: |
        gh issue create \
          --title "🚀 Production deployment - $(date +%Y-%m-%d)" \
          --body "Production deployment completed successfully.

        **Commit:** ${{ github.sha }}
        **Author:** ${{ github.actor }}
        **Workflow:** ${{ github.workflow }}
        **Environment:** production
        **Timestamp:** $(date)

        All systems operational! ✅" \
          --label "deployment,production" \
          --assignee "${{ github.actor }}"
```

### Template pour bibliothèques/packages

```yaml
# .github/workflows/library-pipeline.yml
name: Library CI/CD Pipeline

on:
  push:
    branches: [ main ]
    tags: [ 'v*' ]
  pull_request:
    branches: [ main ]

jobs:
  test-matrix:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [16, 18, 20]

    steps:
    - name: Checkout
      uses: actions/checkout@v3

    - name: Setup Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}

    - name: Install and test
      run: |
        npm ci
        npm test

    - name: Report matrix results
      if: always()
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: |
        if [ ${{ job.status }} == 'success' ]; then
          STATUS_EMOJI="✅"
          STATUS_TEXT="passed"
        else
          STATUS_EMOJI="❌"
          STATUS_TEXT="failed"
        fi

        if [ "${{ github.event_name }}" == "pull_request" ]; then
          gh pr comment ${{ github.event.number }} \
            --body "$STATUS_EMOJI Node.js ${{ matrix.node-version }}: Tests $STATUS_TEXT"
        fi

  publish:
    needs: test-matrix
    runs-on: ubuntu-latest
    if: github.ref_type == 'tag'

    steps:
    - name: Checkout
      uses: actions/checkout@v3

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        registry-url: 'https://registry.npmjs.org'

    - name: Install dependencies
      run: npm ci

    - name: Build package
      run: npm run build

    - name: Publish to npm
      run: npm publish
      env:
        NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}

    - name: Create GitHub Release
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: |
        TAG_NAME=${GITHUB_REF#refs/tags/}
        PACKAGE_VERSION=$(node -p "require('./package.json').version")

        # Générer les notes de release
        CHANGELOG=$(git log $(git describe --tags --abbrev=0 HEAD^)..HEAD --pretty=format:"- %s (%h)")

        gh release create "$TAG_NAME" \
          --title "Release $TAG_NAME" \
          --notes "## What's Changed

$CHANGELOG

## Installation

\`\`\`bash
npm install $(node -p "require('./package.json').name")@$PACKAGE_VERSION
\`\`\`

## Documentation

See [README](./README.md) for usage instructions." \
          --latest

    - name: Notify community
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: |
        TAG_NAME=${GITHUB_REF#refs/tags/}
        PACKAGE_NAME=$(node -p "require('./package.json').name")

        gh issue create \
          --title "📦 $PACKAGE_NAME $TAG_NAME released!" \
          --body "🎉 A new version of $PACKAGE_NAME has been released!

**Version:** $TAG_NAME
**NPM:** https://www.npmjs.com/package/$PACKAGE_NAME
**Release Notes:** https://github.com/${{ github.repository }}/releases/tag/$TAG_NAME

Thank you to all contributors! 🙏" \
          --label "release,announcement"
```

## Résumé des bonnes pratiques

**🏗️ Architecture de pipeline :**
- Séparez clairement les étapes (test, build, deploy)
- Utilisez des jobs parallèles quand possible
- Implémentez des caches intelligents pour les performances

**🔒 Sécurité :**
- Validez toujours les secrets avant utilisation
- Masquez les données sensibles dans les logs
- Utilisez des environnements isolés

**📊 Monitoring :**
- Trackez les métriques de performance
- Implémentez des health checks automatiques
- Créez des alertes pour les anomalies

**🔄 GitHub CLI Integration :**
- Commentez les PRs avec les résultats
- Créez des issues pour les échecs et alertes
- Utilisez les releases pour les déploiements

**🛠️ Maintenance :**
- Documentez vos pipelines
- Versionnez vos templates
- Monitorer régulièrement les performances

L'intégration de GitHub CLI dans vos pipelines transforme la CI/CD en un système intelligent qui communique automatiquement avec votre équipe et documente chaque étape du processus !

⏭️
