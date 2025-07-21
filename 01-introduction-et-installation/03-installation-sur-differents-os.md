🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 1.3 - Installation sur différents OS (Windows, macOS, Linux)

## 🎯 Introduction

Vous êtes maintenant convaincu de l'utilité de la CLI GitHub ? Parfait ! Il est temps de l'installer sur votre ordinateur. Cette section vous guidera pas à pas pour installer la CLI GitHub sur votre système d'exploitation, quelle que soit votre configuration.

**Rassurez-vous** : L'installation est simple et rapide, généralement en quelques minutes seulement !

## 🔍 Avant de commencer : Vérifications préliminaires

### Vérifiez vos prérequis

Avant d'installer la CLI GitHub, assurons-nous que vous avez tout ce qu'il faut :

#### 1. Système d'exploitation supporté
La CLI GitHub fonctionne sur :
- **Windows** 10 ou plus récent
- **macOS** 10.14 (Mojave) ou plus récent
- **Linux** : La plupart des distributions modernes

#### 2. Git installé
La CLI GitHub travaille en complément de Git, il doit donc être installé.

**Vérifier si Git est installé :**
```bash
git --version
```

**Si Git n'est pas installé :**
- **Windows** : Téléchargez sur [git-scm.com](https://git-scm.com/download/win)
- **macOS** : `brew install git` ou téléchargez sur [git-scm.com](https://git-scm.com/download/mac)
- **Linux** : `sudo apt install git` (Ubuntu/Debian) ou équivalent

#### 3. Terminal fonctionnel
Vous devez pouvoir ouvrir et utiliser votre terminal :
- **Windows** : PowerShell, Command Prompt, ou Git Bash
- **macOS** : Terminal ou iTerm2
- **Linux** : Terminal de votre distribution

#### 4. Connexion internet
Nécessaire pour le téléchargement et l'authentification initiale.

### Identifier votre système

**Pas sûr de votre système ?**

**Windows :**
- Appuyez sur `Windows + R`, tapez `winver`, validez
- Ou : Paramètres → Système → À propos

**macOS :**
- Menu Apple → À propos de ce Mac
- Ou dans Terminal : `sw_vers`

**Linux :**
- Dans Terminal : `lsb_release -a` ou `cat /etc/os-release`

## 🪟 Installation sur Windows

### Méthode 1 : Chocolatey (Recommandée)

**Chocolatey** est un gestionnaire de paquets pour Windows qui simplifie l'installation.

#### Étape 1 : Installer Chocolatey (si pas déjà fait)

1. **Ouvrir PowerShell en tant qu'administrateur** :
   - Clic droit sur le bouton Windows
   - Sélectionner "Windows PowerShell (Admin)" ou "Terminal (Admin)"

2. **Coller cette commande** :
```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

3. **Attendre l'installation** (quelques minutes)

4. **Vérifier l'installation** :
```powershell
choco --version
```

#### Étape 2 : Installer la CLI GitHub

```powershell
choco install gh
```

**C'est tout !** Chocolatey télécharge et installe automatiquement la CLI GitHub.

### Méthode 2 : Winget (Windows 10 1709+)

**Winget** est le gestionnaire de paquets officiel de Microsoft.

#### Installation en une commande
```powershell
winget install GitHub.cli
```

### Méthode 3 : Installateur MSI (Solution universelle)

Si les gestionnaires de paquets ne fonctionnent pas :

#### Étape 1 : Télécharger
1. Allez sur [cli.github.com](https://cli.github.com/)
2. Cliquez sur "Download for Windows"
3. Choisissez le fichier `.msi` approprié :
   - **64-bit** : `gh_X.X.X_windows_amd64.msi` (le plus courant)
   - **32-bit** : `gh_X.X.X_windows_386.msi` (systèmes anciens)

#### Étape 2 : Installer
1. **Double-cliquez** sur le fichier téléchargé
2. **Suivez l'assistant d'installation** :
   - Acceptez la licence
   - Choisissez le dossier d'installation (par défaut recommandé)
   - Cliquez sur "Install"
3. **Terminez** l'installation

### Méthode 4 : Scoop (Pour les utilisateurs avancés)

**Scoop** est un gestionnaire de paquets léger pour Windows.

```powershell
scoop bucket add github-gh https://github.com/cli/scoop-gh.git
scoop install gh
```

### Vérification de l'installation Windows

**Ouvrez un nouveau terminal** (important : nouveau pour recharger le PATH) et tapez :

```powershell
gh --version
```

**Résultat attendu** :
```
gh version 2.X.X (YYYY-MM-DD)
https://github.com/cli/cli/releases/tag/vX.X.X
```

**Si ça ne marche pas** :
1. Redémarrez votre terminal
2. Vérifiez que `gh` est dans votre PATH
3. Réinstallez avec une méthode différente

## 🍎 Installation sur macOS

### Méthode 1 : Homebrew (Fortement recommandée)

**Homebrew** est LE gestionnaire de paquets de référence sur macOS.

#### Étape 1 : Installer Homebrew (si pas déjà fait)

1. **Ouvrir Terminal** :
   - Cmd + Espace → Taper "Terminal" → Entrée
   - Ou Applications → Utilitaires → Terminal

2. **Installer Homebrew** :
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

3. **Suivre les instructions** affichées à l'écran

4. **Vérifier l'installation** :
```bash
brew --version
```

#### Étape 2 : Installer la CLI GitHub

```bash
brew install gh
```

**Avantages de Homebrew** :
- Installation en une commande
- Mises à jour automatiques avec `brew upgrade`
- Gestion des dépendances
- Désinstallation facile

### Méthode 2 : MacPorts

**MacPorts** est une alternative à Homebrew.

#### Prérequis
1. Installer Xcode Command Line Tools :
```bash
xcode-select --install
```

2. Télécharger MacPorts depuis [macports.org](https://www.macports.org/install.php)

#### Installation
```bash
sudo port install gh
```

### Méthode 3 : Installateur PKG

#### Étape 1 : Télécharger
1. Visitez [cli.github.com](https://cli.github.com/)
2. Cliquez sur "Download for macOS"
3. Téléchargez le fichier `.pkg`

#### Étape 2 : Installer
1. **Double-cliquez** sur le fichier `.pkg`
2. **Suivez l'assistant** :
   - Vérification de sécurité (autorisez si demandé)
   - Acceptez la licence
   - Choisissez le disque d'installation
   - Entrez votre mot de passe administrateur si demandé
3. **Terminez** l'installation

### Méthode 4 : Compilation depuis les sources (Avancée)

**Pour les développeurs expérimentés** qui veulent la dernière version :

```bash
# Prérequis : Go installé
git clone https://github.com/cli/cli.git gh-cli
cd gh-cli
make install
```

### Vérification de l'installation macOS

**Ouvrez un nouveau Terminal** et tapez :

```bash
gh --version
```

**Résultat attendu** :
```
gh version 2.X.X (YYYY-MM-DD)
https://github.com/cli/cli/releases/tag/vX.X.X
```

## 🐧 Installation sur Linux

Linux offre de nombreuses options d'installation selon votre distribution.

### Ubuntu / Debian

#### Méthode 1 : Dépôt officiel (Recommandée)

```bash
# Ajouter la clé GPG officielle de GitHub
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg

# Ajouter le dépôt officiel
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null

# Mettre à jour et installer
sudo apt update
sudo apt install gh
```

#### Méthode 2 : Snap

```bash
sudo snap install gh
```

#### Méthode 3 : Paquet .deb

```bash
# Télécharger le paquet
wget https://github.com/cli/cli/releases/download/vX.X.X/gh_X.X.X_linux_amd64.deb

# Installer
sudo dpkg -i gh_X.X.X_linux_amd64.deb
```

### Red Hat / CentOS / Fedora

#### RHEL/CentOS (avec dnf/yum)

```bash
# Ajouter le dépôt
sudo dnf config-manager --add-repo https://cli.github.com/packages/rpm/gh-cli.repo

# Installer
sudo dnf install gh
```

#### Fedora

```bash
sudo dnf install gh
```

#### Avec RPM directement

```bash
# Télécharger le paquet RPM
wget https://github.com/cli/cli/releases/download/vX.X.X/gh_X.X.X_linux_amd64.rpm

# Installer
sudo rpm -i gh_X.X.X_linux_amd64.rpm
```

### Arch Linux

```bash
# Avec pacman
sudo pacman -S github-cli

# Ou avec yay (AUR)
yay -S github-cli
```

### openSUSE

```bash
# Ajouter le dépôt
sudo zypper addrepo https://cli.github.com/packages/rpm/gh-cli.repo

# Installer
sudo zypper install gh
```

### Alpine Linux

```bash
apk add github-cli
```

### Installation universelle (toutes distributions)

#### Méthode 1 : Binaire précompilé

```bash
# Télécharger la dernière version
curl -LO https://github.com/cli/cli/releases/latest/download/gh_linux_amd64.tar.gz

# Extraire
tar -xzf gh_linux_amd64.tar.gz

# Copier le binaire
sudo cp gh_linux_amd64/bin/gh /usr/local/bin/

# Rendre exécutable
sudo chmod +x /usr/local/bin/gh
```

#### Méthode 2 : AppImage

```bash
# Télécharger l'AppImage
wget https://github.com/cli/cli/releases/latest/download/gh_linux_amd64.AppImage

# Rendre exécutable
chmod +x gh_linux_amd64.AppImage

# Exécuter
./gh_linux_amd64.AppImage --version
```

### Vérification de l'installation Linux

```bash
gh --version
```

**Si la commande n'est pas trouvée** :
```bash
# Vérifier le PATH
echo $PATH

# Localiser gh
which gh
whereis gh

# Ajouter au PATH si nécessaire
echo 'export PATH=$PATH:/usr/local/bin' >> ~/.bashrc
source ~/.bashrc
```

## 🔧 Résolution des problèmes d'installation

### Problèmes courants et solutions

#### "Commande non trouvée" après installation

**Problème** : Le terminal ne trouve pas la commande `gh`.

**Solutions** :
1. **Redémarrez votre terminal**
2. **Vérifiez le PATH** :
   ```bash
   echo $PATH  # Linux/macOS
   echo $env:PATH  # Windows PowerShell
   ```
3. **Rechargez votre profil** :
   ```bash
   source ~/.bashrc  # Linux
   source ~/.zshrc   # macOS avec Zsh
   ```

#### Problèmes de permissions sur Linux/macOS

**Problème** : Erreurs de permission lors de l'installation.

**Solutions** :
1. **Utilisez sudo** pour les commandes d'installation
2. **Vérifiez les permissions** du dossier `/usr/local/bin`
3. **Installez dans votre répertoire utilisateur** :
   ```bash
   mkdir -p ~/.local/bin
   cp gh ~/.local/bin/
   echo 'export PATH=$PATH:~/.local/bin' >> ~/.bashrc
   ```

#### Installation qui échoue sur Windows

**Problème** : L'installation se bloque ou échoue.

**Solutions** :
1. **Exécutez PowerShell en tant qu'administrateur**
2. **Vérifiez votre antivirus** (peut bloquer le téléchargement)
3. **Essayez une méthode alternative** (MSI au lieu de Chocolatey)
4. **Téléchargez manuellement** depuis GitHub

#### Version obsolète installée

**Problème** : Une ancienne version de gh est installée.

**Solutions** :
```bash
# Vérifier la version installée
gh --version

# Mettre à jour avec votre gestionnaire de paquets
brew upgrade gh          # macOS
choco upgrade gh         # Windows
sudo apt update && sudo apt upgrade gh  # Ubuntu
```

### Test complet de l'installation

Pour vérifier que tout fonctionne correctement :

```bash
# Vérifier la version
gh --version

# Tester l'aide
gh --help

# Tester une commande simple (sans authentification)
gh repo view cli/cli --json name,description
```

**Résultat attendu** : La commande doit s'exécuter sans erreur et afficher des informations sur le repository de la CLI GitHub.

## 🎯 Optimisation post-installation

### Configuration du terminal

#### Autocomplétion

**Bash :**
```bash
# Ajouter à ~/.bashrc
eval "$(gh completion -s bash)"
```

**Zsh :**
```bash
# Ajouter à ~/.zshrc
eval "$(gh completion -s zsh)"
```

**PowerShell (Windows) :**
```powershell
# Ajouter à votre profil PowerShell
gh completion -s powershell | Out-String | Invoke-Expression
```

#### Alias utiles

Ajoutez ces alias à votre configuration shell :

```bash
# Bash/Zsh (~/.bashrc ou ~/.zshrc)
alias ghi="gh issue"
alias ghp="gh pr"
alias ghr="gh repo"

# PowerShell (profil PowerShell)
Set-Alias ghi gh issue
Set-Alias ghp gh pr
Set-Alias ghr gh repo
```

### Vérification de sécurité

#### Vérifier l'authenticité de l'installation

```bash
# Vérifier la signature (Linux/macOS)
gh --version

# Comparer avec la version officielle
curl -s https://api.github.com/repos/cli/cli/releases/latest | grep tag_name
```

#### Audit de sécurité

```bash
# Vérifier les permissions
ls -la $(which gh)

# Vérifier l'origine du binaire
file $(which gh)
```

## 📋 Tableau récapitulatif des méthodes d'installation

| OS | Méthode recommandée | Méthode alternative | Temps | Difficulté |
|---|---|---|---|---|
| **Windows 10/11** | Chocolatey: `choco install gh` | Winget: `winget install GitHub.cli` | 2-5 min | Facile |
| **macOS** | Homebrew: `brew install gh` | Installateur PKG | 1-3 min | Très facile |
| **Ubuntu/Debian** | APT: Dépôt officiel | Snap: `sudo snap install gh` | 2-5 min | Facile |
| **RHEL/CentOS** | DNF: Dépôt officiel | RPM direct | 2-5 min | Facile |
| **Arch Linux** | Pacman: `sudo pacman -S github-cli` | AUR avec yay | 1-3 min | Facile |
| **Autres Linux** | Binaire précompilé | AppImage | 3-7 min | Moyen |

## 🎉 Félicitations !

Si vous avez suivi cette section jusqu'au bout, vous devriez maintenant avoir :

✅ **La CLI GitHub installée** sur votre système
✅ **Une installation vérifiée** qui fonctionne
✅ **Connaissance des méthodes** d'installation pour votre OS
✅ **Solutions aux problèmes courants**

### Prochaines étapes

Maintenant que la CLI GitHub est installée, il est temps de la configurer et de vous authentifier !

👉 **Continuez avec** : [Première configuration et authentification](04-premiere-configuration-et-authentification.md)

---

*Vous avez terminé la section 1.3 ! La CLI GitHub est maintenant installée sur votre machine. Dans la prochaine section, nous allons la configurer et établir votre première connexion à GitHub.*

⏭️
