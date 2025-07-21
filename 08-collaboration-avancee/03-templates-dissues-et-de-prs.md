🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 8.3 Templates d'issues et de PRs

## Introduction

Les templates d'issues et de pull requests sont des outils puissants pour standardiser et améliorer la qualité des contributions dans vos projets. Ils guident les contributeurs en leur fournissant une structure claire et s'assurent que toutes les informations nécessaires sont collectées dès le départ.

Dans cette section, nous allons apprendre à créer, gérer et utiliser efficacement les templates avec la CLI GitHub, depuis les templates simples jusqu'aux configurations avancées avec formulaires interactifs.

## Comprendre les templates GitHub

### Qu'est-ce qu'un template ?

Un **template** est un modèle pré-rempli qui apparaît automatiquement quand quelqu'un crée une nouvelle issue ou pull request. Il contient :

- 📝 **Structure** : Sections et champs prédéfinis
- 🎯 **Guidage** : Instructions et exemples
- 🏷️ **Métadonnées** : Labels, assignés, projets automatiques
- ✅ **Checklists** : Étapes à suivre

### Types de templates

**Templates d'issues :**
- Bug reports
- Feature requests
- Questions/Support
- Tâches de maintenance

**Templates de pull requests :**
- Nouvelles fonctionnalités
- Corrections de bugs
- Documentation
- Refactoring

**Templates de configuration :**
- Formulaires interactifs
- Choix multiples
- Validation automatique

## Structure des templates

### Emplacement des fichiers

```
.github/
├── ISSUE_TEMPLATE/
│   ├── bug_report.md
│   ├── feature_request.md
│   └── config.yml
├── PULL_REQUEST_TEMPLATE/
│   └── pull_request_template.md
└── DISCUSSION_TEMPLATE/
    └── general.md
```

### Format de base

**Template d'issue simple :**
```markdown
---
name: Bug Report
about: Signaler un bug ou problème
title: '[BUG] '
labels: 'bug, needs-triage'
assignees: 'maintainer-team'
---

## Description du problème
Décrivez clairement le bug rencontré.

## Étapes pour reproduire
1. Aller à '...'
2. Cliquer sur '....'
3. Faire défiler jusqu'à '....'
4. Voir l'erreur

## Comportement attendu
Description claire de ce qui devrait se passer.

## Comportement actuel
Description de ce qui se passe actuellement.

## Environnement
- OS: [ex: Windows 10]
- Navigateur: [ex: Chrome 91]
- Version: [ex: 1.2.3]
```

## Création de templates avec la CLI

### Template d'issue basique

```bash
#!/bin/bash
# create-issue-template.sh - Création de template d'issue

TEMPLATE_DIR=".github/ISSUE_TEMPLATE"
mkdir -p "$TEMPLATE_DIR"

# Template de bug report
cat > "$TEMPLATE_DIR/bug_report.md" << 'EOF'
---
name: 🐛 Bug Report
about: Signaler un bug ou un comportement inattendu
title: '[BUG] '
labels: ['bug', 'needs-triage']
assignees: []
---

## 📋 Description du bug
<!-- Description claire et concise du problème -->

## 🔄 Étapes pour reproduire
1.
2.
3.
4.

## ✅ Comportement attendu
<!-- Décrivez ce qui devrait se passer -->

## ❌ Comportement actuel
<!-- Décrivez ce qui se passe actuellement -->

## 📸 Captures d'écran
<!-- Si applicable, ajoutez des captures d'écran -->

## 🖥️ Environnement
- **OS**:
- **Navigateur**:
- **Version de l'application**:
- **Version de Node.js** (si applicable):

## 📝 Informations supplémentaires
<!-- Toute autre information utile pour résoudre le problème -->

## ✅ Checklist
- [ ] J'ai vérifié qu'un issue similaire n'existe pas déjà
- [ ] J'ai testé avec la dernière version
- [ ] J'ai fourni toutes les informations demandées
EOF

echo "✅ Template bug_report.md créé"
```

### Template de feature request

```bash
# Template de demande de fonctionnalité
cat > "$TEMPLATE_DIR/feature_request.md" << 'EOF'
---
name: ✨ Feature Request
about: Proposer une nouvelle fonctionnalité
title: '[FEATURE] '
labels: ['enhancement', 'needs-discussion']
assignees: []
---

## 🎯 Résumé de la fonctionnalité
<!-- Description courte et claire de la fonctionnalité proposée -->

## 💡 Motivation et contexte
<!-- Pourquoi cette fonctionnalité est-elle nécessaire ? Quel problème résout-elle ? -->

## 📋 Description détaillée
<!-- Description complète de ce que vous aimeriez voir implémenté -->

## 🎨 Maquettes/Wireframes
<!-- Si applicable, ajoutez des maquettes ou schémas -->

## ✅ Critères d'acceptation
<!-- Liste des critères qui définiront que la fonctionnalité est terminée -->
- [ ]
- [ ]
- [ ]

## 🔄 Alternatives considérées
<!-- Décrivez les alternatives que vous avez envisagées -->

## 📊 Impact estimé
- **Utilisateurs affectés**:
- **Priorité**: [ ] Basse [ ] Moyenne [ ] Haute [ ] Critique
- **Effort de développement**: [ ] Petit [ ] Moyen [ ] Important

## 📝 Informations supplémentaires
<!-- Tout contexte ou information supplémentaire -->
EOF

echo "✅ Template feature_request.md créé"
```

### Template de question/support

```bash
# Template de support
cat > "$TEMPLATE_DIR/question.md" << 'EOF'
---
name: ❓ Question/Support
about: Poser une question ou demander de l'aide
title: '[QUESTION] '
labels: ['question', 'support']
assignees: []
---

## ❓ Question
<!-- Posez votre question de manière claire et concise -->

## 🎯 Contexte
<!-- Décrivez ce que vous essayez de faire -->

## 💻 Code/Configuration actuelle
```javascript
// Votre code ici
```

## ✅ Ce qui a été essayé
<!-- Décrivez les solutions que vous avez déjà tentées -->
- [ ]
- [ ]
- [ ]

## 📚 Documentation consultée
<!-- Liens vers la documentation que vous avez consultée -->
- [ ] README.md
- [ ] Documentation officielle
- [ ] Issues existantes
- [ ] Stack Overflow

## 📝 Informations supplémentaires
<!-- Toute autre information qui pourrait être utile -->
EOF

echo "✅ Template question.md créé"
```

## Templates de Pull Requests

### Template de PR standard

```bash
# Créer le dossier pour les templates de PR
mkdir -p ".github/PULL_REQUEST_TEMPLATE"

# Template principal de PR
cat > ".github/PULL_REQUEST_TEMPLATE/pull_request_template.md" << 'EOF'
## 📋 Description
<!-- Décrivez les changements apportés et pourquoi ils sont nécessaires -->

## 🔗 Issues liées
<!-- Utilisez des mots-clés comme "fixes #123" ou "closes #456" -->
Fixes #

## 🎯 Type de changement
<!-- Cochez les cases appropriées -->
- [ ] 🐛 Bug fix (changement non-breaking qui corrige un problème)
- [ ] ✨ Nouvelle fonctionnalité (changement non-breaking qui ajoute une fonctionnalité)
- [ ] 💥 Breaking change (fix ou fonctionnalité qui causerait un dysfonctionnement des fonctionnalités existantes)
- [ ] 📚 Documentation (changements de documentation uniquement)
- [ ] 🎨 Style (formatage, points-virgules manquants, etc. ; pas de changement de code)
- [ ] ♻️ Refactoring (changement de code qui ne corrige pas de bug ni n'ajoute de fonctionnalité)
- [ ] ⚡ Performance (changement de code qui améliore les performances)
- [ ] ✅ Tests (ajout de tests manquants ou correction de tests existants)
- [ ] 🔧 Chores (changements du processus de build ou outils auxiliaires)

## 🧪 Tests
<!-- Décrivez les tests que vous avez effectués -->
- [ ] Tests unitaires
- [ ] Tests d'intégration
- [ ] Tests manuels
- [ ] Tests de régression

### Détails des tests
<!-- Expliquez comment vous avez testé vos changements -->

## 📸 Captures d'écran/Démos
<!-- Si applicable, ajoutez des captures d'écran ou liens vers des démos -->

## ✅ Checklist
- [ ] Mon code suit les conventions de style de ce projet
- [ ] J'ai effectué une auto-review de mon code
- [ ] J'ai commenté mon code, particulièrement dans les zones difficiles à comprendre
- [ ] J'ai apporté les changements correspondants à la documentation
- [ ] Mes changements ne génèrent pas de nouveaux warnings
- [ ] J'ai ajouté des tests qui prouvent que mon correctif est efficace ou que ma fonctionnalité fonctionne
- [ ] Les tests unitaires nouveaux et existants passent localement avec mes changements
- [ ] Toutes les dépendances nécessaires ont été ajoutées/mises à jour

## 👥 Reviewers
<!-- Mentionnez les personnes ou équipes qui devraient review cette PR -->
@team/reviewers

## 📝 Notes pour les reviewers
<!-- Informations importantes pour les reviewers -->
EOF

echo "✅ Template de PR principal créé"
```

### Templates spécialisés de PR

```bash
# Template pour hotfix
cat > ".github/PULL_REQUEST_TEMPLATE/hotfix.md" << 'EOF'
## 🚨 HOTFIX - Action Urgente Requise

### 🔥 Problème critique
<!-- Décrivez le problème critique qui nécessite ce hotfix -->

### 📊 Impact
- **Sévérité**: [ ] Critique [ ] Majeure
- **Utilisateurs affectés**:
- **Services impactés**:

### 🔧 Solution
<!-- Décrivez la solution implémentée -->

### ✅ Tests d'urgence effectués
- [ ] Test de fonctionnement de base
- [ ] Test de non-régression critique
- [ ] Validation en environnement de staging

### 📋 Plan de rollback
<!-- Comment annuler ces changements si nécessaire -->

### 👥 Approbations requises
- [ ] @tech-lead
- [ ] @ops-team
- [ ] @product-owner

⚠️ **Cette PR nécessite une review et un merge immédiats**
EOF

# Template pour release
cat > ".github/PULL_REQUEST_TEMPLATE/release.md" << 'EOF'
## 🚀 Release v{VERSION}

### 📋 Résumé de la release
<!-- Description générale de cette version -->

### ✨ Nouvelles fonctionnalités
<!-- Liste des nouvelles fonctionnalités -->
-

### 🐛 Corrections de bugs
<!-- Liste des bugs corrigés -->
-

### 🔄 Changements
<!-- Autres changements importants -->
-

### 💥 Breaking Changes
<!-- Changements incompatibles (si applicable) -->
-

### 📚 Migration Guide
<!-- Instructions de migration pour les breaking changes -->

### ✅ Checklist Release
- [ ] Tous les tests passent
- [ ] Documentation mise à jour
- [ ] CHANGELOG.md mis à jour
- [ ] Version bumpée dans package.json
- [ ] Tests de migration effectués
- [ ] Équipe QA a validé
- [ ] Communication préparée

### 🎯 Plan de déploiement
1. Merge de cette PR
2. Création du tag de release
3. Déploiement en staging
4. Tests de validation
5. Déploiement en production
6. Communication aux utilisateurs
EOF

echo "✅ Templates spécialisés de PR créés"
```

## Configuration avancée avec formulaires

### Configuration des choix multiples

```bash
# Créer le fichier de configuration
cat > ".github/ISSUE_TEMPLATE/config.yml" << 'EOF'
blank_issues_enabled: false
contact_links:
  - name: 💬 Discussions Communauté
    url: https://github.com/myorg/myrepo/discussions
    about: Posez des questions et discutez avec la communauté
  - name: 📚 Documentation
    url: https://docs.myproject.com
    about: Consultez la documentation officielle
  - name: 🐦 Twitter
    url: https://twitter.com/myproject
    about: Suivez-nous pour les dernières nouvelles
EOF

echo "✅ Configuration des templates créée"
```

### Formulaire interactif d'issue

```bash
# Template avec formulaire YAML
cat > ".github/ISSUE_TEMPLATE/bug-form.yml" << 'EOF'
name: 🐛 Bug Report (Formulaire)
description: Signaler un bug avec un formulaire guidé
title: "[BUG] "
labels: ["bug", "needs-triage"]
assignees:
  - maintainer-username

body:
  - type: markdown
    attributes:
      value: |
        Merci de prendre le temps de signaler ce bug !
        Remplissez le formulaire ci-dessous avec le plus de détails possible.

  - type: textarea
    id: bug-description
    attributes:
      label: Description du bug
      description: Description claire et concise du problème
      placeholder: Décrivez le bug...
    validations:
      required: true

  - type: textarea
    id: reproduction-steps
    attributes:
      label: Étapes pour reproduire
      description: Étapes détaillées pour reproduire le comportement
      placeholder: |
        1. Aller à '...'
        2. Cliquer sur '...'
        3. Faire défiler jusqu'à '...'
        4. Voir l'erreur
    validations:
      required: true

  - type: textarea
    id: expected-behavior
    attributes:
      label: Comportement attendu
      description: Ce qui devrait se passer
      placeholder: Décrivez le comportement attendu...
    validations:
      required: true

  - type: textarea
    id: actual-behavior
    attributes:
      label: Comportement actuel
      description: Ce qui se passe actuellement
      placeholder: Décrivez le comportement actuel...
    validations:
      required: true

  - type: dropdown
    id: os
    attributes:
      label: Système d'exploitation
      description: Sur quel OS rencontrez-vous ce problème ?
      options:
        - Windows
        - macOS
        - Linux (Ubuntu)
        - Linux (Autres)
        - iOS
        - Android
    validations:
      required: true

  - type: dropdown
    id: browser
    attributes:
      label: Navigateur
      description: Quel navigateur utilisez-vous ?
      options:
        - Chrome
        - Firefox
        - Safari
        - Edge
        - Opera
        - Autre
    validations:
      required: false

  - type: input
    id: version
    attributes:
      label: Version de l'application
      description: Quelle version de l'application utilisez-vous ?
      placeholder: "ex: 1.2.3"
    validations:
      required: true

  - type: textarea
    id: additional-context
    attributes:
      label: Contexte additionnel
      description: Ajoutez tout autre contexte ou captures d'écran
      placeholder: Informations supplémentaires...
    validations:
      required: false

  - type: checkboxes
    id: terms
    attributes:
      label: Vérifications
      description: Confirmez que vous avez effectué ces vérifications
      options:
        - label: J'ai cherché dans les issues existantes
          required: true
        - label: J'ai testé avec la dernière version
          required: true
        - label: J'ai fourni toutes les informations demandées
          required: true
EOF

echo "✅ Formulaire interactif de bug créé"
```

## Gestion des templates avec scripts

### Script de déploiement de templates

```bash
#!/bin/bash
# deploy-templates.sh - Déploiement automatique des templates

REPO_PATH=$1
if [ -z "$REPO_PATH" ]; then
    echo "Usage: $0 <owner/repo>"
    exit 1
fi

echo "📝 Déploiement des templates pour $REPO_PATH"

# Créer la structure de dossiers
mkdir -p .github/{ISSUE_TEMPLATE,PULL_REQUEST_TEMPLATE,DISCUSSION_TEMPLATE}

# Templates d'issues
echo "🐛 Création des templates d'issues..."
./create-issue-template.sh

# Templates de PR
echo "🔄 Création des templates de PR..."
./create-pr-templates.sh

# Configuration
echo "⚙️  Création de la configuration..."
./create-template-config.sh

# Commit et push
echo "📤 Déploiement vers GitHub..."
git add .github/
git commit -m "feat: add issue and PR templates

- Add bug report template with guided form
- Add feature request template
- Add question/support template
- Add standard PR template
- Add specialized templates (hotfix, release)
- Configure template settings"

git push origin main

echo "✅ Templates déployés avec succès !"
echo "🔗 Vérifiez sur: https://github.com/$REPO_PATH/issues/new/choose"
```

### Script de validation des templates

```bash
#!/bin/bash
# validate-templates.sh - Validation des templates

echo "🔍 Validation des templates GitHub"

TEMPLATE_DIR=".github"
ERRORS=0

# Fonction de validation
validate_file() {
    local file=$1
    local type=$2

    if [ ! -f "$file" ]; then
        echo "❌ Fichier manquant: $file"
        ((ERRORS++))
        return
    fi

    # Vérifier la syntaxe YAML front-matter
    if head -n 20 "$file" | grep -q "^---$"; then
        echo "✅ Front-matter détecté: $file"

        # Extraire et valider le YAML
        sed -n '/^---$/,/^---$/p' "$file" | head -n -1 | tail -n +2 > temp_yaml

        if ! python3 -c "import yaml; yaml.safe_load(open('temp_yaml'))" 2>/dev/null; then
            echo "❌ YAML invalide dans: $file"
            ((ERRORS++))
        fi

        rm -f temp_yaml
    fi

    # Vérifications spécifiques par type
    case $type in
        "issue")
            if ! grep -q "## " "$file"; then
                echo "⚠️  Aucun header détecté dans: $file"
            fi
            ;;
        "pr")
            if ! grep -q "Checklist" "$file"; then
                echo "⚠️  Aucune checklist dans: $file"
            fi
            ;;
    esac
}

# Valider les templates d'issues
echo "📋 Validation des templates d'issues..."
for template in "$TEMPLATE_DIR"/ISSUE_TEMPLATE/*.md; do
    [ -f "$template" ] && validate_file "$template" "issue"
done

# Valider les templates de PR
echo "🔄 Validation des templates de PR..."
for template in "$TEMPLATE_DIR"/PULL_REQUEST_TEMPLATE/*.md; do
    [ -f "$template" ] && validate_file "$template" "pr"
done

# Valider la configuration
echo "⚙️  Validation de la configuration..."
if [ -f "$TEMPLATE_DIR/ISSUE_TEMPLATE/config.yml" ]; then
    validate_file "$TEMPLATE_DIR/ISSUE_TEMPLATE/config.yml" "config"
fi

# Résumé
echo ""
if [ $ERRORS -eq 0 ]; then
    echo "✅ Tous les templates sont valides !"
else
    echo "❌ $ERRORS erreur(s) détectée(s)"
    exit 1
fi
```

## Utilisation des templates avec la CLI

### Créer une issue avec template

```bash
# Lister les templates disponibles
gh api repos/OWNER/REPO --jq '.has_issues'

# Créer une issue en utilisant un template spécifique
gh issue create --template bug_report

# Créer une issue avec pré-remplissage
gh issue create \
  --title "[BUG] Problème de connexion" \
  --body-file bug-report-content.md \
  --label "bug,priority-high" \
  --assignee @me
```

### Créer une PR avec template

```bash
# Créer une PR avec le template par défaut
gh pr create

# Créer une PR avec template spécifique
gh pr create --template hotfix

# Pré-remplir une PR
gh pr create \
  --title "fix: correction authentification" \
  --body "$(cat .github/PULL_REQUEST_TEMPLATE/hotfix.md)" \
  --reviewer @team/backend
```

## Maintenance et évolution des templates

### Script de mise à jour

```bash
#!/bin/bash
# update-templates.sh - Mise à jour des templates

echo "🔄 Mise à jour des templates"

# Sauvegarder les templates actuels
BACKUP_DIR="templates-backup-$(date +%Y%m%d)"
cp -r .github "$BACKUP_DIR"
echo "💾 Sauvegarde créée: $BACKUP_DIR"

# Appliquer les nouvelles versions
./deploy-templates.sh

# Valider les nouveaux templates
if ./validate-templates.sh; then
    echo "✅ Mise à jour réussie"
    rm -rf "$BACKUP_DIR"
else
    echo "❌ Erreur détectée, restauration..."
    rm -rf .github
    mv "$BACKUP_DIR" .github
    echo "🔙 Templates restaurés"
    exit 1
fi
```

### Métriques d'utilisation

```bash
#!/bin/bash
# template-metrics.sh - Métriques d'utilisation des templates

REPO=$1
if [ -z "$REPO" ]; then
    echo "Usage: $0 <owner/repo>"
    exit 1
fi

echo "📊 Métriques des templates pour $REPO"

# Issues par template (approximation basée sur les labels)
echo "🐛 Bug reports:"
gh issue list --repo $REPO --label bug --state all --json number | jq '. | length'

echo "✨ Feature requests:"
gh issue list --repo $REPO --label enhancement --state all --json number | jq '. | length'

echo "❓ Questions/Support:"
gh issue list --repo $REPO --label question --state all --json number | jq '. | length'

# PR par type (approximation basée sur les titres)
echo "🔄 Pull Requests par type:"
echo "  Hotfix: $(gh pr list --repo $REPO --state all --search '[HOTFIX]' --json number | jq '. | length')"
echo "  Feature: $(gh pr list --repo $REPO --state all --search '[FEATURE]' --json number | jq '. | length')"
echo "  Bug fix: $(gh pr list --repo $REPO --state all --search '[BUG]' --json number | jq '. | length')"
```

## Points clés à retenir

✅ **Templates standardisent** la création d'issues et de PR pour une meilleure qualité

✅ **Formulaires YAML** offrent une expérience utilisateur guidée et interactive

✅ **Multiple templates** permettent de couvrir différents cas d'usage

✅ **Configuration centralisée** avec `config.yml` pour personnaliser l'expérience

✅ **Validation automatique** assure la qualité et la cohérence des templates

✅ **Métriques d'utilisation** aident à optimiser les templates dans le temps

✅ **Maintenance régulière** garde les templates pertinents et efficaces

## Prochaine étape

Maintenant que vous maîtrisez la création et gestion de templates, nous allons apprendre à intégrer GitHub avec vos projets et outils de gestion pour créer un écosystème de collaboration complet.


⏭️
