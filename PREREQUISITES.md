# Prérequis - Formation CLI GitHub

Ce document détaille tous les prérequis nécessaires pour suivre efficacement cette formation sur la CLI GitHub.

## 🎯 Niveau requis

**Niveau débutant à intermédiaire** - Cette formation est accessible aux développeurs ayant des bases en développement et en ligne de commande.

## 💻 Prérequis techniques

### 1. Système d'exploitation compatible

**Systèmes supportés :**
- **Linux** (Ubuntu 18.04+, Debian 10+, CentOS 8+, Fedora 32+)
- **macOS** (10.14 Mojave ou plus récent)
- **Windows** (Windows 10 ou Windows 11)

### 2. Terminal/Ligne de commande

**Connaissances requises :**
- Navigation dans les dossiers (`cd`, `ls`/`dir`, `pwd`)
- Création et modification de fichiers (`touch`, `mkdir`, `nano`/`vim`)
- Exécution de commandes avec paramètres
- Compréhension des chemins relatifs et absolus

**Terminaux recommandés :**
- **Linux** : Terminal par défaut, Gnome Terminal, Konsole
- **macOS** : Terminal, iTerm2
- **Windows** : PowerShell, Windows Terminal, Git Bash

### 3. Git installé et configuré

**Installation de Git :**
```bash
# Ubuntu/Debian
sudo apt update && sudo apt install git

# macOS (avec Homebrew)
brew install git

# Windows
# Télécharger depuis https://git-scm.com/download/win
```

**Configuration minimale requise :**
```bash
git config --global user.name "Votre Nom"
git config --global user.email "votre.email@example.com"
```

**Vérification de l'installation :**
```bash
git --version
# Doit afficher une version 2.20+ de préférence
```

## 🔐 Compte et accès GitHub

### 1. Compte GitHub actif

- **Compte GitHub** : Gratuit ou payant sur [github.com](https://github.com)
- **Email vérifié** : Nécessaire pour certaines fonctionnalités
- **Authentification à deux facteurs** : Fortement recommandée

### 2. Permissions et droits

**Pour la formation complète, vous devrez pouvoir :**
- Créer des repositories publics et privés
- Créer des issues et pull requests
- Accéder aux GitHub Actions (inclus dans les comptes gratuits)
- Créer des releases

## 🛠️ Outils complémentaires

### 1. Éditeur de texte

**Un éditeur pour modifier les fichiers markdown et configuration :**
- **Visual Studio Code** (recommandé)
- **Nano** (terminal)
- **Vim/Neovim** (terminal)
- **Sublime Text**
- **Atom**

### 2. Gestionnaires de paquets (optionnel mais recommandé)

**Pour faciliter l'installation de la CLI GitHub :**
- **Linux** : `apt`, `yum`, `dnf`, `pacman` (selon la distribution)
- **macOS** : Homebrew (`brew`)
- **Windows** : Chocolatey (`choco`) ou Windows Package Manager (`winget`)

## 📚 Connaissances préalables

### 1. Concepts Git de base (indispensables)

**Vous devez comprendre :**
- Qu'est-ce qu'un repository
- Les concepts de commit, branch, merge
- La différence entre Git et GitHub
- Les commandes Git de base : `clone`, `add`, `commit`, `push`, `pull`

**Si vous débutez avec Git :**
- [Git Handbook](https://guides.github.com/introduction/git-handbook/) (officiel GitHub)
- [Learn Git Branching](https://learngitbranching.js.org/) (interactif)

### 2. Développement logiciel (recommandé)

**Concepts utiles :**
- Cycle de vie d'un projet logiciel
- Notions de versioning et releases
- Travail collaboratif sur du code
- Bases des méthodologies Agile/Scrum

### 3. Ligne de commande (indispensable)

**Vous devez être à l'aise avec :**
- L'exécution de commandes avec options et paramètres
- La lecture de messages d'erreur en anglais
- L'utilisation de pipes (`|`) et redirections (`>`, `>>`)
- Les variables d'environnement de base

## 🔧 Vérification des prérequis

### Script de vérification automatique

```bash
#!/bin/bash
echo "🔍 Vérification des prérequis pour la formation CLI GitHub"
echo ""

# Vérification Git
if command -v git &> /dev/null; then
    echo "✅ Git installé : $(git --version)"
else
    echo "❌ Git n'est pas installé"
fi

# Vérification de la configuration Git
if git config user.name &> /dev/null && git config user.email &> /dev/null; then
    echo "✅ Git configuré (nom: $(git config user.name))"
else
    echo "⚠️  Git non configuré - exécutez les commandes de configuration"
fi

# Vérification de la connectivité GitHub
if curl -s https://api.github.com &> /dev/null; then
    echo "✅ Connexion à GitHub API : OK"
else
    echo "❌ Impossible de se connecter à GitHub"
fi

# Vérification SSH (optionnel)
if ssh -T git@github.com 2>&1 | grep -q "successfully authenticated"; then
    echo "✅ Authentification SSH GitHub : OK"
else
    echo "ℹ️  SSH GitHub non configuré (optionnel)"
fi

echo ""
echo "🎯 Si tous les éléments marqués ✅ sont présents, vous pouvez commencer la formation !"
```

### Checklist manuelle

- [ ] Système d'exploitation compatible
- [ ] Terminal/ligne de commande fonctionnel
- [ ] Git installé et configuré
- [ ] Compte GitHub actif avec email vérifié
- [ ] Éditeur de texte installé
- [ ] Connexion internet stable
- [ ] Connaissances de base Git et ligne de commande

## 🚀 Installation de la CLI GitHub

**Cette étape sera détaillée dans la section 1 de la formation, mais voici un aperçu :**

```bash
# macOS (Homebrew)
brew install gh

# Ubuntu/Debian
sudo apt install gh

# Windows (Chocolatey)
choco install gh

# Ou téléchargement direct depuis GitHub Releases
```

## 📖 Ressources préparatoires recommandées

### Documentation officielle
- [GitHub Docs](https://docs.github.com) - Documentation complète
- [GitHub CLI Manual](https://cli.github.com/manual/) - Manuel officiel

### Lectures préparatoires (optionnel)
- [GitHub Flow](https://guides.github.com/introduction/flow/) - Méthodologie de travail
- [Understanding the GitHub flow](https://guides.github.com/introduction/flow/) - Workflow recommandé

## ⚠️ Notes importantes

**Limitations des comptes gratuits :**
- GitHub Actions : 2000 minutes/mois
- Stockage : 500 MB pour les artifacts
- Certaines fonctionnalités avancées peuvent nécessiter un compte Pro

**Confidentialité :**
- Nous recommandons de créer un repository de test pour les exercices
- Évitez d'utiliser des données sensibles pendant la formation

**Support :**
- Cette formation couvre la CLI GitHub v2.0+
- Certaines fonctionnalités peuvent évoluer entre les versions

---

Une fois tous ces prérequis vérifiés, vous êtes prêt à commencer la formation ! 🎉
