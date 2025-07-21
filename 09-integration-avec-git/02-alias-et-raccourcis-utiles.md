🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 9.2 Alias et raccourcis utiles

## Qu'est-ce qu'un alias ?

Un alias est un raccourci personnalisé qui remplace une commande longue par une version courte. C'est comme créer un surnom pour une commande compliquée !

**Exemple simple :**
```bash
# Au lieu de taper ça à chaque fois :
git checkout -b nouvelle-branche

# Vous pourrez taper juste ça :
gcb nouvelle-branche
```

## Pourquoi utiliser des alias ?

### Gains de productivité

- **⏱️ Gain de temps** : Taper `gst` au lieu de `git status`
- **🧠 Moins d'effort mental** : Mémoriser des raccourcis simples
- **✨ Moins d'erreurs** : Éviter les fautes de frappe dans les longues commandes
- **🔄 Workflows fluides** : Enchaîner rapidement les actions

### Types d'alias

Il existe trois types d'alias que nous allons couvrir :

1. **Alias Git** → Pour les commandes Git
2. **Alias Shell** → Pour combiner Git et GitHub CLI
3. **Alias GitHub CLI** → Pour les commandes GitHub CLI

## Alias Git essentiels

### Configuration des alias Git

Les alias Git se configurent avec `git config` :

```bash
# Syntaxe générale
git config --global alias.RACCOURCI "commande complète"
```

### Alias Git de base

```bash
# Status et information
git config --global alias.st "status"
git config --global alias.br "branch"
git config --global alias.co "checkout"
git config --global alias.ci "commit"

# Utilisation :
git st        # = git status
git br        # = git branch
git co main   # = git checkout main
git ci -m "message"  # = git commit -m "message"
```

### Alias Git avancés

```bash
# Commit rapide avec message
git config --global alias.cm "commit -m"
# Usage : git cm "Mon message de commit"

# Checkout et créer une branche
git config --global alias.cob "checkout -b"
# Usage : git cob nouvelle-feature

# Log formaté et lisible
git config --global alias.lg "log --oneline --decorate --graph"
# Usage : git lg

# Push de la branche courante
git config --global alias.pushcb "push origin HEAD"
# Usage : git pushcb

# Pull avec rebase pour éviter les merges inutiles
git config --global alias.up "pull --rebase"
# Usage : git up
```

### Alias Git pour l'intégration GitHub

```bash
# Commit et push en une fois
git config --global alias.cap "!git add -A && git commit -m"
# Usage : git cap "Mon message" (puis git push manuellement)

# Mise à jour complète de main
git config --global alias.sync "!git checkout main && git pull origin main"
# Usage : git sync
```

## Alias Shell (Bash/Zsh)

Les alias shell permettent de combiner Git et GitHub CLI en une seule commande.

### Configuration des alias shell

Ajoutez ces lignes dans votre fichier de configuration :
- **Linux/macOS** : `~/.bashrc` ou `~/.zshrc`
- **Windows Git Bash** : `~/.bashrc`

```bash
# Ouvrir le fichier de configuration
# Sur macOS/Linux :
nano ~/.bashrc    # ou ~/.zshrc si vous utilisez Zsh

# Sur Windows :
nano ~/.bashrc
```

### Alias shell essentiels

```bash
# Alias pour Git (plus courts que les alias Git natifs)
alias g="git"
alias ga="git add"
alias gc="git commit"
alias gp="git push"
alias gl="git pull"
alias gs="git status"
alias gb="git branch"

# Usage :
# gs        # = git status
# ga .      # = git add .
# gc -m "fix" # = git commit -m "fix"
```

### Alias combinant Git et GitHub CLI

```bash
# Workflow complet : add + commit + push + create PR
alias gpr='function _gpr() {
    git add . &&
    git commit -m "$1" &&
    git push origin HEAD &&
    gh pr create --title "$1" --body "";
}; _gpr'

# Usage : gpr "Ajout nouvelle fonctionnalité"

# Checkout d'une PR et test
alias prco="gh pr checkout"
# Usage : prco 23  # = gh pr checkout 23

# Lister les PRs avec format compact
alias prl="gh pr list --limit 10"
# Usage : prl

# Voir le statut complet (git + GitHub)
alias status='function _status() {
    echo "=== Git Status ===";
    git status --short;
    echo "";
    echo "=== PR Status ===";
    gh pr status;
}; _status'

# Usage : status
```

### Alias pour les tâches quotidiennes

```bash
# Synchronisation rapide avec main
alias sync="git checkout main && git pull origin main"

# Créer une branche depuis main à jour
alias newbr='function _newbr() {
    git checkout main &&
    git pull origin main &&
    git checkout -b "$1";
}; _newbr'
# Usage : newbr feature-login

# Nettoyage des branches locales supprimées sur GitHub
alias cleanup="git remote prune origin && git branch --merged | grep -v main | xargs -n 1 git branch -d"

# Revenir rapidement à main et nettoyer
alias done='function _done() {
    git checkout main &&
    git pull origin main &&
    git branch -d "$1";
}; _done'
# Usage : done feature-login
```

## Alias GitHub CLI

GitHub CLI permet aussi de créer ses propres alias avec `gh alias`.

### Configuration d'alias GitHub CLI

```bash
# Syntaxe générale
gh alias set RACCOURCI "commande gh complète"
```

### Alias GitHub CLI utiles

```bash
# Voir les PRs en format condensé
gh alias set prs "pr list --limit 20 --json number,title,author --template '{{range .}}#{{.number}} {{.title}} (@{{.author.login}}){{\"\\n\"}}{{end}}'"

# Créer une PR avec template par défaut
gh alias set prc "pr create --title '' --body 'Description:\\n\\n## Changements\\n- \\n\\n## Tests\\n- [ ] Tests ajoutés\\n- [ ] Tests passent'"

# Approuver rapidement une PR
gh alias set approve "pr review --approve"

# Merger avec suppression de branche
gh alias set done "pr merge --squash --delete-branch"

# Voir les issues assignées à moi
gh alias set myissues "issue list --assignee @me"
```

### Utilisation des alias GitHub CLI

```bash
# Après configuration, vous pouvez utiliser :
gh prs          # Liste condensée des PRs
gh prc          # Créer PR avec template
gh approve 15   # Approuver la PR #15
gh done 15      # Merger et supprimer la branche
gh myissues     # Mes issues assignées
```

## Workflow complet avec alias

Voici comment un workflow typique devient ultra-rapide avec les alias :

### Sans alias (version longue)

```bash
git checkout main
git pull origin main
git checkout -b feature-contact-form
# ... développement ...
git add .
git commit -m "Add contact form with validation"
git push origin feature-contact-form
gh pr create --title "Add contact form with validation" --body "New contact form with email validation and spam protection"
```

### Avec alias (version courte)

```bash
sync                    # = git checkout main && git pull origin main
newbr feature-contact-form  # = checkout main + pull + checkout -b
# ... développement ...
gpr "Add contact form with validation"  # = add + commit + push + create PR
```

**Résultat : 8 commandes → 3 commandes !**

## Gestion et organisation des alias

### Lister vos alias existants

```bash
# Alias Git
git config --get-regexp alias

# Alias shell
alias | grep git

# Alias GitHub CLI
gh alias list
```

### Supprimer des alias

```bash
# Supprimer un alias Git
git config --global --unset alias.nom_alias

# Supprimer un alias GitHub CLI
gh alias delete nom_alias

# Les alias shell : commentez la ligne dans ~/.bashrc ou ~/.zshrc
```

### Partager des alias en équipe

Créez un fichier `.git_aliases` dans votre projet :

```bash
# .git_aliases - Alias partagés de l'équipe
alias sync="git checkout main && git pull origin main"
alias newbr='function _newbr() { sync && git checkout -b "$1"; }; _newbr'
alias gpr='function _gpr() { git add . && git commit -m "$1" && git push origin HEAD && gh pr create --title "$1" --body ""; }; _gpr'
```

Puis chaque membre charge ces alias :
```bash
# Dans ~/.bashrc ou ~/.zshrc
source /chemin/vers/projet/.git_aliases
```

## Conseils pour bien utiliser les alias

### 1. Commencer petit

```bash
# ✅ Commencez par les alias simples
alias gs="git status"
alias ga="git add"

# ❌ Évitez les alias trop complexes au début
alias mega_workflow="git add . && git commit && git push && gh pr create && gh pr merge"
```

### 2. Noms intuitifs

```bash
# ✅ Noms logiques et mémorisables
alias gst="git status"      # Git STatus
alias prc="gh pr create"    # PR Create

# ❌ Noms cryptiques
alias x="git status"
alias zzz="gh pr create"
```

### 3. Documenter vos alias

```bash
# Ajoutez des commentaires dans vos fichiers de config
alias gs="git status"       # Statut Git rapide
alias sync="git checkout main && git pull origin main"  # Sync avec main
```

## Résumé des bénéfices

Avec des alias bien configurés, vous obtenez :

- **⚡ Vitesse** : Commandes 3-5x plus rapides à taper
- **🎯 Focus** : Moins de distraction, plus de concentration sur le code
- **🔄 Workflows fluides** : Enchaînement naturel des actions
- **🧠 Charge mentale réduite** : Moins à mémoriser, plus d'automatismes
- **👥 Cohérence d'équipe** : Workflows standardisés et partagés

L'investissement initial dans la configuration d'alias vous fera gagner des heures chaque semaine !

⏭️
