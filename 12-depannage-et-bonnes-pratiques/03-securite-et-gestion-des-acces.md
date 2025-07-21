🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 12.3 Sécurité et gestion des accès

## Introduction

La sécurité est un aspect crucial de l'utilisation de la CLI GitHub, car elle vous donne accès à vos repositories, issues, PRs et potentiellement à des informations sensibles de votre organisation. Cette section vous guide dans la mise en place de bonnes pratiques de sécurité pour protéger vos accès et données.

## Comprendre les modèles d'authentification

### Types d'authentification GitHub CLI

**Authentification OAuth (recommandée) :**
- Utilise le navigateur web pour l'authentification
- Génère automatiquement des tokens sécurisés
- Supporte l'authentification à deux facteurs (2FA)
- Permissions granulaires

**Tokens d'accès personnel (PAT) :**
- Créés manuellement dans les paramètres GitHub
- Contrôle total sur les permissions (scopes)
- Idéaux pour l'automatisation et les scripts
- Nécessitent une gestion manuelle

**Authentification SSH :**
- Utilise les clés SSH existantes
- Principalement pour les opérations Git
- Sécurité élevée avec des clés bien gérées

### Vérifier votre authentification actuelle

```bash
# Vérifier le statut d'authentification
gh auth status

# Voir les détails complets
gh auth status --show-token

# Vérifier les permissions du token
gh api user --jq '.permissions'
```

## Authentification sécurisée

### Méthode recommandée : OAuth web

```bash
# Authentification la plus sécurisée
gh auth login --web

# Avec des scopes spécifiques
gh auth login --web --scopes "repo,read:org"
```

**Avantages de l'authentification web :**
- ✅ Support natif du 2FA
- ✅ Interface utilisateur familière
- ✅ Permissions clairement affichées
- ✅ Révocation facile depuis GitHub

### Création de tokens d'accès personnel sécurisés

Si vous devez utiliser des PAT, suivez ces bonnes pratiques :

**Étapes pour créer un PAT sécurisé :**
1. Aller dans GitHub → Settings → Developer settings → Personal access tokens
2. Choisir "Fine-grained tokens" (plus sécurisé) ou "Tokens (classic)"
3. Définir une expiration courte (30-90 jours maximum)
4. Sélectionner uniquement les permissions nécessaires
5. Limiter l'accès aux repositories spécifiques si possible

**Utilisation du token :**
```bash
# Authentification avec token (méthode sécurisée)
echo "your_token_here" | gh auth login --with-token

# Éviter absolument cette méthode (non sécurisée)
# gh auth login --with-token your_token_here
```

## Gestion des permissions et scopes

### Comprendre les scopes essentiels

**Scopes de base :**
- `repo` : Accès complet aux repositories privés
- `public_repo` : Accès aux repositories publics uniquement
- `read:org` : Lire les informations d'organisation
- `user` : Accès aux informations de profil

**Scopes avancés :**
- `admin:org` : Administration d'organisation
- `delete_repo` : Suppression de repositories
- `workflow` : Gestion des GitHub Actions

### Principe du moindre privilège

```bash
# Exemple : Pour un script qui lit seulement les issues publiques
gh auth login --scopes "public_repo,read:user"

# Exemple : Pour l'administration d'une organisation
gh auth login --scopes "repo,admin:org,read:user"

# Vérifier les scopes accordés
gh auth status
```

### Audit des permissions

```bash
# Vérifier les permissions actuelles
gh api user --jq '.permissions'

# Lister les repositories accessibles
gh repo list --limit 100

# Vérifier l'accès à une organisation
gh api orgs/your-org/members/your-username
```

## Sécurisation des tokens

### Stockage sécurisé

**Éviter absolument :**
- ❌ Tokens en dur dans les scripts
- ❌ Tokens dans les variables d'environnement globales
- ❌ Tokens dans les fichiers de configuration versionnés
- ❌ Partage de tokens par email/chat

**Bonnes pratiques :**
```bash
# Utiliser des fichiers protégés
echo "your_token" > ~/.github_token
chmod 600 ~/.github_token

# Dans les scripts, lire depuis un fichier sécurisé
GITHUB_TOKEN=$(cat ~/.github_token)
export GITHUB_TOKEN

# Ou utiliser des gestionnaires de secrets
# macOS Keychain, Windows Credential Manager, Linux Keyring
```

### Variables d'environnement sécurisées

```bash
# Dans votre .bashrc/.zshrc (pour usage local uniquement)
export GITHUB_TOKEN=$(security find-generic-password -s github_token -w 2>/dev/null)

# Pour les scripts temporaires
read -s -p "GitHub Token: " GITHUB_TOKEN
export GITHUB_TOKEN
```

### Rotation des tokens

```bash
# Script de rotation automatique des tokens
#!/bin/bash
rotate_github_token() {
    echo "🔄 Rotation du token GitHub"

    # Déconnexion actuelle
    gh auth logout

    # Nouvelle authentification
    gh auth login --web

    # Vérification
    if gh auth status; then
        echo "✅ Token rotated successfully"
    else
        echo "❌ Token rotation failed"
    fi
}
```

## Sécurité des accès multi-comptes

### Isolation des comptes

```bash
# Configurer des profils séparés
# Compte personnel
gh auth login --hostname github.com --username personal-account

# Compte professionnel
gh auth login --hostname github.com --username work-account

# Basculer entre les comptes
gh auth switch --hostname github.com --user personal-account
gh auth switch --hostname github.com --user work-account
```

### Configuration par projet

```bash
# Dans un projet spécifique
cd /path/to/work/project

# Utiliser un token spécifique pour ce projet
export GITHUB_TOKEN=$(cat ~/.tokens/work-token)

# Ou créer un script d'environnement
cat > .env << 'EOF'
#!/bin/bash
export GITHUB_TOKEN=$(cat ~/.tokens/work-token)
export GH_HOST=github.com
echo "🏢 Environment work configured"
EOF

source .env
```

## Sécurité des extensions

### Évaluation de la sécurité des extensions

Avant d'installer une extension, vérifiez :

```bash
# Vérifier la popularité et la maintenance
gh extension browse

# Examiner le code source
gh repo view owner/gh-extension-name

# Vérifier l'auteur et l'organisation
gh api repos/owner/gh-extension-name --jq '.owner.login'

# Voir les permissions demandées (si documentées)
gh repo view owner/gh-extension-name --web
```

**Critères de sécurité :**
- ✅ Auteur reconnu ou organisation officielle
- ✅ Code source ouvert et auditable
- ✅ Maintenance active (commits récents)
- ✅ Documentation claire des permissions
- ✅ Issues de sécurité traitées rapidement

### Installation sécurisée d'extensions

```bash
# Installer uniquement des extensions de confiance
gh extension install dlvhdr/gh-dash  # Auteur reconnu

# Éviter les extensions avec des warning de sécurité
# Vérifier les issues GitHub de l'extension

# Audit périodique des extensions
gh extension list
```

### Nettoyage des extensions

```bash
# Supprimer les extensions non utilisées
gh extension remove unused-extension

# Audit complet
gh extension list | while read ext; do
    echo "Extension: $ext"
    echo "Last used: ?" # Documenter votre usage
done
```

## Sécurité réseau et communications

### Configuration HTTPS vs SSH

**HTTPS avec authentification :**
```bash
# Configuration sécurisée HTTPS
gh config set git_protocol https

# Vérifier les certificats SSL
curl -I https://api.github.com
```

**SSH (recommandé pour la sécurité) :**
```bash
# Configuration SSH sécurisée
gh config set git_protocol ssh

# Vérifier la configuration SSH
ssh -T git@github.com

# Configuration SSH sécurisée (~/.ssh/config)
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_github
    IdentitiesOnly yes
    AddKeysToAgent yes
    UseKeychain yes  # macOS seulement
```

### Protection contre les attaques réseau

```bash
# Vérifier les certificats SSL
openssl s_client -connect api.github.com:443 -servername api.github.com

# Utiliser des DNS sécurisés
# Configuration selon votre OS pour utiliser 1.1.1.1 ou 8.8.8.8

# Éviter les réseaux non sécurisés pour les opérations sensibles
```

## Audit et monitoring de sécurité

### Surveillance des accès

```bash
# Script d'audit de sécurité
#!/bin/bash
echo "🔍 Audit de sécurité GitHub CLI"
echo "==============================="

echo "📊 Informations d'authentification :"
gh auth status

echo ""
echo "🔑 Tokens actifs :"
gh api user/tokens 2>/dev/null || echo "Non accessible via API"

echo ""
echo "🔌 Extensions installées :"
gh extension list

echo ""
echo "⚙️ Configuration actuelle :"
gh config list

echo ""
echo "🌐 Test de connectivité sécurisée :"
curl -I https://api.github.com | head -n 1
```

### Logs de sécurité

```bash
# Activer les logs pour audit
export GH_DEBUG=1

# Rediriger les logs vers un fichier sécurisé
gh command 2>> ~/.logs/gh-security.log

# Protéger les logs
chmod 600 ~/.logs/gh-security.log
```

### Détection d'activité suspecte

```bash
# Vérifier les connexions récentes (sur GitHub web)
# Settings → Security → Sign-in log

# Script de vérification automatique
check_suspicious_activity() {
    # Vérifier les ratios d'API inhabituels
    CURRENT_RATE=$(gh api rate_limit --jq '.rate.remaining')
    if [ "$CURRENT_RATE" -lt 100 ]; then
        echo "⚠️ Warning: Low API rate limit remaining"
    fi

    # Vérifier l'accès aux repositories critiques
    gh repo view critical-repo > /dev/null 2>&1 || echo "❌ Cannot access critical repo"
}
```

## Sécurité en environnement CI/CD

### Configuration sécurisée pour l'intégration continue

```bash
# Dans les pipelines CI/CD
# Utiliser des secrets d'environnement, jamais de tokens en dur

# GitHub Actions example
env:
  GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

# Configuration pour CI
export GH_PROMPT_DISABLED=1
export NO_COLOR=1

# Limiter les permissions au strict nécessaire
# Utiliser des tokens avec scopes minimal pour CI
```

### Bonnes pratiques CI/CD

```bash
# Script sécurisé pour CI
#!/bin/bash
set -euo pipefail  # Strict error handling

# Vérifier que le token est présent
if [ -z "${GITHUB_TOKEN:-}" ]; then
    echo "❌ GITHUB_TOKEN not set"
    exit 1
fi

# Vérifier la validité du token
if ! gh auth status; then
    echo "❌ Invalid GitHub token"
    exit 1
fi

# Effectuer les opérations avec permissions minimales
gh repo list --limit 1 > /dev/null || exit 1
```

## Incident response et récupération

### En cas de compromission suspectée

**Actions immédiates :**
```bash
# 1. Déconnecter tous les accès
gh auth logout

# 2. Révoquer les tokens depuis GitHub web
# Settings → Developer settings → Personal access tokens

# 3. Changer les mots de passe GitHub
# Settings → Password and authentication

# 4. Vérifier l'activité récente
# Settings → Security log
```

### Procédure de récupération

```bash
# 1. Nettoyer la configuration locale
rm -rf ~/.config/gh/
rm -rf ~/.cache/gh/

# 2. Réauthentification sécurisée
gh auth login --web --scopes "repo,read:user"

# 3. Audit complet
gh auth status
gh config list
gh extension list

# 4. Reconfiguration sécurisée
gh config set git_protocol ssh
```

### Documentation d'incident

```bash
# Template de rapport d'incident
cat > incident-report.md << 'EOF'
# Incident de sécurité GitHub CLI

## Date et heure
- Détection :
- Résolution :

## Description
- Symptômes observés :
- Cause suspectée :

## Actions prises
- [ ] Déconnexion des accès
- [ ] Révocation des tokens
- [ ] Audit des activités
- [ ] Reconfiguration

## Prévention future
- Mesures à mettre en place :
- Formation nécessaire :
EOF
```

## Bonnes pratiques de sécurité au quotidien

### Check-list de sécurité quotidienne

**Avant de commencer le travail :**
- ✅ Vérifier l'authentification : `gh auth status`
- ✅ Contrôler l'environnement réseau (éviter WiFi public)
- ✅ Vérifier les extensions installées

**Pendant le travail :**
- ✅ Utiliser des permissions minimales
- ✅ Ne jamais partager de tokens
- ✅ Éviter les commandes avec données sensibles dans l'historique

**Fin de session :**
- ✅ Nettoyer les variables d'environnement sensibles
- ✅ Fermer les sessions sur machines partagées
- ✅ Vérifier l'activité dans GitHub web

### Formation et sensibilisation

**Pour vous-même :**
- Restez informé des alertes de sécurité GitHub
- Participez aux formations de sécurité
- Lisez les bonnes pratiques régulièrement

**Pour votre équipe :**
- Partagez les procédures de sécurité
- Organisez des sessions de formation
- Créez des guides internes
- Établissez des protocoles d'incident

## Configuration sécurisée d'équipe

### Standards de sécurité d'équipe

```bash
# Script de configuration sécurisée pour l'équipe
#!/bin/bash
echo "🔒 Configuration sécurisée GitHub CLI - Équipe"

# Configuration de base sécurisée
gh config set git_protocol ssh
gh config set prompt enabled

# Alias sécurisés (sans données sensibles)
gh alias set secure-status "auth status"
gh alias set audit-config "config list"

# Extensions approuvées seulement
echo "📝 Extensions approuvées :"
echo "- dlvhdr/gh-dash"
echo "- mislav/gh-branch"
echo "Demander approbation pour d'autres extensions"

echo "✅ Configuration terminée"
echo "📖 Voir le guide de sécurité interne pour plus de détails"
```

---

**🎯 Points clés à retenir :**
- Utilisez toujours l'authentification web avec 2FA quand possible
- Appliquez le principe du moindre privilège pour les tokens
- Auditez régulièrement vos accès et extensions
- Protégez vos tokens comme des mots de passe
- Documentez les incidents et partagez les bonnes pratiques

**🔒 Règle d'or de sécurité :** En cas de doute sur la sécurité d'un token ou d'un accès, révoque immédiatement et reconfigure proprement.

**➡️ Prochaine étape :** Dans la section 12.4, nous explorerons les conseils pour les équipes et l'adoption à grande échelle de la CLI GitHub.

⏭️
