🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 2.3 - Gestion des tokens d'accès

## 🎯 Introduction

Les tokens d'accès sont le cœur de la sécurité de votre CLI GitHub. Bien que vous ayez déjà utilisé l'authentification automatique, comprendre et maîtriser les tokens vous donnera un contrôle total sur la sécurité et les permissions de vos accès GitHub.

**Analogie** : Si l'authentification automatique est comme avoir un pass universel, les tokens sont comme avoir des cartes d'accès spécialisées pour chaque situation - plus sécurisé et plus flexible !

## 🔑 Qu'est-ce qu'un token d'accès ?

### Définition simple

Un **token d'accès personnel (PAT - Personal Access Token)** est une **clé digitale unique** qui permet à votre CLI GitHub de s'identifier auprès de GitHub et d'effectuer des actions en votre nom.

### Analogie concrète

Imaginez un hôtel avec différents types de cartes :
- **Carte client** : Accès à votre chambre seulement
- **Carte employé** : Accès aux zones de service
- **Carte manager** : Accès aux bureaux administratifs
- **Carte master** : Accès partout

Les tokens fonctionnent de la même manière : chaque token a des **permissions spécifiques** selon son usage.

### Différence avec les mots de passe

| Aspect | Mot de passe | Token d'accès |
|--------|-------------|---------------|
| **Portée** | Accès complet au compte | Permissions spécifiques |
| **Durée** | Permanent (jusqu'à changement) | Expiration configurable |
| **Révocation** | Change tout l'accès | Révocable individuellement |
| **Usage** | Interface web principalement | APIs et outils CLI |
| **Visibilité** | Caché | Partiellement visible |

## 🛡️ Pourquoi utiliser des tokens ?

### 1. **Sécurité renforcée**

**Principe du moindre privilège** : Donnez seulement les permissions nécessaires.

**Exemple concret** :
- Script de backup : Token avec permission "lecture seule"
- CI/CD : Token avec permissions "workflow" et "packages"
- Administration : Token avec permissions étendues

### 2. **Contrôle granulaire**

```bash
# Token pour contribution open source (lecture publique seulement)
Permissions : public_repo

# Token pour développement personnel (tous vos repos)
Permissions : repo, workflow, user

# Token pour administration d'organisation
Permissions : repo, workflow, admin:org, delete_repo
```

### 3. **Traçabilité**

Chaque token a :
- **Un nom descriptif** : "CI Server Production"
- **Une date de création** et d'expiration
- **Un historique d'utilisation**

### 4. **Révocation facile**

**Problème de sécurité ?** Révoquez juste le token concerné sans impacter les autres accès.

## 🔧 Types de tokens

### 1. Classic Personal Access Tokens

**Les tokens traditionnels** - simples et directs :

#### Avantages
- ✅ **Simples** à créer et utiliser
- ✅ **Compatibles** avec tous les outils
- ✅ **Contrôle** des permissions par scope

#### Inconvénients
- ⚠️ **Permissions larges** par scope
- ⚠️ **Accès** à toutes les organisations

### 2. Fine-grained Personal Access Tokens (Beta)

**Les nouveaux tokens** - contrôle ultra-précis :

#### Avantages
- ✅ **Permissions très spécifiques** par repository
- ✅ **Accès limité** aux organisations choisies
- ✅ **Durée de vie** plus courte par défaut

#### Inconvénients
- ⚠️ **Encore en beta** (peut changer)
- ⚠️ **Plus complexe** à configurer

**Pour débuter** : Utilisez les Classic PAT, plus stables et simples.

## 🎨 Créer votre premier token

### Étape 1 : Accéder à la gestion des tokens

1. **Allez sur [github.com](https://github.com)**
2. **Cliquez sur votre photo** de profil (en haut à droite)
3. **Sélectionnez "Settings"**
4. **Dans le menu de gauche** : "Developer settings" (tout en bas)
5. **Cliquez sur** "Personal access tokens"
6. **Sélectionnez** "Tokens (classic)"

### Étape 2 : Générer un nouveau token

1. **Cliquez sur** "Generate new token" → "Generate new token (classic)"
2. **Autorisez avec votre mot de passe** ou 2FA si demandé

### Étape 3 : Configurer le token

#### Nom du token (Note)
**Soyez descriptif !** Exemples :
- ✅ "CLI GitHub - PC Personnel"
- ✅ "Script Backup Repos"
- ✅ "CI/CD Server Production"
- ❌ "Mon token" (trop vague)

#### Expiration
**Recommandations par usage** :
- **Développement local** : 90 jours (équilibre sécurité/praticité)
- **Scripts personnels** : 1 an
- **CI/CD** : Pas d'expiration (mais surveillance renforcée)
- **Tests/expérimentation** : 30 jours

#### Sélection des permissions (Scopes)

**Voici les scopes les plus courants** :

##### Permissions de base
```
✅ repo                    # Accès complet aux repositories privés
✅ public_repo             # Accès aux repositories publics seulement
✅ repo:status             # Statut des commits seulement
✅ repo_deployment         # Déploiements seulement
```

##### Permissions workflow
```
✅ workflow                # GitHub Actions
✅ write:packages          # Publier des packages
✅ read:packages           # Lire des packages
✅ delete:packages         # Supprimer des packages
```

##### Permissions utilisateur
```
✅ user                    # Accès au profil utilisateur
✅ user:email              # Adresses email seulement
✅ user:follow             # Suivre/ne plus suivre
```

##### Permissions organisation
```
✅ admin:org               # Administration complète
✅ write:org               # Écriture dans l'organisation
✅ read:org                # Lecture de l'organisation
```

### Étape 4 : Sauvegarder le token

1. **Cliquez sur** "Generate token"
2. **Copiez IMMÉDIATEMENT** le token affiché
3. **Sauvegardez-le** dans un gestionnaire de mots de passe

⚠️ **ATTENTION** : Vous ne pourrez plus jamais revoir ce token ! Si vous le perdez, il faudra en créer un nouveau.

## 🔧 Utiliser un token avec la CLI

### Méthode 1 : Authentification directe

```bash
gh auth login --with-token
```

Puis **collez votre token** et appuyez sur Entrée.

### Méthode 2 : Variable d'environnement

```bash
# Définir la variable d'environnement
export GITHUB_TOKEN="ghp_votre_token_ici"

# La CLI utilisera automatiquement cette variable
gh repo list
```

### Méthode 3 : Fichier token (temporaire)

```bash
# Créer un fichier temporaire avec le token
echo "ghp_votre_token_ici" > token.txt

# Utiliser le fichier pour l'authentification
gh auth login --with-token < token.txt

# Supprimer le fichier (sécurité)
rm token.txt
```

### Méthode 4 : Script sécurisé

```bash
#!/bin/bash
# auth-script.sh

# Demander le token de manière sécurisée
echo -n "Token GitHub: "
read -s GITHUB_TOKEN
echo

# Authentifier
echo "$GITHUB_TOKEN" | gh auth login --with-token

# Nettoyer la variable
unset GITHUB_TOKEN

echo "Authentification terminée !"
```

## 🎯 Tokens par cas d'usage

### Développeur personnel

**Besoins** : Accès à vos repos personnels, création d'issues, PRs.

**Permissions recommandées** :
```
✅ repo           # Tous vos repositories
✅ workflow       # GitHub Actions
✅ user           # Votre profil
✅ delete_repo    # Supprimer vos repos si besoin
```

**Expiration** : 90 jours

### Contributeur open source

**Besoins** : Contribuer à des projets publics, forker, créer des PRs.

**Permissions recommandées** :
```
✅ public_repo    # Repos publics seulement
✅ user:email     # Pour les commits
```

**Expiration** : 1 an (moins de risque avec permissions limitées)

### Scripts d'automatisation

**Besoins** : Backup, synchronisation, rapports automatiques.

**Permissions recommandées** :
```
✅ repo:status    # Lire le statut
✅ repo           # Si backup complet nécessaire
✅ read:org       # Informations organisation
```

**Expiration** : 1 an (avec rotation planifiée)

### CI/CD et déploiement

**Besoins** : Tests automatiques, déploiement, gestion des packages.

**Permissions recommandées** :
```
✅ repo                # Code source
✅ workflow            # GitHub Actions
✅ write:packages      # Publier des packages
✅ repo:status         # Statuts de build
```

**Expiration** : Pas d'expiration (mais surveillance)

### Administration d'équipe

**Besoins** : Gestion des membres, repositories, permissions.

**Permissions recommandées** :
```
✅ repo                # Tous les repos
✅ workflow            # GitHub Actions
✅ admin:org           # Administration
✅ user                # Profils utilisateurs
✅ delete_repo         # Suppression si nécessaire
```

**Expiration** : 30-60 jours (rotation fréquente pour admin)

## 🔄 Gestion du cycle de vie des tokens

### Surveillance des tokens

#### Via GitHub.com
1. **Settings** → **Developer settings** → **Personal access tokens**
2. **Vérifiez régulièrement** :
   - Tokens inutilisés (dernière utilisation)
   - Tokens expirés
   - Tokens avec trop de permissions

#### Via CLI
```bash
# Voir vos tokens actifs (partiellement)
gh auth status --show-token

# Tester un token
gh api user
```

### Rotation des tokens

**Bonnes pratiques de rotation** :

#### Rotation planifiée
```bash
#!/bin/bash
# rotate-token.sh

echo "🔄 Rotation du token GitHub"

# 1. Créer un nouveau token sur GitHub.com
echo "1. Créez un nouveau token sur GitHub avec les mêmes permissions"
echo "2. Copiez le nouveau token"

# 2. Tester le nouveau token
echo -n "Nouveau token: "
read -s NEW_TOKEN
echo

echo "$NEW_TOKEN" | gh auth login --with-token

# 3. Tester
if gh api user > /dev/null 2>&1; then
    echo "✅ Nouveau token fonctionnel"
    echo "4. Révoquez l'ancien token sur GitHub.com"
else
    echo "❌ Problème avec le nouveau token"
fi
```

#### Rotation d'urgence
```bash
# En cas de compromission
gh auth logout                    # Déconnexion immédiate
# Révoquer sur GitHub.com        # Via l'interface web
gh auth login --with-token       # Nouveau token
```

### Révocation des tokens

#### Quand révoquer ?
- ✅ **Token compromis** ou suspecté
- ✅ **Token inutilisé** depuis longtemps
- ✅ **Changement de poste** ou d'équipe
- ✅ **Projet terminé**

#### Comment révoquer ?
1. **GitHub.com** → **Settings** → **Developer settings** → **Personal access tokens**
2. **Trouvez le token** dans la liste
3. **Cliquez sur "Delete"**
4. **Confirmez** la suppression

## 🛡️ Sécurité des tokens

### Stockage sécurisé

#### ✅ Bonnes pratiques
```bash
# Gestionnaire de mots de passe
# - 1Password, Bitwarden, LastPass, etc.

# Variables d'environnement (temporaire)
export GITHUB_TOKEN="ghp_xxx"

# Fichiers avec permissions restreintes
echo "ghp_xxx" > ~/.github_token
chmod 600 ~/.github_token
```

#### ❌ À éviter absolument
```bash
# Dans le code source
const token = "ghp_xxx";  // ❌ JAMAIS !

# Dans les fichiers de configuration versionnés
# .env, config.yml, etc. commités sur Git

# Dans les logs
echo "Token: ghp_xxx" > logfile.txt  // ❌

# En plain text sans protection
echo "ghp_xxx" > token.txt  // ❌
```

### Détection des fuites

#### GitHub scanning
GitHub **scanne automatiquement** :
- Repositories publics et privés
- Commits, issues, PRs
- Logs de CI/CD

**Si un token est détecté**, GitHub :
1. **Révoque automatiquement** le token
2. **Vous notifie** par email
3. **Crée une alerte** de sécurité

#### Outils de détection
```bash
# TruffleHog - Scanner de secrets
pip install trufflehog
trufflehog --regex --entropy=False .

# git-secrets - Prévention
git secrets --install
git secrets --register-aws
```

### Permissions minimales

**Toujours appliquer le principe de moindre privilège** :

```bash
# ❌ Trop de permissions
repo, workflow, admin:org, delete_repo, user
# Pour un simple script de lecture

# ✅ Permissions appropriées
public_repo, user:email
# Pour le même script
```

## 🔧 Automatisation avec tokens

### Scripts de sauvegarde

```bash
#!/bin/bash
# backup-repos.sh

# Token avec permissions 'repo' et 'user'
GITHUB_TOKEN="${GITHUB_TOKEN}"

if [ -z "$GITHUB_TOKEN" ]; then
    echo "❌ Variable GITHUB_TOKEN requise"
    exit 1
fi

# Authentification
echo "$GITHUB_TOKEN" | gh auth login --with-token

# Sauvegarde
mkdir -p backup-$(date +%Y%m%d)
cd backup-$(date +%Y%m%d)

# Cloner tous vos repos
gh repo list --json name,sshUrl | jq -r '.[].sshUrl' | while read repo; do
    echo "Backup de $repo"
    git clone "$repo"
done

echo "✅ Sauvegarde terminée"
```

### Monitoring automatique

```bash
#!/bin/bash
# check-repos.sh

# Token avec permissions minimales
export GITHUB_TOKEN="ghp_readonly_token"

# Vérifier les repos avec issues ouvertes
echo "📊 Repositories avec issues ouvertes:"
gh search repos --owner="@me" --json name | \
    jq -r '.[].name' | \
    while read repo; do
        count=$(gh issue list --repo="$repo" --state=open --json number | jq length)
        if [ "$count" -gt 0 ]; then
            echo "  $repo: $count issues"
        fi
    done
```

### CI/CD avec tokens

**Exemple GitHub Actions** :

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup GitHub CLI
        run: |
          echo "${{ secrets.DEPLOY_TOKEN }}" | gh auth login --with-token

      - name: Create release
        run: |
          gh release create v1.0.0 --generate-notes
```

## 🔍 Audit et conformité

### Audit régulier

**Script d'audit mensuel** :

```bash
#!/bin/bash
# audit-tokens.sh

echo "🔍 Audit des tokens GitHub - $(date)"
echo "================================="

# 1. Lister tous les tokens via l'interface web
echo "1. Vérifiez manuellement sur GitHub.com:"
echo "   Settings → Developer settings → Personal access tokens"
echo ""

# 2. Vérifier les tokens actifs
echo "2. Tokens actifs dans la CLI:"
gh auth status
echo ""

# 3. Tester les permissions
echo "3. Test des permissions actuelles:"
echo -n "  Accès utilisateur: "
gh api user --jq .login && echo "✅" || echo "❌"

echo -n "  Accès repos: "
gh repo list --limit 1 >/dev/null 2>&1 && echo "✅" || echo "❌"

echo -n "  Accès workflow: "
gh api repos/:owner/:repo/actions/runs --jq .total_count >/dev/null 2>&1 && echo "✅" || echo "❌"

echo ""
echo "4. Actions recommandées:"
echo "   - Révoquez les tokens inutilisés"
echo "   - Vérifiez les dates d'expiration"
echo "   - Réduisez les permissions si possible"
```

### Documentation des tokens

**Maintenez un inventaire** :

```markdown
# Inventaire des tokens GitHub

| Nom | Usage | Permissions | Expiration | Statut |
|-----|-------|-------------|------------|--------|
| CLI-PC-Personnel | Développement local | repo, workflow, user | 2024-03-15 | ✅ Actif |
| CI-Production | Déploiement auto | repo, workflow, packages | Aucune | ✅ Actif |
| Script-Backup | Sauvegarde quotidienne | repo (lecture) | 2024-06-01 | ✅ Actif |
| Test-Temporaire | Tests API | public_repo | 2024-01-15 | ❌ Expiré |
```

## 🚨 Gestion des incidents

### Token compromis

**Actions immédiates** :

1. **Révocation** :
   ```bash
   # Déconnexion CLI
   gh auth logout

   # Révocation sur GitHub.com
   # Settings → Developer settings → Tokens → Delete
   ```

2. **Évaluation** :
   - Quelles données étaient accessibles ?
   - Quelles actions ont pu être effectuées ?
   - Y a-t-il eu des activités suspectes ?

3. **Nouveau token** :
   ```bash
   # Créer un nouveau token avec permissions minimales
   gh auth login --with-token
   ```

4. **Surveillance** :
   - Vérifiez les logs GitHub
   - Surveillez les repositories pour des changements
   - Alertez l'équipe si nécessaire

### Perte de token

**Si vous perdez un token** :

```bash
# 1. Tentez de vous reconnecter
gh auth status

# 2. Si échec, nouvelle authentification
gh auth login

# 3. Révoquez l'ancien token sur GitHub.com
# pour éviter qu'il traîne
```

## 📋 Récapitulatif des bonnes pratiques

### Création de tokens
- ✅ **Noms descriptifs** et explicites
- ✅ **Permissions minimales** nécessaires
- ✅ **Expiration appropriée** au contexte
- ✅ **Documentation** de l'usage

### Utilisation
- ✅ **Variables d'environnement** pour les scripts
- ✅ **Gestionnaire de mots de passe** pour le stockage
- ✅ **Jamais dans le code source** ou logs
- ✅ **Test régulier** du fonctionnement

### Maintenance
- ✅ **Audit mensuel** des tokens actifs
- ✅ **Rotation planifiée** des tokens critiques
- ✅ **Révocation** des tokens inutilisés
- ✅ **Surveillance** des alertes de sécurité

### Sécurité
- ✅ **2FA activé** sur le compte GitHub
- ✅ **Principe de moindre privilège**
- ✅ **Monitoring** des activités
- ✅ **Plan de réponse** aux incidents

## 🎯 Prochaine étape

Vous maîtrisez maintenant la gestion complète des tokens d'accès ! Il est temps de voir comment optimiser l'intégration avec votre éditeur de code préféré.

👉 **Continuez avec** : [Configuration de l'éditeur par défaut](04-configuration-de-lediteur-par-defaut.md)

---

*Vous avez terminé la section 2.3 ! Vous savez maintenant créer, gérer et sécuriser vos tokens d'accès GitHub comme un expert. Dans la prochaine section, nous optimiserons l'intégration avec votre éditeur.*

⏭️
