🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 2.4 - Configuration de l'éditeur par défaut

## 🎯 Introduction

L'éditeur par défaut est probablement la configuration la plus importante de votre CLI GitHub ! Pourquoi ? Parce que c'est l'outil qui s'ouvrira automatiquement chaque fois que vous devrez écrire ou modifier du texte : messages de commit, descriptions d'issues, corps de pull requests, etc.

**Analogie** : Si la CLI GitHub est votre atelier, l'éditeur est votre outil de précision favori - autant qu'il soit parfaitement adapté à vos mains !

## 📝 Pourquoi l'éditeur est-il si important ?

### Quand la CLI ouvre votre éditeur

Votre éditeur s'ouvrira automatiquement dans ces situations :

#### Messages de commit étendus
```bash
gh pr create
# → S'ouvre pour écrire le titre et la description de la PR
```

#### Descriptions d'issues
```bash
gh issue create
# → S'ouvre pour rédiger le contenu de l'issue
```

#### Édition de contenu existant
```bash
gh issue edit 42
# → S'ouvre pour modifier une issue existante
```

#### Messages de release
```bash
gh release create v1.0.0
# → S'ouvre pour rédiger les notes de version
```

### Impact sur votre productivité

**Avec un mauvais éditeur** :
- ⏰ Perte de temps à comprendre l'interface
- 😤 Frustration avec les raccourcis inconnus
- 🐛 Risque d'erreurs de manipulation
- 💔 Rupture du flow de travail

**Avec le bon éditeur** :
- ⚡ Rapidité et fluidité
- 😊 Confort d'utilisation familier
- ✅ Fonctionnalités avancées (coloration, autocomplete)
- 🔄 Intégration parfaite dans votre workflow

## 🔧 Configuration de base

### Voir l'éditeur actuel

```bash
gh config get editor
```

**Résultats possibles** :
- `nano` (éditeur par défaut sur beaucoup de systèmes)
- `vim` (éditeur par défaut sur certains systèmes)
- Rien (pas encore configuré)

### Configurer votre éditeur

```bash
gh config set editor "votre-editeur-ici"
```

**La syntaxe exacte dépend de votre éditeur** - nous verrons les détails pour chaque éditeur populaire !

## 🎨 Configuration par éditeur

### Visual Studio Code (Recommandé pour débuter)

#### Configuration standard
```bash
gh config set editor "code --wait"
```

**Explication** :
- `code` : Lance VS Code
- `--wait` : **Crucial** - La CLI attend que vous fermiez l'onglet avant de continuer

#### Configuration avancée
```bash
# Ouvrir dans une nouvelle fenêtre
gh config set editor "code --wait --new-window"

# Ajouter des dossiers au workspace
gh config set editor "code --wait --add"

# Réutiliser la fenêtre existante
gh config set editor "code --wait --reuse-window"
```

#### Vérifier que VS Code est accessible
```bash
# Tester la commande
code --version

# Si "command not found", installer VS Code ou ajouter au PATH
```

**Sur Windows** : Assurez-vous que VS Code est dans votre PATH ou utilisez le chemin complet.

### Vim/Neovim (Pour utilisateurs terminal)

#### Vim classique
```bash
gh config set editor vim
```

#### Neovim
```bash
gh config set editor nvim
```

#### Vim avec options
```bash
# Forcer le mode texte
gh config set editor "vim +startinsert"

# Avec configuration spécifique
gh config set editor "vim -u ~/.vimrc.github"
```

**Avantages Vim** :
- ✅ **Rapidité** : Démarre instantanément
- ✅ **Efficacité** : Raccourcis puissants
- ✅ **Disponibilité** : Installé partout
- ✅ **Légèreté** : Fonctionne même en SSH

### Nano (Simple et accessible)

#### Configuration de base
```bash
gh config set editor nano
```

#### Options utiles
```bash
# Avec numéros de ligne
gh config set editor "nano -l"

# Avec coloration syntaxique
gh config set editor "nano -Y markdown"

# Mode accessible (beaucoup d'aide)
gh config set editor "nano -T 4 -l"
```

**Avantages Nano** :
- ✅ **Simplicité** : Interface intuitive
- ✅ **Aide** : Raccourcis affichés en bas
- ✅ **Accessibilité** : Parfait pour débuter
- ✅ **Fiabilité** : Peu de risques d'erreur

### Sublime Text

#### Configuration standard
```bash
gh config set editor "subl --wait"
```

#### Configuration avancée
```bash
# Nouveau projet pour chaque édition
gh config set editor "subl --wait --new-window"

# Ajouter au projet existant
gh config set editor "subl --wait --add"
```

**Sur différents OS** :
- **macOS** : `subl` (si installé)
- **Windows** : `"C:\Program Files\Sublime Text\subl.exe" --wait`
- **Linux** : `subl` (selon l'installation)

### Atom

```bash
gh config set editor "atom --wait"
```

**Note** : Atom n'est plus maintenu depuis 2022, mais peut encore être utilisé.

### Emacs

#### GNU Emacs
```bash
gh config set editor emacs
```

#### Emacs en mode texte
```bash
gh config set editor "emacs -nw"
```

#### Spacemacs/Doom Emacs
```bash
# Spacemacs
gh config set editor "emacsclient -c"

# Doom Emacs
gh config set editor "doom run"
```

### Notepad++ (Windows)

```bash
# Installation standard
gh config set editor "notepad++ -multiInst -notabbar -nosession -noPlugin"

# Chemin complet si nécessaire
gh config set editor "\"C:\Program Files\Notepad++\notepad++.exe\" -multiInst"
```

### Éditeurs en ligne de commande

#### Micro (moderne et accessible)
```bash
gh config set editor micro
```

#### Joe (Joe's Own Editor)
```bash
gh config set editor joe
```

#### Pico (ancêtre de Nano)
```bash
gh config set editor pico
```

## 🔄 Configuration contextuelle

### Configuration par hostname

**Différents éditeurs** selon l'environnement :

```bash
# VS Code pour GitHub.com (personnel)
gh config set editor "code --wait" --host github.com

# Vim pour GitHub Enterprise (travail)
gh config set editor vim --host entreprise.github.com
```

### Configuration par repository

**Éditeur spécifique** pour certains projets :

```bash
# Dans un projet de documentation
cd mon-projet-docs
gh config set editor "code --wait" --repo

# Dans un projet serveur
cd mon-projet-serveur
gh config set editor vim --repo
```

### Vérifier les configurations

```bash
# Configuration globale
gh config get editor

# Configuration pour un hostname
gh config get editor --host entreprise.github.com

# Configuration pour le repo actuel
gh config get editor --repo
```

## 🎯 Configurations recommandées par profil

### Débutant complet

**Priorité : Simplicité et aide**

```bash
gh config set editor nano
```

**Pourquoi Nano ?**
- Interface intuitive avec aide intégrée
- Raccourcis affichés en permanence
- Peu de risques d'erreur
- Disponible sur tous les systèmes

### Développeur Web/JavaScript

**Priorité : Intégration et fonctionnalités**

```bash
gh config set editor "code --wait"
```

**Pourquoi VS Code ?**
- Coloration syntaxique Markdown
- Extensions GitHub intégrées
- Interface familière
- Intégration Git native

### Développeur Système/DevOps

**Priorité : Performance et universalité**

```bash
gh config set editor vim
```

**Pourquoi Vim ?**
- Disponible sur tous les serveurs
- Très rapide
- Fonctionne en SSH
- Puissant pour l'édition de texte

### Utilisateur macOS

**Priorité : Intégration système**

```bash
# Option 1 : VS Code
gh config set editor "code --wait"

# Option 2 : TextEdit (natif)
gh config set editor "open -W -a TextEdit"

# Option 3 : Sublime Text
gh config set editor "subl --wait"
```

### Développeur Mobile/Multi-plateforme

**Priorité : Synchronisation et accessibilité**

```bash
# VS Code avec synchronisation
gh config set editor "code --wait --new-window"
```

## 🔧 Résolution de problèmes

### Problèmes courants et solutions

#### "Command not found: code"

**Problème** : VS Code n'est pas dans le PATH.

**Solutions** :
```bash
# macOS : Installer command line tools
# VS Code → View → Command Palette → "Shell Command: Install 'code' command"

# Windows : Ajouter au PATH ou utiliser le chemin complet
gh config set editor "\"C:\Users\USERNAME\AppData\Local\Programs\Microsoft VS Code\Code.exe\" --wait"

# Linux : Vérifier l'installation
which code
sudo ln -s /snap/bin/code /usr/local/bin/code  # Si installé via snap
```

#### L'éditeur s'ouvre mais la CLI ne attend pas

**Problème** : Oubli du flag `--wait`.

**Solution** :
```bash
# Ajouter --wait à votre configuration
gh config set editor "code --wait"  # Au lieu de juste "code"
```

#### L'éditeur ne se ferme pas correctement

**Problème** : Configuration d'éditeur incorrecte.

**Solutions** :
```bash
# Vim : Vérifier comment quitter
# :wq (write + quit) ou :q! (quit without saving)

# Nano : Ctrl+X puis Y pour sauvegarder

# VS Code : Fermer l'onglet (Ctrl+W) ou la fenêtre
```

#### Caractères étranges ou encodage incorrect

**Solutions** :
```bash
# Forcer UTF-8 pour VS Code
gh config set editor "code --wait --locale=en-US"

# Pour Vim
gh config set editor "vim +set encoding=utf-8"
```

### Tests de configuration

#### Tester votre éditeur

```bash
# Créer une issue de test
gh issue create --title "Test éditeur" --body "Test de configuration"

# L'éditeur devrait s'ouvrir pour la description
```

#### Vérifier que tout fonctionne

```bash
# Test complet
echo "Test" | gh issue create --title "Test" --body-file -

# Si ça marche, votre éditeur est bien configuré !
```

## 🎨 Optimisations avancées

### Extensions et plugins

#### VS Code
**Extensions recommandées** pour GitHub :
- **GitHub Pull Requests and Issues** : Intégration directe
- **GitLens** : Informations Git enrichies
- **Markdown All in One** : Support Markdown avancé
- **GitHub Markdown Preview** : Prévisualisation GitHub

```bash
# Installation via CLI
code --install-extension GitHub.vscode-pull-request-github
code --install-extension eamodio.gitlens
```

#### Vim
**Plugins utiles** :
```vim
" Dans ~/.vimrc
" Coloration Markdown
Plugin 'plasticboy/vim-markdown'

" Intégration Git
Plugin 'tpope/vim-fugitive'

" GitHub integration
Plugin 'tpope/vim-rhubarb'
```

### Templates et snippets

#### Template d'issue dans VS Code
**Créer** `.vscode/settings.json` :
```json
{
  "github-issues.queries": [
    {
      "label": "Bug Template",
      "query": "is:open is:issue label:bug"
    }
  ]
}
```

#### Snippets Vim pour GitHub
```vim
" Dans ~/.vimrc
" Snippet pour issue
iabbrev ghissue ### Description\n\n### Steps to Reproduce\n\n### Expected Behavior\n\n### Actual Behavior
```

### Scripts d'aide

#### Script de changement d'éditeur rapide
```bash
#!/bin/bash
# switch-editor.sh

case "$1" in
    "vscode"|"code")
        gh config set editor "code --wait"
        echo "✅ Éditeur changé vers VS Code"
        ;;
    "vim")
        gh config set editor vim
        echo "✅ Éditeur changé vers Vim"
        ;;
    "nano")
        gh config set editor nano
        echo "✅ Éditeur changé vers Nano"
        ;;
    *)
        echo "Usage: $0 [vscode|vim|nano]"
        echo "Éditeur actuel: $(gh config get editor)"
        ;;
esac
```

#### Test automatisé de l'éditeur
```bash
#!/bin/bash
# test-editor.sh

echo "🧪 Test de l'éditeur configuré..."

EDITOR=$(gh config get editor)
echo "Éditeur configuré: $EDITOR"

# Créer un fichier de test
TEST_FILE="/tmp/gh-editor-test.txt"
echo "Test de l'éditeur GitHub CLI" > "$TEST_FILE"

# Tester si l'éditeur peut ouvrir le fichier
if command -v ${EDITOR%% *} >/dev/null 2>&1; then
    echo "✅ Éditeur disponible"

    # Test d'ouverture (annulé rapidement)
    timeout 2s $EDITOR "$TEST_FILE" 2>/dev/null || true
    echo "✅ Test d'ouverture réussi"
else
    echo "❌ Éditeur non disponible"
    echo "💡 Reconfigurez avec: gh config set editor <votre-editeur>"
fi

# Nettoyage
rm -f "$TEST_FILE"
```

## 💡 Conseils d'optimisation

### Raccourcis et workflows

#### Intégration VS Code + GitHub CLI
```bash
# Alias dans votre shell
alias ghpr="gh pr create && code ."
alias ghissue="gh issue create && gh issue list"
```

#### Workflow Vim optimisé
```bash
# Dans ~/.vimrc
" Raccourcis GitHub
nnoremap <leader>gi :!gh issue create<CR>
nnoremap <leader>gp :!gh pr create<CR>
```

### Configuration d'équipe

#### Standardisation
```bash
#!/bin/bash
# team-editor-setup.sh

echo "🏢 Configuration éditeur standardisée équipe"

# Éditeur principal (VS Code)
gh config set editor "code --wait"

# Configuration de backup (Nano)
if ! command -v code >/dev/null 2>&1; then
    echo "⚠️ VS Code non disponible, utilisation de Nano"
    gh config set editor nano
fi

echo "✅ Configuration équipe appliquée"
```

#### Documentation équipe
```markdown
# Guide Éditeur - Équipe Dev

## Configuration Standard
```bash
gh config set editor "code --wait"
```

## Extensions VS Code Requises
- GitHub Pull Requests and Issues
- GitLens
- Markdown All in One

## Alternative (si VS Code indisponible)
```bash
gh config set editor nano
```
```

## 🔄 Migration et sauvegarde

### Sauvegarder votre configuration

```bash
# Sauvegarder toute la config
cp ~/.config/gh/config.yml ~/backup-gh-config.yml

# Sauvegarder juste l'éditeur
echo "editor: $(gh config get editor)" > ~/editor-backup.txt
```

### Migration vers un nouvel éditeur

```bash
#!/bin/bash
# migrate-editor.sh

OLD_EDITOR=$(gh config get editor)
echo "Éditeur actuel: $OLD_EDITOR"

echo "Nouveau éditeur:"
read NEW_EDITOR

# Sauvegarder l'ancien
echo "old_editor: $OLD_EDITOR" >> ~/.gh-editor-history

# Configurer le nouveau
gh config set editor "$NEW_EDITOR"

echo "✅ Migration terminée"
echo "Pour revenir: gh config set editor '$OLD_EDITOR'"
```

## 📋 Récapitulatif des commandes essentielles

### Configuration de base
```bash
# Voir l'éditeur actuel
gh config get editor

# Configurer l'éditeur
gh config set editor "code --wait"

# Supprimer la configuration
gh config clear editor
```

### Configurations populaires
```bash
# VS Code (recommandé pour débuter)
gh config set editor "code --wait"

# Vim (terminal/serveur)
gh config set editor vim

# Nano (simple et accessible)
gh config set editor nano

# Sublime Text
gh config set editor "subl --wait"
```

### Configuration avancée
```bash
# Par hostname
gh config set editor vim --host entreprise.github.com

# Par repository
gh config set editor nano --repo

# Test de l'éditeur
gh issue create --title "Test" --body "Test"
```

## 🎯 Prochaine étape

Félicitations ! Vous avez terminé la section "Authentification et Configuration". Votre CLI GitHub est maintenant parfaitement configurée et sécurisée selon vos préférences.

Il est temps de passer aux choses sérieuses : apprendre à gérer vos repositories avec la CLI GitHub !

👉 **Continuez avec** : [Section 3 - Gestion des Repositories](../03-gestion-des-repositories/README.md)

---

*Vous avez terminé la section 2.4 et toute la section 2 ! Votre CLI GitHub est maintenant optimisée avec votre éditeur préféré. Dans la prochaine section, nous explorerons la gestion complète des repositories.*

## 🎉 Récapitulatif de la Section 2

Vous venez de terminer toute la section "Authentification et Configuration" ! Voici ce que vous maîtrisez maintenant :

✅ **2.1** - Connexion sécurisée avec toutes les options de `gh auth login`
✅ **2.2** - Configuration personnalisée avec `gh config` selon vos préférences
✅ **2.3** - Gestion experte des tokens d'accès et sécurité
✅ **2.4** - Intégration parfaite avec votre éditeur favori

**Votre CLI GitHub est maintenant** : authentifiée, sécurisée, configurée et personnalisée ! 🚀

⏭️
