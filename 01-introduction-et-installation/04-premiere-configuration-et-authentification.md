🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 1.4 - Première configuration et authentification

## 🎯 Introduction

Excellent ! Vous avez installé la CLI GitHub avec succès. Maintenant, il est temps de la connecter à votre compte GitHub et de la configurer selon vos préférences. Cette section vous guidera pas à pas pour votre première authentification et configuration.

**Rassurez-vous** : Le processus est sécurisé et guidé. GitHub a conçu cette étape pour être aussi simple que possible !

## 🔐 Comprendre l'authentification GitHub

### Pourquoi s'authentifier ?

**Analogie** : Imaginez la CLI GitHub comme votre carte bancaire. Sans code PIN (authentification), elle ne peut rien faire. Avec l'authentification, elle peut accéder à vos projets et effectuer des actions en votre nom.

### Méthodes d'authentification disponibles

GitHub propose plusieurs méthodes pour vous connecter :

#### 1. **OAuth via navigateur** (Recommandée pour débuter)
- **Simple** : GitHub ouvre votre navigateur
- **Sécurisé** : Vous vous connectez sur GitHub.com directement
- **Automatique** : Permissions gérées automatiquement

#### 2. **Token d'accès personnel (PAT)**
- **Contrôle** : Vous définissez précisément les permissions
- **Durable** : Pas d'expiration automatique
- **Flexible** : Utilisable dans des scripts

#### 3. **Clé SSH**
- **Technique** : Pour les utilisateurs Git expérimentés
- **Performance** : Authentification rapide
- **Sécurisé** : Chiffrement asymétrique

**Pour débuter** : Nous recommandons la méthode OAuth qui est la plus simple !

## 🚀 Première authentification : Méthode OAuth

### Étape 1 : Lancer l'authentification

Ouvrez votre terminal et tapez :

```bash
gh auth login
```

### Étape 2 : Choisir le service GitHub

La CLI vous demande quel service GitHub utiliser :

```
? What account do you want to log into?
  > GitHub.com
    GitHub Enterprise Server
```

**Pour la plupart des utilisateurs** : Choisissez `GitHub.com` et appuyez sur Entrée.

**GitHub Enterprise** : Si vous travaillez dans une entreprise qui utilise GitHub Enterprise, choisissez la deuxième option.

### Étape 3 : Choisir le protocole

```
? What is your preferred protocol for Git operations?
  > HTTPS
    SSH
```

**Pour débuter** : Choisissez `HTTPS` (plus simple à configurer).

**Différence** :
- **HTTPS** : Utilise votre nom d'utilisateur/mot de passe ou token
- **SSH** : Utilise des clés cryptographiques (plus technique)

### Étape 4 : Authentification via navigateur

```
? How would you like to authenticate GitHub CLI?
  > Login with a web browser
    Paste an authentication token
```

Choisissez `Login with a web browser` pour la méthode la plus simple.

### Étape 5 : Code de vérification

La CLI affiche un code de vérification :

```
! First copy your one-time code: XXXX-XXXX
Press Enter to open github.com in your browser...
```

1. **Copiez le code** affiché (sélectionnez et Ctrl+C / Cmd+C)
2. **Appuyez sur Entrée**
3. **Votre navigateur s'ouvre** automatiquement sur GitHub

### Étape 6 : Connexion sur GitHub

Dans votre navigateur :

1. **Connectez-vous** à GitHub (si pas déjà connecté)
2. **Collez le code** de vérification dans le champ prévu
3. **Cliquez sur "Continue"**
4. **Autorisez l'application** "GitHub CLI" à accéder à votre compte

### Étape 7 : Confirmation

Retournez dans votre terminal. Vous devriez voir :

```
✓ Authentication complete.
- gh config set -h github.com git_protocol https
✓ Configured git protocol
✓ Logged in as votre-nom-utilisateur
```

**Félicitations !** Vous êtes maintenant authentifié !

## 🔧 Vérifier votre authentification

### Test simple

Vérifiez que tout fonctionne avec :

```bash
gh auth status
```

**Résultat attendu** :
```
github.com
  ✓ Logged in to github.com as votre-nom-utilisateur (oauth_token)
  ✓ Git operations for github.com configured to use https protocol.
  ✓ Token: *******************
```

### Test avec une commande pratique

```bash
gh repo list
```

Cette commande devrait afficher la liste de vos repositories GitHub.

**Si vous voyez vos repos** : ✅ L'authentification fonctionne parfaitement !

## 🛠️ Configuration personnalisée

### Configurer l'éditeur par défaut

La CLI GitHub a besoin de connaître votre éditeur préféré pour certaines actions :

```bash
# Pour Visual Studio Code
gh config set editor "code --wait"

# Pour Vim
gh config set editor vim

# Pour Nano
gh config set editor nano

# Pour Sublime Text
gh config set editor "subl --wait"

# Pour Atom
gh config set editor "atom --wait"
```

**Pourquoi "wait" ?** : Cette option fait que la CLI attend que vous fermiez l'éditeur avant de continuer.

### Configurer le navigateur par défaut

```bash
# Pour Chrome
gh config set browser chrome

# Pour Firefox
gh config set browser firefox

# Pour Safari (macOS)
gh config set browser safari

# Pour Edge
gh config set browser edge
```

### Voir toutes vos configurations

```bash
gh config list
```

**Exemple de sortie** :
```
editor=code --wait
git_protocol=https
browser=chrome
```

## 🔐 Authentification avec Token d'accès personnel

### Quand utiliser un token ?

Les tokens sont utiles pour :
- **Scripts automatisés**
- **Serveurs de CI/CD**
- **Contrôle précis des permissions**
- **Expiration personnalisée**

### Créer un token d'accès personnel

#### Étape 1 : Aller sur GitHub.com

1. Connectez-vous à [github.com](https://github.com)
2. Cliquez sur votre **photo de profil** (en haut à droite)
3. Sélectionnez **"Settings"**

#### Étape 2 : Accéder aux tokens

1. Dans le menu de gauche, cliquez sur **"Developer settings"** (tout en bas)
2. Cliquez sur **"Personal access tokens"**
3. Sélectionnez **"Tokens (classic)"**

#### Étape 3 : Générer un nouveau token

1. Cliquez sur **"Generate new token"** → **"Generate new token (classic)"**
2. **Donnez un nom** au token (ex: "CLI GitHub sur mon PC")
3. **Choisissez l'expiration** :
   - 30 jours (sécurisé)
   - 90 jours (équilibré)
   - Pas d'expiration (pratique mais moins sécurisé)

#### Étape 4 : Sélectionner les permissions

**Pour un usage général**, cochez ces permissions :
- ✅ `repo` (accès complet aux repositories)
- ✅ `workflow` (GitHub Actions)
- ✅ `write:packages` (GitHub Packages)
- ✅ `delete:packages` (supprimer des packages)
- ✅ `admin:org` (gestion d'organisation)
- ✅ `admin:public_key` (clés SSH)
- ✅ `admin:repo_hook` (webhooks)
- ✅ `user` (profil utilisateur)
- ✅ `delete_repo` (supprimer des repos)

#### Étape 5 : Générer et copier

1. Cliquez sur **"Generate token"**
2. **Copiez immédiatement** le token affiché
3. **Sauvegardez-le** dans un gestionnaire de mots de passe

⚠️ **Important** : Vous ne pourrez plus voir ce token ! Si vous le perdez, il faudra en créer un nouveau.

### Utiliser le token pour l'authentification

```bash
gh auth login --with-token
```

Ensuite, **collez votre token** et appuyez sur Entrée.

**Alternative** : Sauvegarder le token dans un fichier et l'utiliser :

```bash
echo "votre_token_ici" > token.txt
gh auth login --with-token < token.txt
rm token.txt  # Supprimer le fichier pour la sécurité
```

## 🔑 Authentification SSH (Avancée)

### Prérequis pour SSH

L'authentification SSH nécessite :
- Une paire de clés SSH générée
- La clé publique ajoutée à votre compte GitHub

### Générer une clé SSH

```bash
# Générer une nouvelle clé SSH (remplacez l'email)
ssh-keygen -t ed25519 -C "votre.email@example.com"

# Ou pour les systèmes plus anciens
ssh-keygen -t rsa -b 4096 -C "votre.email@example.com"
```

**Questions posées** :
1. **Fichier** : Appuyez sur Entrée pour l'emplacement par défaut
2. **Passphrase** : Tapez un mot de passe sécurisé (recommandé)

### Ajouter la clé à l'agent SSH

```bash
# Démarrer l'agent SSH
eval "$(ssh-agent -s)"

# Ajouter votre clé
ssh-add ~/.ssh/id_ed25519
```

### Ajouter la clé à GitHub

```bash
# Copier la clé publique dans le presse-papier

# Linux
cat ~/.ssh/id_ed25519.pub | xclip -selection clipboard

# macOS
pbcopy < ~/.ssh/id_ed25519.pub

# Windows (Git Bash)
cat ~/.ssh/id_ed25519.pub | clip
```

Ensuite :
1. Allez sur **GitHub.com → Settings → SSH and GPG keys**
2. Cliquez sur **"New SSH key"**
3. Donnez un **titre** à votre clé
4. **Collez** la clé publique
5. Cliquez sur **"Add SSH key"**

### Authentifier la CLI avec SSH

```bash
gh auth login
```

Choisissez :
- GitHub.com
- **SSH** comme protocole
- **Login with a web browser**

## 🔄 Gérer plusieurs comptes

### Cas d'usage

Vous pourriez avoir besoin de plusieurs comptes :
- **Personnel** et **professionnel**
- **Différentes organisations**
- **GitHub.com** et **GitHub Enterprise**

### Changer de compte

```bash
# Voir le compte actuel
gh auth status

# Se déconnecter
gh auth logout

# Se connecter à un autre compte
gh auth login
```

### Utiliser plusieurs comptes simultanément

```bash
# Se connecter à GitHub Enterprise en plus
gh auth login --hostname votre-entreprise.github.com

# Basculer entre les comptes
gh auth switch --hostname github.com
gh auth switch --hostname votre-entreprise.github.com
```

## 🛡️ Sécurité et bonnes pratiques

### Sécuriser votre authentification

#### 1. Authentification à deux facteurs (2FA)
**Fortement recommandé** ! Activez 2FA sur votre compte GitHub :
- GitHub.com → Settings → Account security → Two-factor authentication

#### 2. Tokens avec permissions minimales
- Donnez seulement les permissions nécessaires
- Définissez une date d'expiration
- Renouvelez régulièrement

#### 3. Audit régulier
```bash
# Vérifier vos authentifications
gh auth status

# Lister vos tokens sur GitHub.com
# Settings → Developer settings → Personal access tokens
```

### Que faire en cas de problème de sécurité ?

#### Si vous pensez que votre token est compromis :

1. **Révoquez immédiatement** le token sur GitHub.com
2. **Créez un nouveau token**
3. **Authentifiez-vous à nouveau** :
```bash
gh auth logout
gh auth login --with-token
```

#### Si votre clé SSH est compromise :

1. **Supprimez la clé** de GitHub.com
2. **Générez une nouvelle paire de clés**
3. **Ajoutez la nouvelle clé publique** à GitHub

## 🧪 Tests de validation

### Vérifier que tout fonctionne

```bash
# Test 1 : Statut d'authentification
gh auth status

# Test 2 : Lister vos repositories
gh repo list

# Test 3 : Voir votre profil
gh api user

# Test 4 : Créer une issue de test (dans un repo test)
gh issue create --title "Test CLI" --body "Test de la CLI GitHub"

# Test 5 : Lister les issues
gh issue list
```

### Résultats attendus

✅ **Tous les tests réussissent** : Votre configuration est parfaite !

❌ **Un test échoue** : Vérifiez :
1. Votre connexion internet
2. Vos permissions de token
3. Que vous êtes dans un repository GitHub (pour les tests d'issues)

## 🎨 Personnalisation avancée

### Thèmes et couleurs

```bash
# Activer les couleurs (généralement activé par défaut)
gh config set prompt enabled

# Configurer le pager (pour les longues sorties)
gh config set pager less
```

### Alias personnalisés

```bash
# Créer des raccourcis pour vos commandes fréquentes
gh alias set prs 'pr list'
gh alias set issues 'issue list'
gh alias set myrepos 'repo list --limit 20'

# Utiliser vos alias
gh prs
gh issues
gh myrepos
```

### Configuration par projet

Vous pouvez avoir des configurations différentes par repository :

```bash
# Dans un repository spécifique
cd mon-projet
gh config set --repo editor vim
gh config set --repo browser firefox
```

## 📋 Résumé de votre configuration

À ce stade, vous devriez avoir :

✅ **Authentification fonctionnelle** à GitHub
✅ **Éditeur configuré** pour les tâches CLI
✅ **Navigateur configuré** pour l'intégration web
✅ **Tests de validation** réussis
✅ **Compréhension** des options de sécurité

### Commandes essentielles à retenir

```bash
# Vérifier l'authentification
gh auth status

# Voir la configuration
gh config list

# Aide sur n'importe quelle commande
gh help
gh auth --help
gh config --help
```

## 🎯 Prochaine étape

Félicitations ! Vous avez terminé la configuration de base de la CLI GitHub. Vous êtes maintenant prêt à découvrir les fonctionnalités avancées.

La section suivante vous apprendra à gérer vos repositories avec la CLI GitHub.

👉 **Continuez avec** : [Section 2 - Authentification et Configuration](../02-authentification-et-configuration/README.md)

---

*Vous avez terminé la section 1.4 ! Votre CLI GitHub est maintenant configurée et authentifiée. Vous êtes prêt à explorer toutes ses fonctionnalités dans les sections suivantes.*

## 🎉 Récapitulatif de la Section 1

Vous avez maintenant terminé toute la section "Introduction et Installation" ! Voici ce que vous avez accompli :

✅ **1.1** - Compris ce qu'est la CLI GitHub et pourquoi l'utiliser
✅ **1.2** - Appris les différences avec l'interface web et Git
✅ **1.3** - Installé la CLI GitHub sur votre système
✅ **1.4** - Configuré et authentifié votre CLI GitHub

**Vous avez maintenant une base solide** pour explorer toutes les fonctionnalités avancées de la CLI GitHub dans les sections suivantes !


⏭️
