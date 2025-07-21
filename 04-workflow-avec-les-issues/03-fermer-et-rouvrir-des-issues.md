🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 4.3 - Fermer et rouvrir des issues

## 🎯 Introduction

Le cycle de vie d'une issue ne s'arrête pas à sa création ! Savoir quand et comment fermer ou rouvrir une issue est crucial pour maintenir un backlog propre et un historique utile. Cette section vous apprendra à gérer intelligemment la fermeture des issues pour une organisation optimale de vos projets.

**Analogie** : Si les issues sont vos tâches dans un carnet, fermer une issue c'est comme cocher une case ✅ - mais il faut savoir quand la cocher et parfois la décocher si nécessaire !

## 🔄 Comprendre le cycle de vie des issues

### États d'une issue

Une issue GitHub a **deux états principaux** :

#### État "Ouvert" (Open)
- 🟢 **Active** : Travail en cours ou à faire
- 🎯 **Visible** : Apparaît dans les listes par défaut
- 📊 **Comptabilisée** : Dans les métriques d'avancement

#### État "Fermé" (Closed)
- ✅ **Terminée** : Travail accompli ou abandonné
- 👻 **Archivée** : N'apparaît plus par défaut
- 📈 **Historique** : Conservée pour référence

### Raisons de fermer une issue

**Issues résolues** :
- ✅ **Fonctionnalité implémentée** avec succès
- ✅ **Bug corrigé** et testé
- ✅ **Documentation mise à jour**
- ✅ **Tâche accomplie**

**Issues obsolètes** :
- 🚫 **Plus d'actualité** (changement de priorités)
- 🚫 **Doublon** d'une autre issue
- 🚫 **Hors scope** du projet
- 🚫 **Problème externe** (pas notre responsabilité)

### Raisons de rouvrir une issue

**Résolution incomplète** :
- 🔄 **Bug récurrent** après correction
- 🔄 **Fonctionnalité** partiellement implémentée
- 🔄 **Régression** détectée

**Nouvelle information** :
- 📝 **Détails supplémentaires** découverts
- 📝 **Impact** plus large que prévu
- 📝 **Solution** s'avère insuffisante

## ✅ Fermer des issues avec `gh issue close`

### Syntaxe de base

```bash
# Fermer une issue simple
gh issue close 42

# Fermer avec un commentaire
gh issue close 42 --comment "Problème résolu dans le commit abc123"

# Fermer comme résolu vs non planifié
gh issue close 42 --reason completed    # Par défaut
gh issue close 42 --reason "not planned"
```

### Fermeture simple

```bash
# Issue terminée
gh issue close 125

# Vérifier qu'elle est bien fermée
gh issue view 125
```

**Résultat** : L'issue passe à l'état "Closed" et disparaît des listes par défaut.

### Fermeture avec commentaire explicatif

```bash
# Bug corrigé
gh issue close 87 --comment "Bug corrigé dans le commit 4a2b8c9. La validation des formulaires fonctionne maintenant correctement sur tous les navigateurs."

# Fonctionnalité livrée
gh issue close 156 --comment "Fonctionnalité de recherche implémentée et déployée en production. Les utilisateurs peuvent maintenant rechercher par titre et contenu."

# Issue obsolète
gh issue close 203 --comment "Cette fonctionnalité n'est plus prioritaire suite au pivot produit. Nous nous concentrons maintenant sur l'API mobile."
```

### Types de fermeture avec raisons

#### Fermeture comme "completed" (résolu)

```bash
# Travail terminé avec succès
gh issue close 42 --reason completed --comment "Implémentation terminée et testée"
```

**Utiliser pour** :
- ✅ Bugs corrigés
- ✅ Fonctionnalités implémentées
- ✅ Tâches accomplies
- ✅ Documentation mise à jour

#### Fermeture comme "not planned" (non planifié)

```bash
# Issue abandonnée ou hors scope
gh issue close 42 --reason "not planned" --comment "Fonctionnalité reportée à une version ultérieure"
```

**Utiliser pour** :
- 🚫 Doublons
- 🚫 Hors scope du projet
- 🚫 Priorités changées
- 🚫 Ressources insuffisantes

## 🔄 Rouvrir des issues avec `gh issue reopen`

### Syntaxe de base

```bash
# Rouvrir une issue fermée
gh issue reopen 42

# Rouvrir avec commentaire
gh issue reopen 42 --comment "Le bug réapparaît sur la version mobile"
```

### Cas de réouverture

#### Bug récurrent

```bash
gh issue reopen 87 --comment "Le bug de validation réapparaît sur Safari 16. La correction précédente était incomplète.

Nouveaux détails :
- Navigateur : Safari 16.2
- OS : macOS 13.1
- Comportement : Le formulaire ne valide plus les emails avec des caractères spéciaux"
```

#### Régression détectée

```bash
gh issue reopen 156 --comment "Régression détectée après le déploiement v2.1.3. La fonctionnalité de recherche ne retourne plus de résultats pour les requêtes avec accents.

À investiguer :
- Version introduisant le problème
- Impact sur les utilisateurs
- Solution de contournement temporaire"
```

#### Scope élargi

```bash
gh issue reopen 203 --comment "Suite aux retours utilisateurs, cette fonctionnalité redevient prioritaire. Le besoin est confirmé par 85% des utilisateurs interrogés.

Nouvelle approche :
- Implémentation MVP d'abord
- Itération selon feedback
- Timeline : Sprint 15"
```

## 🎯 Workflows de fermeture

### Workflow développeur solo

#### Fermeture après développement

```bash
# 1. Terminer le développement
git add .
git commit -m "Implement user authentication

Fixes #42"

git push

# 2. Fermer l'issue manuellement (si pas automatique)
gh issue close 42 --comment "Authentification utilisateur implémentée avec :
- Connexion par email/mot de passe
- Validation côté client et serveur
- Session persistante
- Déconnexion sécurisée

Tests passés, fonctionnalité prête."
```

#### Nettoyage du backlog

```bash
# Identifier les issues obsolètes
gh issue list --search "updated:<$(date -d '30 days ago' +%Y-%m-%d)" --state open

# Fermer les issues anciennes et obsolètes
gh issue close 78 --reason "not planned" --comment "Issue obsolète suite au changement d'architecture. Les besoins ont évolué."

gh issue close 91 --reason "not planned" --comment "Doublon de l'issue #89 qui est plus complète."
```

### Workflow équipe

#### Fermeture avec validation

```bash
# Développeur ferme après implémentation
gh issue close 145 --comment "Implémentation terminée. Ready for QA testing.

Changes :
- New dashboard component
- Responsive design
- Unit tests added
- Documentation updated

@qa-team please validate"

# QA rouvre si problème détecté
gh issue reopen 145 --comment "Issue found during testing :
- Dashboard doesn't load on IE11
- Mobile version has alignment issues

@alice please check these items"

# Développeur corrige et referme
gh issue close 145 --comment "All QA issues fixed :
✅ IE11 compatibility added
✅ Mobile alignment corrected
✅ Cross-browser testing passed

Ready for production deployment."
```

#### Review de sprint

```bash
# Fermer les issues du sprint terminé
gh issue list --milestone "Sprint 12" --state open --json number,title | \
  jq -r '.[] | "\(.number)|\(.title)"' | \
  while IFS='|' read -r number title; do
    echo "Issue #$number: $title"
    read -p "Fermer cette issue ? (y/n): " -n 1 -r
    echo
    if [[ $REPLY =~ ^[Yy]$ ]]; then
      gh issue close "$number" --comment "Issue fermée lors de la review de Sprint 12"
    fi
  done
```

### Workflow open source

#### Gestion des contributions externes

```bash
# Fermer une demande de fonctionnalité hors scope
gh issue close 234 --reason "not planned" --comment "Thank you for this feature request!

While this is an interesting idea, it falls outside our current project scope and roadmap. We're focusing on core functionality improvements for v2.0.

Feel free to implement this as a plugin or fork the project if you'd like to explore this direction!"

# Fermer un doublon
gh issue close 267 --reason "not planned" --comment "This is a duplicate of issue #234. Please continue the discussion there.

Thanks for reporting this!"
```

#### Fermeture après contribution communautaire

```bash
gh issue close 189 --comment "Fixed by community contribution! 🎉

Thanks to @contributor-name for the excellent PR #456 that resolves this issue.

Changes included:
- Performance optimization
- Memory leak fix
- Additional test coverage

Available in v1.4.0"
```

## 🔍 Recherche et filtrage d'issues fermées

### Voir les issues fermées

```bash
# Toutes les issues fermées
gh issue list --state closed

# Issues fermées récemment
gh issue list --state closed --search "closed:>$(date -d '7 days ago' +%Y-%m-%d)"

# Issues fermées par une personne
gh issue list --state closed --search "closed-by:alice"
```

### Analyser les fermetures

```bash
# Issues fermées comme résolues vs non planifiées
gh issue list --state closed --json number,title,stateReason --jq '.[] | select(.stateReason == "COMPLETED")'

gh issue list --state closed --json number,title,stateReason --jq '.[] | select(.stateReason == "NOT_PLANNED")'

# Performance de résolution (temps moyen)
gh issue list --state closed --json number,createdAt,closedAt --jq '.[] | "\(.number): \((.closedAt | fromdateiso8601) - (.createdAt | fromdateiso8601)) seconds"'
```

## 🎨 Automation de la fermeture

### Fermeture automatique par commit

**Configuration dans vos messages de commit** :

```bash
# Mots-clés qui ferment automatiquement
git commit -m "Fix user login validation

Fixes #42"

git commit -m "Implement dark mode feature

Closes #156
Resolves #157"

git commit -m "Update installation documentation

Close #203"
```

**Mots-clés reconnus** :
- `Fixes #123`
- `Closes #123`
- `Resolves #123`
- `Fix #123`
- `Close #123`
- `Resolve #123`

### Script de fermeture en masse

```bash
#!/bin/bash
# mass-close.sh

echo "🧹 Fermeture en masse d'issues obsolètes"

# Issues très anciennes sans activité
OLD_ISSUES=$(gh issue list --search "updated:<$(date -d '60 days ago' +%Y-%m-%d)" --state open --json number)

echo "$OLD_ISSUES" | jq -r '.[].number' | while read issue_number; do
    echo "Issue #$issue_number : ancienne sans activité"

    # Demander confirmation pour chaque issue
    gh issue view "$issue_number" --json title,updatedAt | \
      jq -r '"Titre: \(.title)\nDernière MAJ: \(.updatedAt)"'

    read -p "Fermer cette issue ? (y/n/q): " -n 1 -r
    echo

    case $REPLY in
        [Yy])
            gh issue close "$issue_number" --reason "not planned" \
              --comment "Issue fermée automatiquement - aucune activité depuis 60 jours. Rouvrir si toujours pertinente."
            echo "✅ Issue #$issue_number fermée"
            ;;
        [Qq])
            echo "❌ Arrêt du script"
            break
            ;;
        *)
            echo "⏭️ Issue #$issue_number ignorée"
            ;;
    esac
done
```

### Script de nettoyage par labels

```bash
#!/bin/bash
# cleanup-by-labels.sh

echo "🏷️ Nettoyage par labels"

# Fermer tous les doublons
echo "Fermeture des doublons..."
gh issue list --label "duplicate" --state open --json number | \
  jq -r '.[].number' | \
  while read issue_number; do
    gh issue close "$issue_number" --reason "not planned" \
      --comment "Fermé automatiquement - marqué comme doublon"
    echo "✅ Doublon #$issue_number fermé"
  done

# Fermer les issues marquées "wont-fix"
echo "Fermeture des issues 'wont-fix'..."
gh issue list --label "wont-fix" --state open --json number | \
  jq -r '.[].number' | \
  while read issue_number; do
    gh issue close "$issue_number" --reason "not planned" \
      --comment "Fermé automatiquement - marqué comme 'wont-fix'"
    echo "✅ Issue wont-fix #$issue_number fermée"
  done

echo "✅ Nettoyage par labels terminé"
```

## 📊 Métriques et analyse

### Métriques de vélocité

```bash
#!/bin/bash
# velocity-metrics.sh

echo "📊 Métriques de vélocité - $(date +%Y-%m-%d)"
echo "==========================================="

# Issues fermées cette semaine
WEEK_CLOSED=$(gh issue list --state closed --search "closed:>$(date -d '7 days ago' +%Y-%m-%d)" --json number | jq length)

# Issues ouvertes cette semaine
WEEK_OPENED=$(gh issue list --search "created:>$(date -d '7 days ago' +%Y-%m-%d)" --json number | jq length)

# Ratio fermeture/ouverture
RATIO=$(echo "scale=2; $WEEK_CLOSED / $WEEK_OPENED" | bc -l)

echo ""
echo "📈 Cette semaine :"
echo "  Issues fermées: $WEEK_CLOSED"
echo "  Issues ouvertes: $WEEK_OPENED"
echo "  Ratio: $RATIO"

if (( $(echo "$RATIO > 1" | bc -l) )); then
    echo "  ✅ Bon rythme - plus de fermetures que d'ouvertures"
elif (( $(echo "$RATIO == 1" | bc -l) )); then
    echo "  ⚖️ Équilibre - rythme stable"
else
    echo "  ⚠️ Attention - backlog qui s'accumule"
fi

# Temps moyen de résolution
echo ""
echo "⏱️ Temps moyen de résolution :"
gh issue list --state closed --search "closed:>$(date -d '30 days ago' +%Y-%m-%d)" \
  --json createdAt,closedAt | \
  jq -r '.[] | ((.closedAt | fromdateiso8601) - (.createdAt | fromdateiso8601)) / 86400' | \
  awk '{ sum += $1; count++ } END { if (count > 0) print "  " sum/count " jours" }'
```

### Analyse des raisons de fermeture

```bash
#!/bin/bash
# closure-analysis.sh

echo "🔍 Analyse des raisons de fermeture"
echo "==================================="

# Issues fermées comme complétées
COMPLETED=$(gh issue list --state closed --json stateReason | \
  jq '[.[] | select(.stateReason == "COMPLETED")] | length')

# Issues fermées comme non planifiées
NOT_PLANNED=$(gh issue list --state closed --json stateReason | \
  jq '[.[] | select(.stateReason == "NOT_PLANNED")] | length')

TOTAL=$((COMPLETED + NOT_PLANNED))

echo ""
echo "📊 Répartition des fermetures :"
echo "  Complétées: $COMPLETED ($(( COMPLETED * 100 / TOTAL ))%)"
echo "  Non planifiées: $NOT_PLANNED ($(( NOT_PLANNED * 100 / TOTAL ))%)"

echo ""
echo "🎯 Top 5 des issues récemment fermées comme complétées :"
gh issue list --state closed --json number,title,stateReason | \
  jq -r '.[] | select(.stateReason == "COMPLETED") | "#\(.number) - \(.title)"' | \
  head -5

echo ""
echo "🚫 Top 5 des issues récemment fermées comme non planifiées :"
gh issue list --state closed --json number,title,stateReason | \
  jq -r '.[] | select(.stateReason == "NOT_PLANNED") | "#\(.number) - \(.title)"' | \
  head -5
```

## 💡 Bonnes pratiques

### Quand fermer une issue

#### ✅ Fermer immédiatement quand :
- **Travail terminé** et testé
- **Bug confirmé** comme corrigé
- **Doublon** identifié
- **Hors scope** évident

#### ⏳ Attendre avant de fermer quand :
- **Tests en cours** (laisser ouvert jusqu'à validation)
- **Déploiement pending** (fermer après mise en production)
- **Feedback attendu** (laisser ouvert pour retours)

### Messages de fermeture efficaces

#### ✅ Bons messages

```bash
# Spécifique et informatif
gh issue close 42 --comment "Bug corrigé dans le commit abc123. Tests passés sur Chrome, Firefox et Safari. Fix déployé en production."

# Avec context pour l'équipe
gh issue close 156 --comment "Fonctionnalité implémentée selon specs. Code reviewé par @senior-dev. Documentation mise à jour. Prêt pour démonstration client."

# Raison claire pour fermeture non planifiée
gh issue close 203 --comment "Fonctionnalité reportée suite au pivot produit vers l'API mobile. Peut être réévaluée en Q3 selon feedback utilisateurs."
```

#### ❌ Messages insuffisants

```bash
# Trop vague
gh issue close 42 --comment "Done"

# Pas d'explication
gh issue close 156 --comment "Fixed"

# Pas de contexte
gh issue close 203 --comment "Not needed anymore"
```

### Gestion de l'historique

#### Préserver le contexte

```bash
# Lier aux commits/PRs
gh issue close 42 --comment "Résolu par PR #156 (commit a1b2c3d).

Changements apportés :
- Validation côté client améliorée
- Messages d'erreur plus clairs
- Tests ajoutés pour edge cases

Merge vers main effectué, déploiement prévu vendredi."
```

#### Documenter les décisions

```bash
# Expliquer le "pourquoi"
gh issue close 89 --reason "not planned" --comment "Après discussion équipe, cette approche présente des risques de sécurité.

Alternative retenue : OAuth2 avec providers externes (issue #234).

Décision validée par @tech-lead et @security-team."
```

## 📋 Récapitulatif des commandes essentielles

### Fermeture

```bash
# Fermeture simple
gh issue close 42

# Fermeture avec commentaire
gh issue close 42 --comment "Description de la résolution"

# Fermeture avec raison
gh issue close 42 --reason completed
gh issue close 42 --reason "not planned"
```

### Réouverture

```bash
# Réouverture simple
gh issue reopen 42

# Réouverture avec commentaire
gh issue reopen 42 --comment "Raison de la réouverture"
```

### Consultation

```bash
# Issues fermées
gh issue list --state closed

# Issues fermées récemment
gh issue list --state closed --search "closed:>2024-01-01"

# Toutes les issues (ouvertes + fermées)
gh issue list --state all
```

### Automatisation

```bash
# Fermeture par commit (dans le message)
git commit -m "Fix login bug

Fixes #42"

# Fermeture en masse par label
gh issue list --label "duplicate" --state open --json number | \
  jq -r '.[].number' | \
  xargs -I {} gh issue close {} --reason "not planned"
```

## 🎯 Prochaine étape

Parfait ! Vous maîtrisez maintenant le cycle de vie complet des issues. Il est temps de découvrir la fonctionnalité la plus puissante : les liens entre issues et commits qui créent une traçabilité parfaite entre vos besoins et votre code.

👉 **Continuez avec** : [Liens entre issues et commits](04-liens-entre-issues-et-commits.md)

---

*Vous avez terminé la section 4.3 ! Vous savez maintenant gérer le cycle de vie complet des issues. Dans la prochaine section, nous explorerons l'intégration puissante entre issues et développement.*

⏭️
