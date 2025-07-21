🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 11.4 Configuration avancée

## Introduction

La configuration avancée de la CLI GitHub vous permet d'optimiser votre expérience utilisateur, d'automatiser des tâches répétitives et d'adapter l'outil à votre environnement de travail spécifique. Cette section explore toutes les possibilités de personnalisation pour tirer le maximum de `gh`.

## Système de configuration de GitHub CLI

### Structure de configuration

La CLI GitHub stocke sa configuration dans des fichiers dédiés :

```bash
# Voir l'emplacement des fichiers de configuration
gh config list

# Structure typique (selon l'OS)
# Linux/macOS: ~/.config/gh/
# Windows: %AppData%\GitHub CLI\
```

**Fichiers de configuration principaux :**
- `config.yml` : Configuration principale
- `hosts.yml` : Configuration des hôtes GitHub
- `state.yml` : État des authentifications

### Commandes de configuration de base

```bash
# Lister toutes les configurations
gh config list

# Voir une configuration spécifique
gh config get editor

# Définir une configuration
gh config set editor code

# Supprimer une configuration
gh config unset editor
```

## Configuration de l'éditeur

### Éditeurs supportés

La CLI GitHub peut utiliser différents éditeurs pour les tâches interactives :

```bash
# Éditeurs populaires
gh config set editor code          # Visual Studio Code
gh config set editor vim           # Vim
gh config set editor nano          # Nano
gh config set editor emacs         # Emacs
gh config set editor subl          # Sublime Text
gh config set editor atom          # Atom
```

### Configuration avec options

```bash
# VS Code avec options spécifiques
gh config set editor "code --wait"

# Vim avec options
gh config set editor "vim +startinsert"

# Nano avec numéros de ligne
gh config set editor "nano -l"
```

### Éditeurs pour différentes tâches

Vous pouvez définir des éditeurs spécifiques selon le contexte :

```bash
# Éditeur par défaut
gh config set editor code

# Variables d'environnement alternatives
export GH_EDITOR=vim           # Priorité sur la config
export EDITOR=nano             # Fallback système
```

## Configuration du protocole Git

### Choix entre HTTPS et SSH

```bash
# Utiliser HTTPS (par défaut)
gh config set git_protocol https

# Utiliser SSH (recommandé pour l'usage fréquent)
gh config set git_protocol ssh

# Vérifier la configuration actuelle
gh config get git_protocol
```

**Avantages de chaque protocole :**

**HTTPS :**
- ✅ Plus simple à configurer
- ✅ Fonctionne derrière les firewalls
- ❌ Demande souvent les identifiants

**SSH :**
- ✅ Authentification par clés (plus sécurisé)
- ✅ Pas de saisie d'identifiants répétée
- ❌ Configuration initiale plus complexe

### Configuration SSH avancée

Si vous utilisez SSH, optimisez votre configuration :

```bash
# Dans ~/.ssh/config
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_github
    IdentitiesOnly yes
    AddKeysToAgent yes
```

## Gestion de la pagination

### Configuration du pager

```bash
# Désactiver la pagination (utile pour les scripts)
gh config set pager ""

# Utiliser less avec options spécifiques
gh config set pager "less -R"

# Utiliser cat (affichage direct)
gh config set pager cat

# Utiliser bat (coloration syntaxique)
gh config set pager "bat --style=plain"
```

### Variables d'environnement pour la pagination

```bash
# Dans votre .bashrc ou .zshrc
export GH_PAGER=cat                    # Pas de pagination
export GH_PAGER="less -R"             # Less avec couleurs
export PAGER="bat --style=plain"      # Pager système avec bat
```

## Configuration des prompts et interactions

### Désactiver les prompts interactifs

Pour l'automatisation, vous pouvez désactiver les confirmations :

```bash
# Désactiver tous les prompts
gh config set prompt disabled

# Réactiver les prompts
gh config set prompt enabled
```

### Comportement par défaut des commandes

```bash
# Toujours créer des PRs en mode draft
gh config set pr_create_draft true

# Toujours ouvrir le navigateur après création
gh config set browser open

# Navigateur par défaut
gh config set browser firefox
```

## Alias avancés

### Création d'alias complexes

Les alias permettent de créer des raccourcis pour des commandes complexes :

```bash
# Alias simples
gh alias set co "pr checkout"
gh alias set pv "pr view"

# Alias avec options par défaut
gh alias set issues-me "issue list --assignee @me --state open"
gh alias set prs-review "pr list --search 'is:open review-requested:@me'"

# Alias avec formatage personnalisé
gh alias set repos "repo list --json name,description,stargazerCount --template '{{range .}}📁 {{.name}} (⭐{{.stargazerCount}}){{if .description}} - {{.description}}{{end}}{{\"\\n\"}}{{end}}'"
```

### Alias avec scripts

Vous pouvez créer des alias qui exécutent des scripts plus complexes :

```bash
# Alias qui exécute un script shell
gh alias set cleanup '!sh -c "git branch --merged | grep -v main | xargs -n 1 git branch -d"'

# Alias avec paramètres
gh alias set search-issues '!sh -c "gh issue list --search \"$1 in:title\" --json number,title | jq -r \".[] | \\\"#\\(.number): \\(.title)\\\"\""'
```

### Alias pour les workflows courants

```bash
# Workflow complet de création de PR
gh alias set quick-pr '!sh -c "
    branch_name=\"feature/$(date +%Y%m%d)-$1\"
    git checkout -b \"$branch_name\"
    echo \"Branche $branch_name créée. Faites vos modifications puis:\"
    echo \"git add . && git commit -m \\\"$2\\\" && git push origin $branch_name && gh pr create\"
"'

# Usage: gh quick-pr "nouvelle-feature" "Ajout de la nouvelle feature"
```

## Configuration multi-comptes

### Gestion de plusieurs comptes GitHub

```bash
# Se connecter avec un compte spécifique
gh auth login --hostname github.com --username compte1

# Basculer entre les comptes
gh auth switch --hostname github.com --user compte1
gh auth switch --hostname github.com --user compte2

# Voir les comptes configurés
gh auth status
```

### Configuration par projet

Vous pouvez avoir des configurations différentes selon les projets :

```bash
# Dans un projet spécifique
cd /path/to/project

# Configuration locale (non globale)
git config user.name "Nom pour ce projet"
git config user.email "email@example.com"

# Utiliser un token spécifique pour ce projet
export GITHUB_TOKEN=token_specifique_projet
```

## Variables d'environnement avancées

### Variables importantes

```bash
# Token d'authentification
export GITHUB_TOKEN=your_token_here

# Hôte GitHub (pour GitHub Enterprise)
export GH_HOST=github.enterprise.com

# Désactiver les couleurs
export NO_COLOR=1

# Forcer les couleurs même dans les pipes
export FORCE_COLOR=1

# Timeout pour les requêtes API
export GH_REQUEST_TIMEOUT=30s

# Debug mode
export GH_DEBUG=1
```

### Configuration dans les fichiers de profil

Ajoutez ces configurations dans votre `~/.bashrc`, `~/.zshrc`, ou `~/.profile` :

```bash
# Configuration GitHub CLI
export GITHUB_TOKEN=$(cat ~/.config/gh/token 2>/dev/null)
export GH_PAGER=cat
export GH_EDITOR=code

# Alias utiles
alias gh-repos='gh repo list --limit 100'
alias gh-issues='gh issue list --assignee @me'
alias gh-prs='gh pr list --author @me'

# Fonctions utiles
gh-clone() {
    gh repo clone "$1"
    cd "$(basename "$1")"
}

gh-new() {
    gh repo create "$1" --private --clone
    cd "$1"
}
```

## Optimisation des performances

### Cache et stockage

```bash
# Nettoyer le cache si nécessaire
rm -rf ~/.cache/gh/

# Configuration du cache (fichier config.yml)
# Généralement géré automatiquement
```

### Optimisation des requêtes

```bash
# Limiter les résultats pour les commandes lentes
gh repo list --limit 50
gh issue list --limit 100

# Utiliser des filtres pour réduire les données
gh pr list --state open --author @me
gh issue list --assignee @me --label bug
```

### Configuration pour les scripts

Pour optimiser l'utilisation dans les scripts :

```bash
# Désactiver l'interactivité
export GH_PROMPT_DISABLED=1

# Format JSON par défaut
export GH_FORMAT=json

# Pas de couleurs
export NO_COLOR=1

# Timeout court
export GH_REQUEST_TIMEOUT=10s
```

## Configuration pour différents environnements

### Environnement de développement

```bash
# ~/.config/gh/config.yml pour le développement
git_protocol: ssh
editor: code --wait
prompt: enabled
pager: less -R
browser: open

# Alias orientés développement
gh alias set dev-status '!gh pr status && gh issue list --assignee @me'
gh alias set quick-commit '!git add . && git commit -m'
```

### Environnement de production/CI

```bash
# Configuration pour CI/CD
export GH_PROMPT_DISABLED=1
export NO_COLOR=1
export GH_PAGER=cat

# Token depuis les secrets
export GITHUB_TOKEN=${{ secrets.GITHUB_TOKEN }}

# Timeout plus long pour les opérations de production
export GH_REQUEST_TIMEOUT=60s
```

## Sécurité et bonnes pratiques

### Gestion sécurisée des tokens

```bash
# Stockage sécurisé du token
echo "your_token" | gh auth login --with-token

# Éviter les tokens en dur dans les scripts
if [ -z "$GITHUB_TOKEN" ]; then
    echo "❌ GITHUB_TOKEN non défini"
    exit 1
fi

# Utiliser des tokens avec permissions minimales
# (scope limité selon les besoins)
```

### Rotation des tokens

```bash
# Vérifier l'expiration des tokens
gh auth status

# Renouveler l'authentification
gh auth refresh

# Se reconnecter avec un nouveau token
gh auth logout
gh auth login
```

### Audit de configuration

```bash
# Vérifier la configuration actuelle
gh config list

# Audit des permissions
gh api user --jq '.permissions'

# Vérifier les alias définis
gh alias list
```

## Debugging et diagnostics

### Mode debug

```bash
# Activer le mode debug
export GH_DEBUG=1
gh repo list

# Debug pour une commande spécifique
GH_DEBUG=1 gh pr create
```

### Logs et diagnostics

```bash
# Voir les logs détaillés
gh --debug repo list

# Vérifier la connectivité
gh api rate_limit

# Tester l'authentification
gh auth status --show-token
```

### Résolution de problèmes courants

```bash
# Problèmes d'authentification
gh auth logout
gh auth login --web

# Problèmes de cache
rm -rf ~/.cache/gh/

# Réinitialisation complète
rm -rf ~/.config/gh/
gh auth login
```

## Configuration d'équipe

### Standardisation d'équipe

Créez un fichier de configuration partagé :

```bash
# team-gh-config.sh
#!/bin/bash

echo "Configuration GitHub CLI pour l'équipe..."

# Configuration standard
gh config set git_protocol ssh
gh config set editor code
gh config set pager "less -R"

# Alias d'équipe
gh alias set team-repos "repo list --org your-org"
gh alias set team-prs "pr list --search 'is:open org:your-org'"
gh alias set deploy "!./scripts/deploy.sh"

echo "✅ Configuration terminée"
```

### Templates et conventions

```bash
# Alias pour templates standardisés
gh alias set bug-report "issue create --template bug_report.md"
gh alias set feature-request "issue create --template feature_request.md"
gh alias set hotfix-pr "pr create --template hotfix.md --draft"
```

---

**🎯 Points clés à retenir :**
- La configuration se fait via `gh config set/get/list`
- Les alias permettent de créer des raccourcis puissants
- Les variables d'environnement offrent une flexibilité supplémentaire
- Adaptez la configuration selon votre environnement (dev, CI/CD)
- La sécurité des tokens est cruciale
- Le mode debug aide à résoudre les problèmes

**💡 Conseil final :** Commencez par les configurations de base (éditeur, protocole) puis ajoutez progressivement des alias et optimisations selon vos besoins quotidiens.

⏭️
