🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 9.3 Workflow complet : dev → commit → push → PR

## Introduction au workflow moderne

Un workflow de développement est comme une recette de cuisine : une série d'étapes logiques qui, bien suivies, garantissent un résultat réussi. Dans cette section, nous allons découvrir le workflow standard utilisé par la plupart des équipes de développement modernes.

## Vue d'ensemble du workflow

```
📋 Planification    🔧 Développement    📤 Partage    👥 Collaboration    🚀 Déploiement
      ↓                    ↓               ↓             ↓                ↓
   Issue/Task    →    Code local    →    Push/PR    →   Review/Test   →   Merge
```

**Les 4 phases principales :**
1. **Préparation** : Synchronisation et création de branche
2. **Développement** : Écriture du code et commits locaux
3. **Partage** : Push et création de Pull Request
4. **Finalisation** : Review, tests et merge

## Phase 1 : Préparation

### Étape 1.1 : Synchroniser avec la branche principale

**Pourquoi ?** Partir d'une base à jour évite les conflits plus tard.

```bash
# Aller sur la branche principale (main ou master)
git checkout main

# Récupérer les derniers changements
git pull origin main

# Vérifier que tout est à jour
git status
# Doit afficher : "Your branch is up to date with 'origin/main'"
```

**💡 Astuce débutant :** Toujours commencer par cette étape, même si vous pensez être à jour !

### Étape 1.2 : Créer une branche de travail

**Pourquoi ?** Isoler votre travail pour ne pas perturber la branche principale.

```bash
# Créer et basculer sur une nouvelle branche
git checkout -b feature/ajout-page-contact

# Vérifier qu'on est sur la bonne branche
git branch
# La branche active est marquée avec une étoile (*)
```

**🎯 Convention de nommage des branches :**
- `feature/nom-fonctionnalite` → Nouvelle fonctionnalité
- `fix/nom-bug` → Correction de bug
- `hotfix/probleme-urgent` → Correction urgente
- `docs/mise-a-jour` → Documentation

### Étape 1.3 : Vérifier le contexte GitHub

```bash
# Vérifier dans quel repository on travaille
gh repo view

# Vérifier l'authentification GitHub
gh auth status
```

## Phase 2 : Développement

### Étape 2.1 : Développer votre fonctionnalité

Voici un exemple concret avec création d'une page de contact :

```bash
# Créer le fichier HTML
cat > contact.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>Contact</title>
</head>
<body>
    <h1>Contactez-nous</h1>
    <form>
        <label>Email: <input type="email" name="email"></label>
        <label>Message: <textarea name="message"></textarea></label>
        <button type="submit">Envoyer</button>
    </form>
</body>
</html>
EOF

# Créer le fichier CSS
cat > styles/contact.css << 'EOF'
form {
    max-width: 500px;
    margin: 20px auto;
}
label {
    display: block;
    margin: 10px 0;
}
input, textarea {
    width: 100%;
    padding: 8px;
}
EOF
```

### Étape 2.2 : Vérifier l'état des changements

```bash
# Voir quels fichiers ont été modifiés
git status

# Exemple de sortie :
# On branch feature/ajout-page-contact
# Untracked files:
#   contact.html
#   styles/contact.css
```

### Étape 2.3 : Commit atomiques (étape par étape)

**Principe :** Un commit = une modification logique cohérente.

```bash
# Commit 1 : Ajouter la structure HTML
git add contact.html
git commit -m "Add contact page HTML structure"

# Commit 2 : Ajouter les styles
git add styles/contact.css
git commit -m "Add contact page CSS styling"

# Vérifier l'historique
git log --oneline -3
```

**✅ Bons messages de commit :**
- `Add contact form validation`
- `Fix email validation bug`
- `Update contact page layout`

**❌ Mauvais messages de commit :**
- `wip` (work in progress)
- `fix`
- `update stuff`

### Étape 2.4 : Commits complexes avec plusieurs fichiers

Pour des modifications qui touchent plusieurs fichiers liés :

```bash
# Exemple : Ajouter un lien vers la page contact dans le menu
# Modifier navbar.html
echo '<a href="contact.html">Contact</a>' >> navbar.html

# Modifier le CSS du menu
echo '.navbar a { margin-right: 10px; }' >> styles/navbar.css

# Commiter ensemble car c'est une modification logique unique
git add navbar.html styles/navbar.css
git commit -m "Add contact link to navigation menu"
```

## Phase 3 : Partage

### Étape 3.1 : Push vers GitHub

```bash
# Push de la branche vers GitHub
git push origin feature/ajout-page-contact

# Si c'est la première fois, Git vous propose la commande complète :
# git push --set-upstream origin feature/ajout-page-contact
```

**Ce qui se passe :** Vos commits locaux sont maintenant visibles sur GitHub !

### Étape 3.2 : Créer une Pull Request

```bash
# Création simple
gh pr create --title "Ajout d'une page de contact" \
             --body "Cette PR ajoute une page de contact avec formulaire"

# Création avec plus de détails
gh pr create --title "Ajout d'une page de contact" \
             --body "## Description
Cette PR ajoute une page de contact complète au site.

## Changements
- Nouveau fichier contact.html avec formulaire
- Styles CSS pour la page contact
- Lien ajouté dans la navigation

## À tester
- [ ] Le formulaire s'affiche correctement
- [ ] Les styles sont cohérents avec le site
- [ ] Le lien de navigation fonctionne"
```

### Étape 3.3 : Vérifier la PR créée

```bash
# Voir les détails de votre PR
gh pr view

# Voir le statut des checks automatiques (tests, etc.)
gh pr checks

# Lister toutes les PRs pour contexte
gh pr list
```

## Phase 4 : Finalisation

### Étape 4.1 : Collaboration et review

**Si vous recevez des commentaires :**

```bash
# Voir les commentaires sur votre PR
gh pr view --comments

# Faire des modifications supplémentaires
echo '<p>Merci pour votre message!</p>' >> contact.html
git add contact.html
git commit -m "Add thank you message to contact form"

# Push des nouvelles modifications
git push origin feature/ajout-page-contact
# La PR se met à jour automatiquement !
```

**Si vous reviewez la PR de quelqu'un d'autre :**

```bash
# Récupérer la PR #25 localement pour la tester
gh pr checkout 25

# Tester le code...
# Puis laisser une review
gh pr review 25 --approve --body "Super travail ! Le formulaire fonctionne parfaitement."

# Ou demander des changements
gh pr review 25 --request-changes --body "Pourrais-tu ajouter la validation email ?"
```

### Étape 4.2 : Merger la Pull Request

Quand la PR est approuvée et les tests passent :

```bash
# Option 1 : Merge classique (conserve l'historique)
gh pr merge feature/ajout-page-contact --merge

# Option 2 : Squash (regroupe tous les commits en un seul)
gh pr merge feature/ajout-page-contact --squash

# Option 3 : Rebase (applique les commits un par un)
gh pr merge feature/ajout-page-contact --rebase

# Avec suppression automatique de la branche
gh pr merge feature/ajout-page-contact --squash --delete-branch
```

**💡 Conseil équipe :** Choisissez une stratégie de merge et restez cohérents.

### Étape 4.3 : Nettoyage post-merge

```bash
# Revenir sur main
git checkout main

# Récupérer les changements mergés
git pull origin main

# Supprimer la branche locale devenue inutile
git branch -d feature/ajout-page-contact

# Vérifier que tout est propre
git status
# Doit afficher : "nothing to commit, working tree clean"
```

## Workflow complet en exemple

Voici un exemple complet du début à la fin :

```bash
# === PHASE 1 : PRÉPARATION ===
git checkout main
git pull origin main
git checkout -b fix/bouton-envoi-contact

# === PHASE 2 : DÉVELOPPEMENT ===
# Correction du bug du bouton
sed -i 's/type="button"/type="submit"/' contact.html
git add contact.html
git commit -m "Fix contact form submit button type"

# Test et commit supplémentaire si nécessaire
echo 'button[type="submit"] { background: blue; }' >> styles/contact.css
git add styles/contact.css
git commit -m "Style submit button for better visibility"

# === PHASE 3 : PARTAGE ===
git push origin fix/bouton-envoi-contact
gh pr create --title "Fix contact form submit button" \
             --body "The button had type='button' instead of type='submit'"

# === PHASE 4 : FINALISATION ===
# Attendre review, puis merger
gh pr merge fix/bouton-envoi-contact --squash --delete-branch

# Nettoyage
git checkout main
git pull origin main
git branch -d fix/bouton-envoi-contact
```

## Variantes du workflow

### Workflow court (hotfixes)

Pour les corrections urgentes :

```bash
git checkout main && git pull origin main
git checkout -b hotfix/typo-homepage
# ... correction rapide ...
git add . && git commit -m "Fix typo on homepage"
git push origin hotfix/typo-homepage
gh pr create --title "Fix typo on homepage" --body "Quick typo fix"
gh pr merge hotfix/typo-homepage --squash --delete-branch
```

### Workflow collaboratif (feature complexe)

Pour les fonctionnalités développées à plusieurs :

```bash
# Développeur 1 : crée la base
git checkout -b feature/user-auth
# ... développement ...
git push origin feature/user-auth
gh pr create --draft --title "User authentication (WIP)"

# Développeur 2 : continue le travail
gh pr checkout feature/user-auth
# ... développement ...
git push origin feature/user-auth

# Quand terminé : sortir du mode draft
gh pr ready feature/user-auth
```

## Gestion des erreurs courantes

### "Branch is behind"

```bash
# Si votre branche est en retard sur main
git checkout feature/ma-branche
git rebase main  # Appliquer les nouveaux commits de main

# Ou merger main dans votre branche
git merge main
```

### "Merge conflicts"

```bash
# Résoudre les conflits
git status  # Voir les fichiers en conflit
# Éditer les fichiers pour résoudre les conflits
git add fichier-resolu.html
git commit -m "Resolve merge conflicts"
```

### "Permission denied" lors du push

```bash
# Vérifier l'authentification
gh auth status
gh auth login  # Se reconnecter si nécessaire
```

## Bonnes pratiques du workflow

### 1. Commits fréquents et petits

```bash
# ✅ Bon : commits logiques et fréquents
git commit -m "Add form HTML structure"
git commit -m "Add form CSS styling"
git commit -m "Add form validation"

# ❌ Éviter : un gros commit en fin de journée
git commit -m "Add entire contact form feature"
```

### 2. Messages descriptifs

```bash
# ✅ Message clair et actionnable
git commit -m "Fix email validation to accept + in addresses"

# ❌ Message vague
git commit -m "fix bug"
```

### 3. Tests avant push

```bash
# Toujours tester localement avant de partager
npm test           # Tests automatisés
npm run lint       # Vérification du style de code
# Tester manuellement dans le navigateur
git push origin ma-branche  # Seulement après validation
```

## Résumé des bénéfices

Ce workflow structuré vous apporte :

- **🔒 Sécurité** : Branche principale toujours stable
- **👥 Collaboration** : Code reviewé avant intégration
- **📚 Traçabilité** : Historique clair des changements
- **🔄 Réversibilité** : Possibilité d'annuler facilement
- **⚡ Efficacité** : Process standardisé et automatisable

Une fois maîtrisé, ce workflow devient naturel et vous permettra de collaborer efficacement avec n'importe quelle équipe de développement !

⏭️
