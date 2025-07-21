🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 11.1 Installer des extensions (`gh extension`)

## Introduction

L'installation d'extensions est l'une des fonctionnalités les plus utiles de la CLI GitHub. Elle vous permet d'ajouter de nouvelles commandes et fonctionnalités à votre installation de `gh` en quelques commandes simples.

## Commandes de base pour les extensions

La CLI GitHub fournit un ensemble de commandes dédiées à la gestion des extensions, toutes regroupées sous `gh extension` (ou sa forme abrégée `gh ext`).

### Vue d'ensemble des commandes

```bash
# Commandes principales pour les extensions
gh extension --help
gh ext --help              # Version abrégée

# Les sous-commandes disponibles :
gh extension list           # Lister les extensions installées
gh extension browse         # Parcourir les extensions disponibles
gh extension install        # Installer une extension
gh extension upgrade        # Mettre à jour les extensions
gh extension remove         # Désinstaller une extension
```

## Découvrir les extensions disponibles

### Parcourir le catalogue d'extensions

Avant d'installer des extensions, il est utile de voir ce qui est disponible :

```bash
# Afficher toutes les extensions disponibles
gh extension browse

# Afficher avec plus de détails
gh extension browse --web
```

La commande `gh extension browse` affiche une liste des extensions populaires avec :
- Le nom de l'extension
- Une description courte
- Le nombre d'étoiles sur GitHub
- La date de dernière mise à jour

### Recherche d'extensions spécifiques

Si vous cherchez une fonctionnalité particulière, vous pouvez :

```bash
# Rechercher des extensions (ouvrira GitHub dans le navigateur)
gh extension browse --web

# Ensuite utiliser la recherche sur GitHub avec des mots-clés comme :
# "gh extension", "github cli extension", etc.
```

## Installation d'extensions

### Installation depuis un repository GitHub

La méthode la plus courante pour installer une extension :

```bash
# Syntaxe générale
gh extension install OWNER/REPOSITORY

# Exemples concrets
gh extension install dlvhdr/gh-dash
gh extension install vilmibm/gh-screensaver
gh extension install mislav/gh-branch
```

**Explication :**
- `OWNER` : Le nom d'utilisateur ou d'organisation GitHub
- `REPOSITORY` : Le nom du repository contenant l'extension
- L'extension sera automatiquement téléchargée et installée

### Installation depuis une URL complète

Vous pouvez aussi utiliser l'URL complète du repository :

```bash
# Installation avec URL complète
gh extension install https://github.com/dlvhdr/gh-dash
```

### Installation locale (développement)

Pour tester une extension en cours de développement :

```bash
# Installation depuis un dossier local
gh extension install /path/to/local/extension

# Installation depuis le dossier courant
gh extension install .
```

## Vérification des installations

### Lister les extensions installées

```bash
# Voir toutes les extensions installées
gh extension list
```

Cette commande affiche :
- Le nom de l'extension
- Le repository source
- La version installée
- Le statut (à jour ou non)

### Exemple de sortie

```
NAME          REPO                    VERSION
dash          dlvhdr/gh-dash         v3.7.1
screensaver   vilmibm/gh-screensaver v1.0.0
branch        mislav/gh-branch       v0.1.0
```

## Utilisation des extensions installées

### Syntaxe d'utilisation

Une fois installée, l'extension devient disponible comme une sous-commande de `gh` :

```bash
# Si vous avez installé gh-dash
gh dash

# Si vous avez installé gh-branch
gh branch

# Avec des options (selon l'extension)
gh dash --help
gh branch --help
```

### Découvrir les fonctionnalités d'une extension

Chaque extension a sa propre aide :

```bash
# Afficher l'aide d'une extension
gh nom-extension --help

# Exemples
gh dash --help
gh branch --help
```

## Gestion des extensions installées

### Mise à jour des extensions

```bash
# Mettre à jour toutes les extensions
gh extension upgrade

# Mettre à jour une extension spécifique
gh extension upgrade nom-extension

# Exemple
gh extension upgrade dash
```

### Désinstallation d'extensions

```bash
# Désinstaller une extension
gh extension remove nom-extension

# Exemple
gh extension remove dash
```

## Extensions populaires recommandées pour débuter

Voici quelques extensions utiles pour commencer :

### gh-dash - Tableau de bord interactif

```bash
gh extension install dlvhdr/gh-dash
```

**Utilité :** Affiche un tableau de bord interactif avec vos PRs, issues, et notifications.

**Usage :**
```bash
gh dash
```

### gh-screensaver - Économiseur d'écran GitHub

```bash
gh extension install vilmibm/gh-screensaver
```

**Utilité :** Affiche une animation des contributions GitHub (plus pour le fun !).

**Usage :**
```bash
gh screensaver
```

### gh-branch - Gestion avancée des branches

```bash
gh extension install mislav/gh-branch
```

**Utilité :** Fonctionnalités supplémentaires pour la gestion des branches.

**Usage :**
```bash
gh branch
```

## Comprendre les permissions

### Sécurité des extensions

Quand vous installez une extension :

1. **Code source visible :** Les extensions sont des repositories GitHub publics
2. **Exécution locale :** Le code s'exécute sur votre machine
3. **Accès aux tokens :** Les extensions peuvent utiliser vos tokens GitHub

### Bonnes pratiques de sécurité

- ✅ Vérifiez la popularité de l'extension (nombre d'étoiles)
- ✅ Regardez la date de dernière mise à jour
- ✅ Consultez le code source si possible
- ✅ Préférez les extensions d'auteurs reconnus
- ❌ Évitez les extensions sans documentation

## Résolution de problèmes courants

### Extension non trouvée

```bash
# Erreur : extension not found
# Solution : Vérifiez l'orthographe du nom
gh extension install owner/correct-repository-name
```

### Problèmes de permissions

```bash
# Si vous avez des erreurs de permissions
# Vérifiez votre authentification
gh auth status

# Re-authentifiez si nécessaire
gh auth login
```

### Extension qui ne fonctionne pas

```bash
# Vérifiez que l'extension est bien installée
gh extension list

# Essayez de la réinstaller
gh extension remove nom-extension
gh extension install owner/repository
```

## Où trouver plus d'extensions

### Sources recommandées

1. **GitHub Topics :** Recherchez "gh-extension" sur GitHub
2. **Awesome lists :** Cherchez "awesome github cli" sur GitHub
3. **Documentation officielle :** GitHub CLI documentation
4. **Communauté :** Forums et discussions GitHub

### Critères de sélection

Quand vous choisissez une extension, regardez :
- **Nombre d'étoiles ⭐** : Popularité
- **Date de mise à jour 📅** : Maintenance active
- **Documentation 📖** : Qualité des instructions
- **Issues ouvertes 🐛** : Problèmes connus

## Conseils pour bien démarrer

### Commencez petit

1. Installez une ou deux extensions populaires
2. Apprenez à les utiliser correctement
3. Explorez d'autres extensions progressivement

### Testez avant d'adopter

```bash
# Installez une extension
gh extension install owner/repository

# Testez-la
gh extension-name --help
gh extension-name

# Si elle ne vous convient pas
gh extension remove extension-name
```

### Gardez vos extensions à jour

```bash
# Vérifiez régulièrement les mises à jour
gh extension list

# Mettez à jour quand nécessaire
gh extension upgrade
```

---

**🎯 Points clés à retenir :**
- Les extensions s'installent avec `gh extension install owner/repository`
- Utilisez `gh extension browse` pour découvrir de nouvelles extensions
- Chaque extension devient une nouvelle sous-commande de `gh`
- Gardez vos extensions à jour avec `gh extension upgrade`
- Vérifiez toujours la fiabilité d'une extension avant l'installation

**➡️ Prochaine étape :** Dans la section 11.2, nous apprendrons à créer nos propres extensions personnalisées.

⏭️
