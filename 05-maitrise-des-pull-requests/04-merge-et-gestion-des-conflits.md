🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 5.4 : Merge et gestion des conflits (`gh pr merge`)

## Introduction

Le merge (fusion) d'une Pull Request est l'étape finale du processus de développement collaboratif. C'est le moment où les modifications proposées sont officiellement intégrées dans la branche principale du projet. Cette étape cruciale nécessite une compréhension des différentes stratégies de merge et de la gestion des conflits qui peuvent survenir.

GitHub CLI simplifie ce processus en permettant de merger des Pull Requests directement depuis le terminal, tout en offrant des options pour gérer les situations complexes.

## Comprendre le merge

### Qu'est-ce qu'un merge ?

Un merge consiste à intégrer les modifications d'une branche (celle de la PR) dans une autre branche (généralement `main` ou `master`). Cette opération combine l'historique des deux branches.

### Prérequis pour merger

Avant de pouvoir merger une PR, plusieurs conditions doivent être remplies :

- **Reviews approuvées** : La PR doit avoir reçu les approbations nécessaires
- **Checks passés** : Tous les tests automatiques (CI/CD) doivent être verts
- **Pas de conflits** : Aucun conflit de merge ne doit exister
- **Permissions** : Vous devez avoir les droits de merge sur le repository

### Vérifier l'état d'une PR avant le merge

Avant de merger, examinez toujours l'état de la PR :

```bash
gh pr view 142
```

Cette commande affiche :
- Le statut des reviews (approved, changes requested)
- L'état des checks (passing, failing)
- La présence éventuelle de conflits
- Les informations générales sur la PR

## La commande `gh pr merge`

### Syntaxe de base

```bash
gh pr merge [numéro-PR] [options]
```

Pour merger la PR numéro 142 :
```bash
gh pr merge 142
```

Si vous êtes sur la branche de la PR, vous pouvez omettre le numéro :
```bash
gh pr merge
```

### Mode interactif

Sans options spécifiques, la commande lance un mode interactif :

```bash
gh pr merge 142
```

Ce mode vous proposera :
1. De choisir la stratégie de merge
2. De confirmer votre choix
3. D'ajouter un message de commit personnalisé si nécessaire

## Stratégies de merge

GitHub propose trois stratégies principales de merge, chacune avec ses avantages selon le contexte.

### 1. Create a merge commit

```bash
gh pr merge 142 --merge
```

**Caractéristiques :**
- Crée un commit de merge spécifique
- Préserve l'historique complet des deux branches
- Maintient la traçabilité de la PR dans l'historique

**Quand l'utiliser :**
- Pour les features importantes où vous voulez garder l'historique détaillé
- Quand l'équipe privilégie la traçabilité complète
- Pour les projets avec des branches de longue durée

**Exemple d'historique résultant :**
```
* Merge pull request #142 from feature/dark-mode
|\
| * Add dark mode toggle
| * Update CSS for dark theme
| * Add dark mode tests
|/
* Previous commit on main
```

### 2. Squash and merge

```bash
gh pr merge 142 --squash
```

**Caractéristiques :**
- Combine tous les commits de la PR en un seul
- Crée un historique linéaire et propre
- Le message du commit peut être personnalisé

**Quand l'utiliser :**
- Pour maintenir un historique Git propre
- Quand les commits individuels de la PR ne sont pas significatifs
- Pour les projets privilégiant la simplicité de l'historique

**Exemple d'historique résultant :**
```
* feat: Add dark mode support (#142)
* Previous commit on main
```

### 3. Rebase and merge

```bash
gh pr merge 142 --rebase
```

**Caractéristiques :**
- Rejoue les commits de la PR sur la branche cible
- Crée un historique linéaire sans commit de merge
- Préserve les commits individuels

**Quand l'utiliser :**
- Pour un historique parfaitement linéaire
- Quand chaque commit de la PR a de la valeur
- Dans les projets avec une politique "no merge commits"

**Exemple d'historique résultant :**
```
* Add dark mode tests
* Update CSS for dark theme
* Add dark mode toggle
* Previous commit on main
```

## Gestion des conflits

### Qu'est-ce qu'un conflit de merge ?

Un conflit survient quand deux branches ont modifié les mêmes lignes d'un fichier de manières différentes. Git ne peut pas décider automatiquement quelle version conserver.

### Détecter les conflits

Avant de tenter un merge, vérifiez s'il y a des conflits :

```bash
gh pr view 142
```

Si des conflits existent, vous verrez un message comme :
```
This branch has conflicts that must be resolved
```

### Résoudre les conflits

GitHub CLI ne peut pas résoudre automatiquement les conflits, mais elle peut vous guider vers les solutions :

#### Méthode 1 : Résolution via le navigateur

```bash
gh pr view 142 --web
```

Cette commande ouvre la PR dans le navigateur où GitHub propose parfois une interface de résolution de conflits.

#### Méthode 2 : Résolution locale

Pour résoudre les conflits localement :

1. **Récupérer la branche de la PR :**
```bash
gh pr checkout 142
```

2. **Mettre à jour avec la branche cible :**
```bash
git fetch origin main
git merge origin/main
```

3. **Résoudre manuellement les conflits** dans votre éditeur de code

4. **Marquer les conflits comme résolus :**
```bash
git add .
git commit -m "Resolve merge conflicts"
```

5. **Pousser les modifications :**
```bash
git push
```

6. **Maintenant vous pouvez merger :**
```bash
gh pr merge 142
```

### Prévenir les conflits

#### Maintenir sa branche à jour

Régulièrement, mettez à jour votre branche de feature :

```bash
# Sur votre branche de feature
git fetch origin main
git merge origin/main
```

#### Communiquer avec l'équipe

Coordonnez-vous avec votre équipe pour éviter de travailler sur les mêmes fichiers simultanément.

## Options avancées de merge

### Personnaliser le message de commit

Pour les merges avec squash, vous pouvez spécifier un message personnalisé :

```bash
gh pr merge 142 --squash --subject "feat: implement user authentication" --body "
- Add login/logout functionality
- Implement JWT token management
- Add password hashing
- Update documentation

Closes #123
Resolves #124
"
```

### Suppression automatique de la branche

Après le merge, supprimez automatiquement la branche source :

```bash
gh pr merge 142 --squash --delete-branch
```

Cette option est particulièrement utile pour maintenir un repository propre.

### Merge administrateur

Si vous avez les permissions d'administrateur, vous pouvez forcer un merge même si certaines conditions ne sont pas remplies :

```bash
gh pr merge 142 --admin
```

**⚠️ Attention :** Utilisez cette option avec parcimonie et seulement en cas d'urgence.

## Workflows de merge courants

### 1. Merge simple et rapide

Pour une PR sans conflits et approuvée :

```bash
# Vérifier l'état
gh pr view 142

# Merger avec squash (recommandé pour la plupart des cas)
gh pr merge 142 --squash --delete-branch
```

### 2. Merge après résolution de conflits

```bash
# Détecter les conflits
gh pr view 142

# Résoudre localement
gh pr checkout 142
git merge origin/main
# ... résoudre les conflits ...
git add .
git commit -m "Resolve conflicts"
git push

# Merger
gh pr merge 142 --squash --delete-branch
```

### 3. Merge de feature importante

Pour une grosse feature où l'historique est important :

```bash
gh pr merge 142 --merge --body "
Merge feature: Advanced user management

This major feature includes:
- Role-based permissions
- User groups management
- Audit logging
- Enhanced security

Reviewed by: @alice @bob
Tested on: staging environment
"
```

## Après le merge

### Vérification post-merge

Après un merge, vérifiez que tout fonctionne :

1. **Mettre à jour votre branche locale :**
```bash
git checkout main
git pull origin main
```

2. **Vérifier que les modifications sont présentes :**
```bash
git log --oneline -5
```

3. **Tester localement** si nécessaire

### Nettoyage

Supprimez les branches locales devenues inutiles :

```bash
git branch -d feature/ma-feature
```

### Suivi des déploiements

Si votre projet a des déploiements automatiques, surveillez que le merge n'a pas cassé la production.

## Cas d'erreurs et solutions

### Erreur : PR not ready for merge

**Problème :** Les conditions de merge ne sont pas remplies.

**Solutions :**
- Attendre l'approbation des reviews
- Corriger les tests qui échouent
- Résoudre les conflits

### Erreur : Insufficient permissions

**Problème :** Vous n'avez pas les droits de merge.

**Solutions :**
- Demander à un maintainer de merger
- Vérifier vos permissions sur le repository

### Erreur : Branch is out of date

**Problème :** La branche principale a été mise à jour depuis la création de la PR.

**Solutions :**
```bash
gh pr checkout 142
git fetch origin main
git merge origin/main
git push
```

## Bonnes pratiques

### Avant de merger

1. **Toujours examiner** une dernière fois les modifications
2. **Vérifier** que tous les tests passent
3. **S'assurer** que les reviews sont positives
4. **Tester localement** si c'est une modification importante

### Choix de la stratégie

- **Squash** pour la plupart des PRs (historique propre)
- **Merge** pour les features importantes (traçabilité)
- **Rebase** pour les projets privilégiant l'historique linéaire

### Communication

- **Notifiez l'équipe** des merges importants
- **Documentez** les changements significatifs
- **Surveillez** les déploiements post-merge

## Résumé

La commande `gh pr merge` est l'aboutissement du workflow de développement collaboratif. Les points essentiels :

**Stratégies de merge :**
- `--merge` : Préserve l'historique complet
- `--squash` : Crée un historique propre (recommandé)
- `--rebase` : Historique linéaire sans merge commits

**Gestion des conflits :**
- Toujours vérifier l'état avant de merger
- Résoudre les conflits localement si nécessaire
- Maintenir sa branche à jour pour éviter les conflits

**Bonnes pratiques :**
- Examiner une dernière fois avant le merge
- Choisir la stratégie appropriée selon le contexte
- Nettoyer après le merge (suppression des branches)
- Surveiller la production post-merge

Le merge réussi d'une Pull Request marque la fin d'un cycle de développement et le début de l'impact réel de vos modifications sur le projet.

⏭️
