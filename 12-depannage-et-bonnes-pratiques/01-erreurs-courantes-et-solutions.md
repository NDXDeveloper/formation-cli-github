🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 12.1 Erreurs courantes et solutions

## Introduction

Cette section présente les erreurs les plus fréquemment rencontrées avec la CLI GitHub et leurs solutions éprouvées. Chaque problème est accompagné d'un diagnostic simple et de solutions étape par étape, accessibles même aux débutants.

## Erreurs d'authentification

### Erreur : "Authentication failed"

**Messages typiques :**
```
HTTP 401 Unauthorized
authentication failed
```

**Causes possibles :**
- Token d'accès expiré ou invalide
- Permissions insuffisantes
- Configuration d'authentification corrompue

**Solutions étape par étape :**

```bash
# 1. Vérifier le statut d'authentification
gh auth status

# 2. Si non authentifié, se reconnecter
gh auth login

# 3. Si déjà authentifié mais problème persiste
gh auth logout
gh auth login --web

# 4. Vérifier les permissions du token
gh api user
```

**Diagnostic approfondi :**
```bash
# Voir les détails de l'authentification
gh auth status --show-token

# Tester l'accès API direct
gh api rate_limit
```

### Erreur : "Bad credentials" ou "Invalid token"

**Messages typiques :**
```
HTTP 401 Bad credentials
Invalid personal access token
```

**Solution rapide :**
```bash
# Régénérer l'authentification
gh auth refresh

# Si cela ne fonctionne pas, réauthentification complète
gh auth logout
gh auth login
```

**Pour les tokens personnels :**
```bash
# Authentification avec un nouveau token
echo "your_new_token" | gh auth login --with-token
```

### Erreur : "Two-factor authentication required"

**Messages typiques :**
```
Must specify two-factor authentication OTP code
```

**Solutions :**
```bash
# Utiliser l'authentification web (recommandé)
gh auth login --web

# Ou configurer avec un token d'application
gh auth login --with-token
```

## Erreurs de permissions

### Erreur : "Forbidden" ou "Permission denied"

**Messages typiques :**
```
HTTP 403 Forbidden
You don't have permission to access this resource
```

**Diagnostic :**
```bash
# Vérifier les permissions actuelles
gh api user --jq '.permissions'

# Vérifier l'accès au repository
gh repo view OWNER/REPO
```

**Solutions selon le contexte :**

**Repository privé :**
```bash
# Vérifier que vous êtes collaborateur
gh api repos/OWNER/REPO/collaborators/USERNAME
```

**Organisation :**
```bash
# Vérifier votre statut dans l'organisation
gh api orgs/ORG/members/USERNAME
```

**Token avec permissions insuffisantes :**
```bash
# Recréer un token avec les bonnes permissions
gh auth login --scopes repo,admin:org
```

### Erreur : "Repository not found"

**Messages typiques :**
```
HTTP 404 Not Found
Repository not found
```

**Vérifications :**
```bash
# Vérifier l'orthographe exacte
gh repo view owner/repository-name

# Lister vos repositories accessibles
gh repo list owner

# Pour les repositories d'organisation
gh repo list org-name
```

## Erreurs de réseau et API

### Erreur : "Rate limit exceeded"

**Messages typiques :**
```
API rate limit exceeded
You have exceeded a secondary rate limit
```

**Solutions :**
```bash
# Vérifier les limites actuelles
gh api rate_limit

# Attendre la réinitialisation ou utiliser un token authentifié
# Les tokens authentifiés ont des limites plus hautes

# Optimiser les requêtes
gh repo list --limit 20  # Limiter les résultats
gh issue list --limit 50 # Réduire le nombre d'appels
```

**Prévention :**
```bash
# Configurer des timeouts
export GH_REQUEST_TIMEOUT=30s

# Utiliser le cache quand possible
gh repo list --json name > repos_cache.json
```

### Erreur : "Request timeout"

**Messages typiques :**
```
Request timeout
Connection timed out
```

**Solutions :**
```bash
# Augmenter le timeout
export GH_REQUEST_TIMEOUT=60s

# Vérifier la connectivité
ping github.com

# Tester avec curl
curl -I https://api.github.com
```

### Erreur : "Server error" (5xx)

**Messages typiques :**
```
HTTP 500 Internal Server Error
HTTP 502 Bad Gateway
HTTP 503 Service Unavailable
```

**Actions à prendre :**
```bash
# Vérifier le statut de GitHub
# Visiter https://www.githubstatus.com/

# Réessayer avec délai
sleep 30 && gh repo list

# Utiliser une commande plus simple
gh api rate_limit  # Test de base
```

## Erreurs de configuration

### Erreur : "Command not found"

**Messages typiques :**
```
gh: command not found
gh extension not found
```

**Pour la CLI principale :**
```bash
# Vérifier l'installation
which gh

# Réinstaller si nécessaire
# macOS
brew install gh

# Ubuntu/Debian
sudo apt update && sudo apt install gh
```

**Pour les extensions :**
```bash
# Lister les extensions installées
gh extension list

# Réinstaller l'extension
gh extension remove extension-name
gh extension install owner/extension-name
```

### Erreur : "Editor not found"

**Messages typiques :**
```
Editor command not found
Failed to open editor
```

**Solutions :**
```bash
# Vérifier la configuration actuelle
gh config get editor

# Configurer un éditeur disponible
gh config set editor nano    # Simple
gh config set editor vim     # Avancé
gh config set editor code    # VS Code

# Ou utiliser une variable d'environnement
export EDITOR=nano
```

### Erreur : "Git not found"

**Messages typiques :**
```
git: command not found
Git is required but not installed
```

**Solution :**
```bash
# Installer Git (selon votre système)
# macOS
brew install git

# Ubuntu/Debian
sudo apt install git

# Windows
# Télécharger depuis https://git-scm.com/
```

## Erreurs Git et synchronisation

### Erreur : "Not a git repository"

**Messages typiques :**
```
fatal: not a git repository
This command requires you to be in a Git repository
```

**Solutions :**
```bash
# Vérifier que vous êtes dans un repository
pwd
ls -la  # Chercher le dossier .git

# Initialiser un repository si nécessaire
git init

# Ou naviguer vers le bon dossier
cd /path/to/your/repository
```

### Erreur : "No remote 'origin'"

**Messages typiques :**
```
No such remote 'origin'
Remote origin does not exist
```

**Solutions :**
```bash
# Vérifier les remotes existants
git remote -v

# Ajouter l'origin manquant
git remote add origin https://github.com/owner/repo.git

# Ou avec SSH
git remote add origin git@github.com:owner/repo.git
```

### Erreur : "Branch not found"

**Messages typiques :**
```
Branch 'branch-name' not found
No such branch
```

**Diagnostic et solutions :**
```bash
# Lister toutes les branches
git branch -a

# Créer la branche si elle n'existe pas
git checkout -b branch-name

# Ou récupérer depuis le remote
git fetch origin
git checkout branch-name
```

## Erreurs d'extensions

### Erreur : "Extension failed to load"

**Messages typiques :**
```
Extension failed to load
Permission denied executing extension
```

**Solutions :**
```bash
# Vérifier les permissions
ls -la $(which gh-extension-name)

# Rendre exécutable si nécessaire
chmod +x $(which gh-extension-name)

# Réinstaller l'extension
gh extension remove extension-name
gh extension install owner/extension-name
```

### Erreur : "Extension not compatible"

**Messages typiques :**
```
Extension requires newer version
Incompatible extension version
```

**Solutions :**
```bash
# Mettre à jour la CLI
brew upgrade gh  # macOS
# Ou selon votre méthode d'installation

# Mettre à jour l'extension
gh extension upgrade extension-name

# Ou toutes les extensions
gh extension upgrade --all
```

## Erreurs de formats et données

### Erreur : "Invalid JSON output"

**Messages typiques :**
```
Invalid JSON
Parse error
```

**Diagnostic :**
```bash
# Tester sans formatage
gh repo list

# Tester le JSON
gh repo list --json name | jq '.'

# Vérifier l'installation de jq
which jq
```

### Erreur : "Template parse error"

**Messages typiques :**
```
Template parse error
Invalid template syntax
```

**Solutions :**
```bash
# Tester avec un template simple
gh repo list --template '{{range .}}{{.name}}{{end}}'

# Vérifier la syntaxe des templates complexes
# Isoler chaque partie du template
```

## Erreurs de performance

### Erreur : "Command hangs" ou est très lent

**Diagnostic :**
```bash
# Utiliser le mode debug
GH_DEBUG=1 gh repo list

# Limiter les résultats
gh repo list --limit 10

# Vérifier les filtres
gh issue list --limit 5
```

**Solutions :**
```bash
# Optimiser les requêtes
gh repo list --json name,description  # Moins de champs

# Nettoyer le cache
rm -rf ~/.cache/gh/

# Utiliser des timeouts
timeout 30s gh repo list
```

## Procédures de diagnostic général

### Check-list de diagnostic rapide

```bash
# 1. Vérifications de base
gh --version
gh auth status
gh config list

# 2. Test de connectivité
gh api rate_limit

# 3. Test avec commande simple
gh repo list --limit 1

# 4. Mode debug si nécessaire
GH_DEBUG=1 gh your-failing-command
```

### Reset complet en cas de problème majeur

```bash
# ⚠️ Cette procédure supprime toute la configuration
# Sauvegardez d'abord si nécessaire

# 1. Déconnexion
gh auth logout

# 2. Suppression de la configuration
rm -rf ~/.config/gh/

# 3. Suppression du cache
rm -rf ~/.cache/gh/

# 4. Réauthentification
gh auth login

# 5. Reconfiguration de base
gh config set editor your-editor
gh config set git_protocol https  # ou ssh
```

## Ressources pour aller plus loin

### Logs et debugging

```bash
# Activer les logs détaillés
export GH_DEBUG=1
export GH_LOG_LEVEL=debug

# Rediriger les erreurs vers un fichier
gh command 2> error.log

# Analyser les requêtes réseau
GH_DEBUG=1 gh command 2>&1 | grep "HTTP"
```

### Communauté et support

**Où chercher de l'aide :**
- GitHub CLI Issues : https://github.com/cli/cli/issues
- GitHub Community : https://github.community/
- Stack Overflow : tag `github-cli`
- Documentation officielle : https://cli.github.com/manual/

**Comment signaler un bug :**
```bash
# Collecter les informations de diagnostic
gh --version
gh auth status
gh config list

# Reproduire avec mode debug
GH_DEBUG=1 gh failing-command

# Inclure ces informations dans votre rapport
```

---

**🎯 Points clés à retenir :**
- La plupart des erreurs ont des solutions simples
- Commencez toujours par vérifier l'authentification
- Utilisez le mode debug pour diagnostiquer
- N'hésitez pas à réinitialiser en cas de problème majeur
- Documentez les solutions qui fonctionnent pour vous

**💡 Astuce :** Gardez une liste personnelle des erreurs rencontrées et de leurs solutions pour référence future.

**➡️ Prochaine étape :** Dans la section 12.2, nous verrons comment optimiser les performances de la CLI GitHub.

⏭️
