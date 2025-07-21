🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 11.2 Créer ses propres extensions

## Introduction

Créer vos propres extensions vous permet d'automatiser vos tâches spécifiques et d'ajouter des fonctionnalités parfaitement adaptées à votre workflow. Contrairement à ce qu'on pourrait penser, créer une extension basique est relativement simple et ne nécessite pas de connaissances avancées en programmation.

## Concept de base d'une extension

### Qu'est-ce qu'une extension concrètement ?

Une extension GitHub CLI est simplement :
- Un fichier exécutable (script ou programme compilé)
- Qui suit une convention de nommage spécifique
- Qui peut être appelé par la CLI GitHub

### Convention de nommage

```bash
# Format obligatoire : gh-<nom-de-votre-extension>
gh-hello          # Devient : gh hello
gh-quick-stats    # Devient : gh quick-stats
gh-deploy-prod    # Devient : gh deploy-prod
```

**Règles importantes :**
- Le nom doit commencer par `gh-`
- Utilisez des tirets pour séparer les mots
- Évitez les caractères spéciaux
- Le nom doit être unique

## Votre première extension simple

### Extension "Hello World" en Bash

Créons une extension basique qui affiche un message de bienvenue :

**Étape 1 : Créer le fichier**
```bash
# Créer le fichier d'extension
touch gh-hello
```

**Étape 2 : Écrire le script**
```bash
#!/bin/bash

# Extension gh-hello
# Usage: gh hello [nom]

# Vérifier si un nom est fourni
if [ $# -eq 0 ]; then
    echo "👋 Bonjour ! Bienvenue dans GitHub CLI !"
    echo "💡 Utilisation : gh hello [votre-nom]"
else
    echo "👋 Bonjour $1 ! Heureux de vous voir utiliser GitHub CLI !"
fi

# Afficher quelques informations utiles
echo ""
echo "📊 Informations rapides :"
echo "Repository actuel : $(basename $(git rev-parse --show-toplevel) 2>/dev/null || echo 'Aucun')"
echo "Utilisateur GitHub : $(gh api user --jq .login 2>/dev/null || echo 'Non connecté')"
```

**Étape 3 : Rendre le fichier exécutable**
```bash
chmod +x gh-hello
```

**Étape 4 : Installer l'extension localement**
```bash
# Installer depuis le dossier courant
gh extension install .
```

**Étape 5 : Tester l'extension**
```bash
# Tester sans paramètre
gh hello

# Tester avec un nom
gh hello Nicolas
```

## Structure d'une extension plus complète

### Extension avec gestion des options

Voici une extension plus avancée qui gère les options et paramètres :

```bash
#!/bin/bash

# Extension gh-quick-stats
# Affiche des statistiques rapides sur le repository courant

# Variables par défaut
SHOW_HELP=false
SHOW_VERBOSE=false
REPO_PATH="."

# Fonction d'aide
show_help() {
    echo "gh-quick-stats - Statistiques rapides du repository"
    echo ""
    echo "Usage:"
    echo "  gh quick-stats [options]"
    echo ""
    echo "Options:"
    echo "  -h, --help     Afficher cette aide"
    echo "  -v, --verbose  Affichage détaillé"
    echo "  -p, --path     Chemin du repository (défaut: .)"
    echo ""
    echo "Exemples:"
    echo "  gh quick-stats"
    echo "  gh quick-stats --verbose"
    echo "  gh quick-stats --path /path/to/repo"
}

# Analyser les arguments
while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            SHOW_HELP=true
            shift
            ;;
        -v|--verbose)
            SHOW_VERBOSE=true
            shift
            ;;
        -p|--path)
            REPO_PATH="$2"
            shift 2
            ;;
        *)
            echo "❌ Option inconnue: $1"
            echo "Utilisez --help pour voir les options disponibles"
            exit 1
            ;;
    esac
done

# Afficher l'aide si demandé
if [ "$SHOW_HELP" = true ]; then
    show_help
    exit 0
fi

# Vérifier qu'on est dans un repository Git
if ! git -C "$REPO_PATH" rev-parse --git-dir > /dev/null 2>&1; then
    echo "❌ Erreur : Pas un repository Git valide"
    exit 1
fi

# Fonctions d'analyse
get_commit_count() {
    git -C "$REPO_PATH" rev-list --count HEAD 2>/dev/null || echo "0"
}

get_branch_count() {
    git -C "$REPO_PATH" branch -r | wc -l 2>/dev/null || echo "0"
}

get_last_commit_date() {
    git -C "$REPO_PATH" log -1 --format=%cd --date=relative 2>/dev/null || echo "Inconnu"
}

# Affichage des statistiques
echo "📊 Statistiques rapides du repository"
echo "======================================="
echo ""

# Informations de base
echo "📁 Repository : $(basename $(git -C "$REPO_PATH" rev-parse --show-toplevel) 2>/dev/null)"
echo "🔗 Branche actuelle : $(git -C "$REPO_PATH" branch --show-current 2>/dev/null)"
echo "📝 Nombre de commits : $(get_commit_count)"
echo "🌿 Nombre de branches : $(get_branch_count)"
echo "⏰ Dernier commit : $(get_last_commit_date)"

# Informations détaillées si verbose
if [ "$SHOW_VERBOSE" = true ]; then
    echo ""
    echo "📋 Informations détaillées :"
    echo "-------------------------"

    # Contributeurs
    echo "👥 Contributeurs principaux :"
    git -C "$REPO_PATH" shortlog -sn HEAD | head -5 2>/dev/null

    echo ""
    echo "📈 Activité récente :"
    git -C "$REPO_PATH" log --oneline -5 2>/dev/null
fi

echo ""
echo "✅ Analyse terminée !"
```

## Extensions en d'autres langages

### Extension Python

Les extensions peuvent être écrites dans n'importe quel langage. Voici un exemple en Python :

```python
#!/usr/bin/env python3

# Extension gh-repo-info
# Affiche des informations détaillées sur le repository

import sys
import subprocess
import json
import argparse

def run_command(command):
    """Exécute une commande et retourne le résultat"""
    try:
        result = subprocess.run(command, shell=True, capture_output=True, text=True)
        return result.stdout.strip()
    except Exception as e:
        return f"Erreur: {e}"

def get_repo_info():
    """Récupère les informations du repository via l'API GitHub"""
    try:
        # Obtenir le nom du repository
        remote_url = run_command("git remote get-url origin")
        if "github.com" not in remote_url:
            return None

        # Extraire owner/repo de l'URL
        if remote_url.startswith("git@"):
            repo_path = remote_url.split(":")[1].replace(".git", "")
        else:
            repo_path = remote_url.split("/")[-2] + "/" + remote_url.split("/")[-1].replace(".git", "")

        # Appeler l'API GitHub
        api_result = run_command(f"gh api repos/{repo_path}")
        return json.loads(api_result) if api_result else None
    except:
        return None

def main():
    parser = argparse.ArgumentParser(description="Informations détaillées du repository")
    parser.add_argument("--format", choices=["text", "json"], default="text",
                       help="Format de sortie")
    args = parser.parse_args()

    print("🔍 Analyse du repository en cours...")

    # Informations Git locales
    repo_name = run_command("basename $(git rev-parse --show-toplevel)")
    current_branch = run_command("git branch --show-current")
    commit_count = run_command("git rev-list --count HEAD")

    # Informations GitHub API
    repo_info = get_repo_info()

    if args.format == "json":
        data = {
            "local": {
                "name": repo_name,
                "branch": current_branch,
                "commits": commit_count
            },
            "github": repo_info
        }
        print(json.dumps(data, indent=2))
    else:
        print(f"\n📁 Repository : {repo_name}")
        print(f"🌿 Branche : {current_branch}")
        print(f"📝 Commits : {commit_count}")

        if repo_info:
            print(f"⭐ Étoiles : {repo_info.get('stargazers_count', 'N/A')}")
            print(f"🍴 Forks : {repo_info.get('forks_count', 'N/A')}")
            print(f"📄 Description : {repo_info.get('description', 'Aucune')}")
            print(f"🔗 URL : {repo_info.get('html_url', 'N/A')}")
        else:
            print("ℹ️  Informations GitHub non disponibles")

if __name__ == "__main__":
    main()
```

Pour utiliser cette extension Python :

```bash
# Rendre le fichier exécutable
chmod +x gh-repo-info

# Installer l'extension
gh extension install .

# Utiliser l'extension
gh repo-info
gh repo-info --format json
```

## Installation et test d'extensions locales

### Installation depuis un dossier

```bash
# Naviguer vers le dossier contenant votre extension
cd /path/to/your/extension

# Installer l'extension localement
gh extension install .

# Vérifier l'installation
gh extension list
```

### Test et débogage

```bash
# Tester l'extension
gh nom-de-votre-extension

# Tester avec différents paramètres
gh nom-de-votre-extension --help
gh nom-de-votre-extension --option valeur

# Voir les erreurs détaillées
gh nom-de-votre-extension 2>&1
```

### Mise à jour d'une extension locale

```bash
# Après modification du code
# Réinstaller l'extension
gh extension remove nom-extension
gh extension install .
```

## Partage et distribution

### Créer un repository GitHub

Pour partager votre extension :

```bash
# Créer un nouveau repository
gh repo create gh-votre-extension --public

# Ajouter votre code
git add .
git commit -m "Première version de l'extension"
git push origin main
```

### Structure recommandée du repository

```
gh-votre-extension/
├── gh-votre-extension          # Le script principal
├── README.md                   # Documentation
├── LICENSE                     # Licence (recommandé)
└── .gitignore                 # Fichiers à ignorer
```

### README.md pour votre extension

```markdown
# gh-votre-extension

Description courte de votre extension.

## Installation

```bash
gh extension install votre-username/gh-votre-extension
```

## Usage

```bash
gh votre-extension [options]
```

## Options

- `--help` : Afficher l'aide
- `--option` : Description de l'option

## Exemples

```bash
gh votre-extension
gh votre-extension --option valeur
```
```

## Bonnes pratiques pour les extensions

### Code propre et documenté

- ✅ Ajoutez des commentaires dans votre code
- ✅ Gérez les erreurs proprement
- ✅ Fournissez une option `--help`
- ✅ Validez les paramètres d'entrée

### Gestion des erreurs

```bash
# Vérifier les prérequis
if ! command -v git &> /dev/null; then
    echo "❌ Git n'est pas installé"
    exit 1
fi

# Vérifier qu'on est dans un repo Git
if ! git rev-parse --git-dir > /dev/null 2>&1; then
    echo "❌ Pas dans un repository Git"
    exit 1
fi

# Vérifier l'authentification GitHub
if ! gh auth status &> /dev/null; then
    echo "❌ Non authentifié avec GitHub"
    echo "💡 Exécutez : gh auth login"
    exit 1
fi
```

### Performance et efficacité

- ✅ Évitez les appels API inutiles
- ✅ Mettez en cache les résultats si possible
- ✅ Affichez des messages de progression pour les opérations longues
- ✅ Permettez l'interruption avec Ctrl+C

## Exemples d'idées d'extensions

### Extensions utiles pour débuter

1. **gh-quick-pr** : Créer une PR avec un template pré-rempli
2. **gh-cleanup** : Supprimer les branches mergées localement
3. **gh-stats** : Afficher des statistiques personnalisées
4. **gh-backup** : Sauvegarder les issues et PRs
5. **gh-team-notify** : Notifier l'équipe sur Slack/Teams

### Extensions d'automatisation

1. **gh-deploy** : Automatiser le déploiement
2. **gh-release** : Créer des releases avec changelog automatique
3. **gh-sync** : Synchroniser avec d'autres outils (Jira, Trello)
4. **gh-report** : Générer des rapports périodiques

## Ressources et aide

### Documentation officielle

- GitHub CLI Extensions : https://cli.github.com/manual/gh_extension
- API GitHub : https://docs.github.com/en/rest

### Exemples d'extensions populaires

Étudiez le code source de ces extensions pour apprendre :
- `dlvhdr/gh-dash` : Interface utilisateur avancée
- `mislav/gh-branch` : Gestion des branches
- `vilmibm/gh-screensaver` : Animations et effets visuels

### Outils de développement

```bash
# Déboguer vos scripts
bash -x gh-votre-extension

# Vérifier la syntaxe
bash -n gh-votre-extension

# Linter pour bash (si installé)
shellcheck gh-votre-extension
```

---

**🎯 Points clés à retenir :**
- Une extension est un simple fichier exécutable nommé `gh-*`
- Commencez par des scripts bash simples avant d'utiliser d'autres langages
- Testez toujours localement avec `gh extension install .`
- Gérez les erreurs et fournissez de l'aide (`--help`)
- Partagez vos extensions utiles sur GitHub pour la communauté

**➡️ Prochaine étape :** Dans la section 11.3, nous verrons comment personnaliser l'affichage et les formats de sortie de la CLI GitHub.

⏭️
