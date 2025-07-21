🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 5.3 : Review et commentaires (`gh pr review`)

## Introduction

La review de code est l'une des pratiques les plus importantes du développement collaboratif. Elle permet de maintenir la qualité du code, de partager les connaissances entre développeurs et de détecter les erreurs avant qu'elles n'atteignent la production.

GitHub CLI offre des outils puissants pour effectuer des reviews directement depuis le terminal, sans avoir besoin d'ouvrir un navigateur. Cette approche est particulièrement appréciée par les développeurs qui préfèrent rester dans leur environnement de travail habituel.

## Comprendre les reviews GitHub

### Qu'est-ce qu'une review ?

Une review de Pull Request est un processus où d'autres développeurs examinent les modifications proposées avant qu'elles ne soient intégrées au code principal. Une review peut avoir trois statuts :

- **Comment** : Simple commentaire sans approbation ni rejet
- **Approve** : Approbation des modifications
- **Request changes** : Demande de modifications avant acceptation

### Pourquoi faire des reviews ?

Les reviews permettent de :
- Détecter les bugs et erreurs de logique
- S'assurer que le code respecte les standards de l'équipe
- Partager les connaissances et bonnes pratiques
- Maintenir la cohérence du codebase
- Améliorer la qualité générale du projet

## La commande `gh pr review`

### Syntaxe de base

```bash
gh pr review [numéro-PR] [options]
```

Si vous êtes dans le dossier du projet et qu'il n'y a qu'une seule PR ouverte, vous pouvez même omettre le numéro :

```bash
gh pr review
```

### Types de reviews

#### 1. Laisser un commentaire simple

Pour ajouter un commentaire général sans approuver ni rejeter :

```bash
gh pr review 142 --comment --body "Le code semble bon, mais pourriez-vous ajouter des tests unitaires ?"
```

#### 2. Approuver une Pull Request

Pour approuver les modifications :

```bash
gh pr review 142 --approve --body "Excellent travail ! Le code est propre et bien documenté."
```

Le message d'approbation est optionnel :
```bash
gh pr review 142 --approve
```

#### 3. Demander des modifications

Pour demander des changements avant d'accepter la PR :

```bash
gh pr review 142 --request-changes --body "Merci pour cette contribution. Quelques ajustements sont nécessaires avant le merge."
```

## Mode interactif

### Lancer une review interactive

Pour une expérience plus guidée, utilisez le mode interactif :

```bash
gh pr review 142
```

Sans options supplémentaires, cette commande lancera un processus interactif qui vous permettra de :
1. Choisir le type de review (comment, approve, request changes)
2. Rédiger votre commentaire dans votre éditeur par défaut
3. Confirmer votre choix avant d'envoyer

### Configuration de l'éditeur

Pour que le mode interactif fonctionne bien, assurez-vous que votre éditeur est configuré :

```bash
gh config set editor "code --wait"  # Pour VS Code
gh config set editor "nano"         # Pour nano
gh config set editor "vim"          # Pour vim
```

## Commentaires sur des lignes spécifiques

### Voir le diff avant de commenter

Avant de faire une review, examinez les modifications :

```bash
gh pr view 142 --diff
```

### Ajouter des commentaires sur des lignes

Malheureusement, `gh pr review` ne permet pas directement de commenter des lignes spécifiques depuis la CLI. Pour cela, vous avez deux options :

1. **Ouvrir dans le navigateur** pour les commentaires détaillés :
```bash
gh pr view 142 --web
```

2. **Faire une review générale** et mentionner les lignes dans votre commentaire :
```bash
gh pr review 142 --comment --body "
Ligne 45 dans utils.js : Cette fonction pourrait être optimisée
Ligne 67 dans main.py : Manque la gestion d'erreur
Sinon le code semble bon !
"
```

## Répondre aux reviews existantes

### Voir les reviews existantes

Avant d'ajouter votre propre review, voyez ce que les autres ont dit :

```bash
gh pr view 142 --comments
```

### Ajouter votre review

Vous pouvez ajouter votre review même si d'autres ont déjà reviewé :

```bash
gh pr review 142 --approve --body "Je suis d'accord avec les commentaires précédents. Une fois les corrections appliquées, ce sera parfait."
```

## Gestion des reviews multiples

### Plusieurs reviews sur la même PR

Une Pull Request peut recevoir plusieurs reviews de différentes personnes. Chaque review est indépendante et visible par tous.

### Modifier une review existante

Si vous voulez changer votre review (par exemple, passer de "request changes" à "approve"), créez simplement une nouvelle review :

```bash
gh pr review 142 --approve --body "Merci pour les corrections ! Maintenant c'est parfait."
```

La nouvelle review remplacera l'état de votre review précédente.

## Workflows de review courants

### 1. Review rapide et approbation

Pour une PR simple que vous approuvez rapidement :

```bash
# Examiner d'abord
gh pr view 142 --diff

# Approuver
gh pr review 142 --approve --body "LGTM! (Looks Good To Me)"
```

### 2. Review avec demande de modifications

Pour une PR qui nécessite des ajustements :

```bash
# Examiner
gh pr view 142 --diff

# Demander des modifications
gh pr review 142 --request-changes --body "
Bonne base, mais quelques points à améliorer :
- Ajouter des tests pour la nouvelle fonctionnalité
- Corriger la typo ligne 23
- Optimiser la requête base de données

Merci !
"
```

### 3. Review collaborative

Quand plusieurs personnes doivent reviewer :

```bash
# Voir qui a déjà reviewé
gh pr view 142

# Ajouter votre review
gh pr review 142 --comment --body "Code backend OK de mon côté. En attente de la review frontend de @alice"
```

## Bonnes pratiques pour les reviews

### Rédiger des commentaires constructifs

**✅ Bon exemple :**
```bash
gh pr review 142 --request-changes --body "
Excellente implémentation de la fonctionnalité ! Quelques suggestions :

- La fonction `calculateTotal()` pourrait bénéficier d'une validation des paramètres
- Considérer l'ajout de tests pour les cas d'erreur
- Documentation parfaite, merci !

Ces ajustements amélioreront la robustesse du code.
"
```

**❌ À éviter :**
```bash
gh pr review 142 --request-changes --body "Code pas bon, refaire"
```

### Utiliser un ton positif

- Commencez par souligner les points positifs
- Utilisez "nous" plutôt que "tu" quand possible
- Proposez des solutions, pas seulement des problèmes
- Restez professionnel et bienveillant

### Être spécifique

Au lieu de "améliorer les performances", préférez "la boucle ligne 45 pourrait être optimisée en utilisant une HashMap".

## Automatisation et scripts

### Review automatique pour certains cas

Vous pouvez créer des scripts pour automatiser certaines reviews :

```bash
#!/bin/bash
# Script pour approuver automatiquement les PRs de documentation
if gh pr view $1 --json files --jq '.files[] | select(.filename | contains(".md"))' | grep -q "README\|docs"; then
    gh pr review $1 --approve --body "Documentation update - auto-approved"
fi
```

### Intégration dans les workflows

Les teams peuvent intégrer `gh pr review` dans leurs processus CI/CD pour des reviews automatisées basées sur certains critères.

## Gestion des notifications

### Suivre les reviews

Quand vous faites une review, vous recevez automatiquement les notifications des mises à jour de la PR.

### Paramétrer les notifications

Configurez vos préférences de notifications GitHub pour être alerté des nouvelles reviews sur vos PRs :

```bash
gh pr view 142 --web
# Puis configurer les notifications dans les paramètres GitHub
```

## Résumé

La commande `gh pr review` est essentielle pour :

- **Maintenir la qualité** : Examiner le code avant intégration
- **Collaborer efficacement** : Échanger avec l'équipe sur les modifications
- **Apprendre** : Découvrir de nouvelles approches et bonnes pratiques
- **Documenter** : Laisser une trace des décisions et suggestions

**Points clés à retenir :**
- Utilisez `--approve`, `--request-changes` ou `--comment` selon le contexte
- Rédigez des commentaires constructifs et spécifiques
- Examinez toujours le code avant de faire votre review (`gh pr view --diff`)
- Le mode interactif est parfait pour les reviews détaillées
- N'hésitez pas à poser des questions si quelque chose n'est pas clair

Une bonne culture de review améliore significativement la qualité du code et renforce la collaboration au sein de l'équipe.

⏭️
