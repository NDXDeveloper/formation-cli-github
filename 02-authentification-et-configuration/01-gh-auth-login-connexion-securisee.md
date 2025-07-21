🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 2.1 - `gh auth login` : connexion sécurisée

## 🎯 Introduction

Vous avez déjà effectué votre première connexion dans la section précédente, mais `gh auth login` est bien plus puissant que ce que vous avez vu ! Cette commande est votre porte d'entrée vers GitHub et offre de nombreuses options pour s'adapter à tous vos besoins.

Dans cette section, nous allons explorer en détail toutes les possibilités de cette commande fondamentale.

## 🔐 Comprendre `gh auth login` en profondeur

### Qu'est-ce que `gh auth login` ?

**Définition simple** : C'est la commande qui permet à votre CLI GitHub de "se présenter" à GitHub et d'obtenir l'autorisation d'agir en votre nom.

**Analogie** : Imaginez que `gh auth login` soit comme présenter votre carte d'identité et obtenir un badge d'accès dans un bâtiment sécurisé. Une fois ce badge obtenu, vous pouvez accéder aux différents étages (repositories, issues, etc.) selon vos permissions.

### Pourquoi une commande dédiée ?

GitHub doit s'assurer que :
- **Vous êtes bien qui vous prétendez être** (authentification)
- **Vous avez le droit de faire ce que vous demandez** (autorisation)
- **Vos échanges sont sécurisés** (chiffrement)

## 🔧 Anatomie de la commande

### Syntaxe de base

```bash
gh auth login [options]
```

### Vue d'ensemble des options principales

| Option | Description | Exemple |
|--------|-------------|---------|
| `--hostname` | Serveur GitHub à utiliser | `--hostname github.com` |
| `--scopes` | Permissions demandées | `--scopes "repo,workflow"` |
| `--with-token` | Utiliser un token existant | `--with-token` |
| `--web` | Forcer l'authentification web | `--web` |
| `--git-protocol` | Protocole Git (https/ssh) | `--git-protocol ssh` |

**Ne vous inquiétez pas** : Nous allons détailler chaque option avec des exemples concrets !

## 🌐 Choisir votre environnement GitHub

### GitHub.com vs GitHub Enterprise

#### GitHub.com (par défaut)
**C'est le GitHub public** que tout le monde connaît :

```bash
gh auth login
# Équivaut à :
gh auth login --hostname github.com
```

#### GitHub Enterprise
**Pour les entreprises** qui ont leur propre instance GitHub :

```bash
gh auth login --hostname votre-entreprise.github.com
```

**Comment savoir** si vous utilisez GitHub Enterprise ?
- Votre URL GitHub ressemble à : `https://github.votre-entreprise.com`
- Votre administrateur vous a donné une URL spécifique
- Vous vous connectez via le portail de votre entreprise

### Gérer plusieurs environnements

Vous pouvez être connecté à plusieurs GitHub simultanément :

```bash
# Connexion à GitHub.com
gh auth login --hostname github.com

# Connexion à votre GitHub d'entreprise
gh auth login --hostname entreprise.github.com

# Voir tous vos comptes
gh auth status
```

**Résultat** :
```
github.com
  ✓ Logged in to github.com as votre-nom

entreprise.github.com
  ✓ Logged in to entreprise.github.com as votre.nom.pro
```

## 🔑 Méthodes d'authentification détaillées

### 1. Authentification par navigateur web (Recommandée)

**C'est la méthode la plus simple et la plus sécurisée** pour débuter.

#### Connexion standard
```bash
gh auth login --web
```

**Avantages** :
- ✅ **Simple** : Interface familière
- ✅ **Sécurisé** : Authentification directe sur GitHub
- ✅ **Complet** : Gère automatiquement toutes les permissions
- ✅ **2FA** : Compatible avec l'authentification à deux facteurs

#### Processus détaillé

1. **Commande lancée** :
```bash
gh auth login --web
```

2. **Choix du service** :
```
? What account do you want to log into?
▸ GitHub.com
  GitHub Enterprise Server
```

3. **Protocole Git** :
```
? What is your preferred protocol for Git operations?
▸ HTTPS
  SSH
```

4. **Méthode d'authentification** :
```
? How would you like to authenticate GitHub CLI?
▸ Login with a web browser
  Paste an authentication token
```

5. **Code de vérification** :
```
! First copy your one-time code: ABCD-EFGH
Press Enter to open github.com in your browser...
```

6. **Confirmation** :
```
✓ Authentication complete.
✓ Configured git protocol
✓ Logged in as votre-nom
```

### 2. Authentification avec token

**Pour les cas avancés** : scripts, serveurs, ou si vous préférez contrôler les permissions.

#### Utilisation d'un token existant
```bash
gh auth login --with-token
```

Puis **collez votre token** et appuyez sur Entrée.

#### Avec un fichier token
```bash
echo "ghp_votre_token_ici" | gh auth login --with-token
```

**Avantages des tokens** :
- ✅ **Contrôle précis** des permissions
- ✅ **Pas d'interaction** (idéal pour les scripts)
- ✅ **Révocable** facilement
- ✅ **Traçable** dans les logs GitHub

### 3. Authentification SSH

**Pour les utilisateurs Git expérimentés** qui préfèrent SSH.

```bash
gh auth login --git-protocol ssh
```

**Prérequis** :
- Clé SSH générée et ajoutée à GitHub
- Agent SSH configuré

**Avantages SSH** :
- ✅ **Performance** : Authentification rapide
- ✅ **Sécurité** : Chiffrement asymétrique
- ✅ **Pas de mot de passe** à retaper

## 🎯 Gestion des permissions (Scopes)

### Qu'est-ce que les scopes ?

**Les scopes définissent ce que votre CLI peut faire** sur GitHub. C'est comme avoir différents niveaux d'accès dans un bâtiment.

**Analogie** :
- **`public_repo`** = Accès aux zones publiques
- **`repo`** = Accès aux zones privées aussi
- **`admin:org`** = Accès aux salles de direction

### Scopes courants et leur signification

| Scope | Ce qu'il permet | Quand l'utiliser |
|-------|-----------------|------------------|
| `public_repo` | Repositories publics seulement | Contribution open source |
| `repo` | Tous vos repositories | Usage général |
| `workflow` | GitHub Actions | CI/CD et automatisation |
| `write:packages` | Publier des packages | Distribution de code |
| `admin:org` | Gestion d'organisation | Administration d'équipe |
| `user` | Informations de profil | Personnalisation |
| `delete_repo` | Supprimer des repos | Administration complète |

### Demander des permissions spécifiques

```bash
# Permissions minimales pour l'open source
gh auth login --scopes "public_repo"

# Permissions standard pour développeur
gh auth login --scopes "repo,workflow,user"

# Permissions complètes pour administrateur
gh auth login --scopes "repo,workflow,admin:org,user,delete_repo"
```

**Principe de sécurité** : Demandez toujours le minimum de permissions nécessaires !

### Voir et modifier vos permissions

```bash
# Voir vos permissions actuelles
gh auth status

# Demander des permissions supplémentaires
gh auth refresh --scopes "repo,workflow,write:packages"
```

## 🔄 Gestion de sessions multiples

### Pourquoi plusieurs sessions ?

Vous pourriez avoir besoin de :
- **Compte personnel** et **compte professionnel**
- **Différentes organisations** avec permissions distinctes
- **GitHub.com** et **GitHub Enterprise** simultanément

### Connexion à plusieurs comptes

```bash
# Compte personnel sur GitHub.com
gh auth login --hostname github.com

# Compte professionnel sur GitHub Enterprise
gh auth login --hostname entreprise.github.com

# Compte dans une autre organisation
gh auth login --hostname autre-org.github.com
```

### Basculer entre les comptes

```bash
# Voir tous vos comptes
gh auth status

# Basculer vers un compte spécifique
gh auth switch --hostname entreprise.github.com

# Utiliser un compte pour une commande spécifique
gh --hostname entreprise.github.com repo list
```

### Déconnexion sélective

```bash
# Se déconnecter d'un compte spécifique
gh auth logout --hostname entreprise.github.com

# Se déconnecter de tous les comptes
gh auth logout
```

## 🛡️ Sécurité et bonnes pratiques

### Authentification sécurisée

#### 1. Utilisez l'authentification à deux facteurs (2FA)

**Activez 2FA sur votre compte GitHub** :
1. GitHub.com → Settings → Account security
2. Two-factor authentication → Enable

**La CLI GitHub fonctionne parfaitement** avec 2FA activé !

#### 2. Choisissez des permissions appropriées

```bash
# ❌ Trop de permissions
gh auth login --scopes "admin:org,delete_repo" # Si vous n'en avez pas besoin

# ✅ Permissions adaptées
gh auth login --scopes "repo,workflow" # Pour la plupart des usages
```

#### 3. Tokens avec expiration

Quand vous créez des tokens manuellement :
- ✅ **Définissez une expiration** (30-90 jours)
- ✅ **Renouvelez régulièrement**
- ✅ **Documentez l'usage** de chaque token

### Audit et monitoring

#### Vérifier vos authentifications

```bash
# Statut de toutes vos authentifications
gh auth status

# Détails avec tokens
gh auth status --show-token
```

#### Audit sur GitHub.com

1. **Settings** → **Developer settings** → **Personal access tokens**
2. Vérifiez régulièrement les tokens actifs
3. Révoquez les tokens inutilisés

### Que faire en cas de problème ?

#### Token compromis
```bash
# 1. Déconnexion immédiate
gh auth logout

# 2. Révocation sur GitHub.com
# Settings → Developer settings → Tokens → Delete

# 3. Nouvelle authentification
gh auth login
```

#### Compte compromis
1. **Changez votre mot de passe** GitHub immédiatement
2. **Révoquez tous les tokens** de la CLI
3. **Vérifiez l'historique** de vos repositories
4. **Reconnectez-vous** avec `gh auth login`

## 🔧 Options avancées de connexion

### Connexion silencieuse (non-interactive)

**Pour les scripts et l'automatisation** :

```bash
# Avec variable d'environnement
export GITHUB_TOKEN="ghp_votre_token"
gh auth login --with-token <<< "$GITHUB_TOKEN"

# Avec fichier
echo "ghp_votre_token" | gh auth login --with-token
```

### Forcer le type d'authentification

```bash
# Forcer l'authentification web même si un token existe
gh auth login --web

# Spécifier le protocole Git dès la connexion
gh auth login --git-protocol ssh

# Connexion avec hostname et scopes en une commande
gh auth login --hostname github.com --scopes "repo,workflow" --web
```

### Configuration pour CI/CD

**Pour les environnements automatisés** :

```bash
# Dans votre script CI/CD
gh auth login --with-token <<< "$GITHUB_TOKEN"
gh repo list # Test de la connexion
```

**Variables d'environnement utiles** :
- `GITHUB_TOKEN` : Token d'authentification
- `GH_HOST` : Hostname par défaut
- `GH_PROTOCOL` : Protocole Git par défaut

## 🐛 Dépannage des problèmes d'authentification

### Erreurs courantes et solutions

#### "authentication required"
```bash
# Erreur
Error: authentication required

# Solution
gh auth login
```

#### "token does not have the required scopes"
```bash
# Erreur
Error: token does not have required scope 'workflow'

# Solution : Ajouter les permissions manquantes
gh auth refresh --scopes "repo,workflow"
```

#### "certificate verification failed"
```bash
# Si vous êtes derrière un proxy d'entreprise
gh auth login --hostname votre-github-entreprise.com
```

### Diagnostic d'authentification

```bash
# Vérification complète
gh auth status --show-token

# Test de connexion
gh api user

# Vérification des permissions
gh api user/repos --paginate
```

### Reset complet de l'authentification

**En cas de problème persistant** :

```bash
# 1. Déconnexion complète
gh auth logout

# 2. Nettoyage du cache (optionnel)
rm -rf ~/.config/gh

# 3. Reconnexion propre
gh auth login
```

## 📋 Exemples de cas d'usage réels

### Développeur freelance

**Besoin** : Travailler pour plusieurs clients avec des comptes séparés.

```bash
# Client A sur GitHub.com
gh auth login --hostname github.com --scopes "repo,workflow"

# Client B sur GitHub Enterprise
gh auth login --hostname client-b.github.com --scopes "repo"

# Basculer selon le projet
gh auth switch --hostname client-b.github.com
```

### Équipe DevOps

**Besoin** : Automatisation avec permissions minimales.

```bash
# Token avec permissions strictes pour le CI
gh auth login --with-token --scopes "repo,workflow,write:packages"
```

### Contributeur open source

**Besoin** : Contribuer à des projets publics uniquement.

```bash
# Permissions limitées au public
gh auth login --scopes "public_repo"
```

### Administrateur d'organisation

**Besoin** : Gestion complète de l'organisation.

```bash
# Permissions étendues
gh auth login --scopes "repo,workflow,admin:org,user,delete_repo"
```

## 💡 Conseils d'optimisation

### Workflow efficace

1. **Commencez simple** : Utilisez `gh auth login` sans options
2. **Ajoutez des permissions** au fur et à mesure : `gh auth refresh --scopes`
3. **Séparez les contextes** : Compte personnel vs professionnel
4. **Automatisez** : Scripts avec tokens pour les tâches répétitives

### Configuration recommandée

**Pour débuter** :
```bash
gh auth login --web --scopes "repo,workflow,user"
```

**Pour une équipe** :
```bash
gh auth login --web --scopes "repo,workflow,write:packages" --git-protocol https
```

**Pour l'administration** :
```bash
gh auth login --web --scopes "repo,workflow,admin:org,user" --git-protocol ssh
```

## 📋 Récapitulatif des commandes essentielles

### Authentification de base
```bash
# Connexion simple
gh auth login

# Connexion avec options
gh auth login --web --scopes "repo,workflow"

# Connexion avec token
gh auth login --with-token
```

### Gestion multi-comptes
```bash
# Voir tous les comptes
gh auth status

# Basculer entre comptes
gh auth switch --hostname github.com

# Déconnexion sélective
gh auth logout --hostname entreprise.github.com
```

### Maintenance et sécurité
```bash
# Rafraîchir les permissions
gh auth refresh --scopes "repo,workflow,user"

# Vérifier l'authentification
gh auth status --show-token

# Reset complet
gh auth logout && gh auth login
```

## 🎯 Prochaine étape

Maintenant que vous maîtrisez parfaitement `gh auth login`, il est temps d'explorer comment personnaliser finement votre CLI GitHub avec les configurations.

👉 **Continuez avec** : [Configuration des préférences (`gh config`)](02-configuration-des-preferences-gh-config.md)

---

*Vous avez terminé la section 2.1 ! Vous maîtrisez maintenant toutes les subtilités de l'authentification GitHub. Dans la prochaine section, nous verrons comment configurer votre CLI selon vos préférences.*

⏭️
