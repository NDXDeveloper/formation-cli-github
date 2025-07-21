🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 2.2 - Configuration des préférences (`gh config`)

## 🎯 Introduction

Maintenant que vous maîtrisez l'authentification, il est temps de personnaliser votre CLI GitHub ! La commande `gh config` est votre outil de personnalisation qui vous permet d'adapter la CLI à votre façon de travailler.

**Analogie** : Si `gh auth login` vous donne les clés de la maison, `gh config` vous permet de régler la température, l'éclairage et tous les paramètres pour vous sentir chez vous !

## 🔧 Comprendre `gh config`

### Qu'est-ce que la configuration ?

**La configuration, ce sont tous les petits réglages** qui rendent votre expérience CLI GitHub plus agréable et efficace :

- **Quel éditeur** s'ouvre quand vous écrivez un message
- **Quel navigateur** s'ouvre pour les liens GitHub
- **Comment** les informations s'affichent dans votre terminal
- **Quels protocoles** utiliser pour Git

### Pourquoi configurer ?

**Sans configuration** : La CLI utilise des paramètres par défaut qui peuvent ne pas vous convenir.

**Avec configuration** : Tout fonctionne exactement comme vous le souhaitez !

**Exemple concret** :
- Défaut : Les messages s'ouvrent dans `nano` (éditeur basique)
- Configuré : Les messages s'ouvrent dans VS Code que vous maîtrisez

## 📋 Anatomie de `gh config`

### Syntaxe de base

```bash
# Voir toute la configuration
gh config list

# Définir une valeur
gh config set <clé> <valeur>

# Récupérer une valeur
gh config get <clé>

# Supprimer une configuration
gh config clear <clé>
```

### Types de configuration

#### Configuration globale
**S'applique partout** où vous utilisez la CLI GitHub :
```bash
gh config set editor "code --wait"
```

#### Configuration par hostname
**S'applique à un GitHub spécifique** (utile si vous avez GitHub.com + Enterprise) :
```bash
gh config set editor vim --host entreprise.github.com
```

#### Configuration par repository
**S'applique uniquement au projet actuel** :
```bash
gh config set editor nano --repo
```

## 🎨 Configurations essentielles

### 1. Éditeur par défaut (`editor`)

**L'éditeur est crucial** car la CLI l'ouvre pour :
- Écrire des messages de commit étendus
- Rédiger des descriptions d'issues et de PR
- Modifier des fichiers de configuration

#### Visual Studio Code
```bash
gh config set editor "code --wait"
```

**Explication** :
- `code` : Lance VS Code
- `--wait` : La CLI attend que vous fermiez l'éditeur avant de continuer

#### Autres éditeurs populaires
```bash
# Vim
gh config set editor vim

# Nano (simple et accessible)
gh config set editor nano

# Sublime Text
gh config set editor "subl --wait"

# Atom
gh config set editor "atom --wait"

# Emacs
gh config set editor emacs

# Notepad++ (Windows)
gh config set editor "notepad++ -multiInst"
```

#### Vérifier votre configuration
```bash
gh config get editor
```

### 2. Navigateur par défaut (`browser`)

**Le navigateur s'ouvre** pour :
- Authentification web
- Visualiser des PR ou issues
- Accéder aux pages GitHub

```bash
# Chrome/Chromium
gh config set browser chrome

# Firefox
gh config set browser firefox

# Safari (macOS)
gh config set browser safari

# Edge
gh config set browser edge

# Brave
gh config set browser brave
```

### 3. Protocole Git (`git_protocol`)

**Détermine comment Git communique** avec GitHub :

#### HTTPS (Recommandé pour débuter)
```bash
gh config set git_protocol https
```

**Avantages HTTPS** :
- ✅ Simple à configurer
- ✅ Fonctionne partout (même derrière des proxies)
- ✅ Utilise votre authentification GitHub

#### SSH (Pour utilisateurs avancés)
```bash
gh config set git_protocol ssh
```

**Avantages SSH** :
- ✅ Plus rapide
- ✅ Pas de mot de passe à retaper
- ✅ Très sécurisé

**Prérequis SSH** : Clé SSH configurée sur GitHub

### 4. Pager (`pager`)

**Le pager affiche les longues sorties** page par page :

```bash
# Less (par défaut, recommandé)
gh config set pager less

# Cat (affiche tout d'un coup)
gh config set pager cat

# Bat (coloration syntaxique avancée)
gh config set pager bat

# Désactiver le pager
gh config set pager ""
```

**Quand est-ce utile ?** Quand vous listez beaucoup d'issues ou de PR.

## 🎯 Configurations avancées

### 5. Format de sortie par défaut (`format`)

**Personnalise l'affichage** des informations :

```bash
# JSON (pour scripts)
gh config set format json

# Table (lisible pour humains)
gh config set format table

# YAML
gh config set format yaml
```

### 6. Prompt interactif (`prompt`)

**Active/désactive les questions interactives** :

```bash
# Activer les prompts (par défaut)
gh config set prompt enabled

# Désactiver (pour scripts automatisés)
gh config set prompt disabled
```

### 7. Mise à jour automatique (`update_check`)

**Vérifie automatiquement** les nouvelles versions :

```bash
# Activer les vérifications
gh config set update_check true

# Désactiver
gh config set update_check false
```

## 🏢 Configuration multi-environnements

### Pourquoi des configurations différentes ?

Vous pourriez vouloir :
- **Éditeur différent** au travail vs à la maison
- **Protocole différent** selon l'environnement réseau
- **Navigateur différent** selon le contexte

### Configuration par hostname

```bash
# Pour GitHub.com (personnel)
gh config set editor "code --wait" --host github.com
gh config set browser chrome --host github.com

# Pour GitHub Enterprise (travail)
gh config set editor vim --host entreprise.github.com
gh config set browser firefox --host entreprise.github.com
```

### Configuration par repository

```bash
# Dans un projet spécifique
cd mon-projet-special
gh config set editor nano --repo
gh config set git_protocol ssh --repo
```

## 📊 Gestion de votre configuration

### Voir toute votre configuration

```bash
# Configuration globale
gh config list

# Configuration pour un hostname spécifique
gh config list --host entreprise.github.com
```

**Exemple de sortie** :
```
editor=code --wait
browser=chrome
git_protocol=https
pager=less
```

### Voir une configuration spécifique

```bash
# Valeur globale
gh config get editor

# Valeur pour un hostname
gh config get editor --host entreprise.github.com

# Valeur pour le repo actuel (si configurée)
gh config get editor --repo
```

### Supprimer une configuration

```bash
# Supprimer une configuration globale
gh config clear editor

# Supprimer pour un hostname
gh config clear editor --host entreprise.github.com

# Supprimer pour le repo actuel
gh config clear editor --repo
```

## 🎨 Configurations par cas d'usage

### Développeur débutant

**Configuration simple et accessible** :

```bash
gh config set editor nano           # Éditeur simple
gh config set browser chrome        # Navigateur populaire
gh config set git_protocol https    # Simple à configurer
gh config set pager less            # Navigation page par page
```

### Développeur VS Code

**Optimisé pour l'écosystème VS Code** :

```bash
gh config set editor "code --wait"  # VS Code avec attente
gh config set browser chrome        # Bon support des devtools
gh config set git_protocol https    # Intégration VS Code Git
```

### Développeur Vim/Terminal

**Tout dans le terminal** :

```bash
gh config set editor vim            # Éditeur terminal
gh config set browser firefox       # Navigateur séparé
gh config set git_protocol ssh      # Performance maximale
gh config set pager less            # Navigation efficace
```

### DevOps/Scripts

**Configuration pour l'automatisation** :

```bash
gh config set prompt disabled       # Pas d'interaction
gh config set format json           # Sortie parsable
gh config set pager cat             # Pas de pagination
```

### Travail en équipe

**Configuration standardisée** :

```bash
gh config set editor "code --wait"  # Éditeur commun
gh config set git_protocol https    # Compatible partout
gh config set browser chrome        # Standard équipe
```

## 🔧 Configurations système vs utilisateur

### Fichiers de configuration

La CLI GitHub stocke sa configuration dans :

#### Linux/macOS
```
~/.config/gh/config.yml
```

#### Windows
```
%AppData%\GitHub CLI\config.yml
```

### Structure du fichier de configuration

**Exemple de `config.yml`** :
```yaml
# Version du schéma
version: 1

# Configuration par hostname
hosts:
    github.com:
        user: votre-nom-utilisateur
        oauth_token: ghp_xxxxx
        git_protocol: https
    entreprise.github.com:
        user: votre.nom.pro
        oauth_token: ghp_yyyyy
        git_protocol: ssh

# Configuration globale
editor: code --wait
browser: chrome
pager: less
```

### Sauvegarde et restauration

#### Sauvegarder votre configuration
```bash
# Copier le fichier de configuration
cp ~/.config/gh/config.yml ~/ma-config-gh-backup.yml
```

#### Restaurer votre configuration
```bash
# Restaurer depuis une sauvegarde
cp ~/ma-config-gh-backup.yml ~/.config/gh/config.yml
```

## 🛠️ Dépannage de configuration

### Problèmes courants et solutions

#### L'éditeur ne s'ouvre pas
```bash
# Vérifier la configuration
gh config get editor

# Tester l'éditeur manuellement
code --wait test.txt

# Reconfigurer si nécessaire
gh config set editor "code --wait"
```

#### Le navigateur ne s'ouvre pas
```bash
# Vérifier la configuration
gh config get browser

# Tester le navigateur
chrome https://github.com

# Reconfigurer
gh config set browser chrome
```

#### Configuration ignorée
```bash
# Vérifier les priorités (repo > host > global)
gh config list --repo
gh config list --host github.com
gh config list
```

### Reset de configuration

#### Reset complet
```bash
# Sauvegarder d'abord
cp ~/.config/gh/config.yml ~/backup-config.yml

# Supprimer toute la configuration
rm ~/.config/gh/config.yml

# Recommencer la configuration
gh auth login
```

#### Reset sélectif
```bash
# Supprimer juste l'éditeur
gh config clear editor

# Reconfigurer
gh config set editor nano
```

## 🎯 Optimisation et productivité

### Alias de configuration

**Créez des alias** pour vos configurations fréquentes :

```bash
# Dans votre .bashrc ou .zshrc
alias gh-config-work="gh config set editor vim --host entreprise.github.com"
alias gh-config-home="gh config set editor 'code --wait' --host github.com"
```

### Scripts de configuration

**Script pour configurer un nouvel environnement** :

```bash
#!/bin/bash
# setup-gh-config.sh

echo "Configuration de la CLI GitHub..."

# Configuration de base
gh config set editor "code --wait"
gh config set browser chrome
gh config set git_protocol https
gh config set pager less

echo "Configuration terminée !"
gh config list
```

### Configuration d'équipe

**Fichier de configuration partagé** pour l'équipe :

```bash
# team-gh-config.sh
#!/bin/bash

# Configuration standardisée équipe
gh config set editor "code --wait"
gh config set git_protocol https
gh config set browser chrome
gh config set pager less

echo "Configuration équipe appliquée ✅"
```

## 📱 Configuration cross-platform

### Gérer les différences d'OS

#### Configuration adaptative
```bash
# Détecter l'OS et configurer l'éditeur approprié
if [[ "$OSTYPE" == "darwin"* ]]; then
    # macOS
    gh config set editor "code --wait"
elif [[ "$OSTYPE" == "linux-gnu"* ]]; then
    # Linux
    gh config set editor "code --wait"
elif [[ "$OSTYPE" == "msys" ]] || [[ "$OSTYPE" == "cygwin" ]]; then
    # Windows
    gh config set editor "code --wait"
fi
```

#### Navigateurs par plateforme
```bash
# Configuration du navigateur selon l'OS
case "$OSTYPE" in
    darwin*)  gh config set browser safari ;;
    linux*)   gh config set browser firefox ;;
    msys*)    gh config set browser edge ;;
esac
```

## 💡 Bonnes pratiques

### 1. Commencez simple
```bash
# Configuration minimale pour débuter
gh config set editor nano
gh config set browser chrome
gh config set git_protocol https
```

### 2. Évoluez progressivement
- Ajoutez des configurations au fur et à mesure
- Testez chaque changement
- Documentez vos choix

### 3. Sauvegardez votre configuration
```bash
# Sauvegarde régulière
cp ~/.config/gh/config.yml ~/Documents/gh-config-backup-$(date +%Y%m%d).yml
```

### 4. Synchronisez entre machines
```bash
# Utiliser Git pour synchroniser
cd ~/dotfiles
cp ~/.config/gh/config.yml gh-config.yml
git add gh-config.yml
git commit -m "Update GH CLI config"
git push
```

## 📋 Configurations recommandées par profil

### Débutant complet
```bash
gh config set editor nano
gh config set browser chrome
gh config set git_protocol https
gh config set pager less
```

### Développeur web moderne
```bash
gh config set editor "code --wait"
gh config set browser chrome
gh config set git_protocol https
gh config set pager less
```

### Développeur système/DevOps
```bash
gh config set editor vim
gh config set browser firefox
gh config set git_protocol ssh
gh config set pager less
gh config set prompt disabled  # Pour scripts
```

### Équipe/Organisation
```bash
gh config set editor "code --wait"
gh config set browser chrome
gh config set git_protocol https
gh config set pager less
gh config set update_check true
```

## 🎯 Récapitulatif des commandes essentielles

### Gestion de base
```bash
# Voir toute la configuration
gh config list

# Définir une valeur
gh config set editor "code --wait"

# Récupérer une valeur
gh config get editor

# Supprimer une configuration
gh config clear editor
```

### Configuration avancée
```bash
# Par hostname
gh config set editor vim --host entreprise.github.com

# Par repository
gh config set git_protocol ssh --repo

# Configuration multiple
gh config set editor "code --wait" browser chrome git_protocol https
```

## 🎯 Prochaine étape

Votre CLI GitHub est maintenant configurée selon vos préférences ! Il est temps d'approfondir la gestion des tokens d'accès pour des usages plus avancés et sécurisés.

👉 **Continuez avec** : [Gestion des tokens d'accès](03-gestion-des-tokens-dacces.md)

---

*Vous avez terminé la section 2.2 ! Votre CLI GitHub est maintenant personnalisée et adaptée à votre façon de travailler. Dans la prochaine section, nous explorerons la gestion avancée des tokens d'accès.*

⏭️
