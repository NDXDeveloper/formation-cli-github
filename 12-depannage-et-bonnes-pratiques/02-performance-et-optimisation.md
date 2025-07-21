🔝 Retour au [Sommaire](/SOMMAIRE.md)

# 12.2 Performance et optimisation

## Introduction

La performance de la CLI GitHub peut varier selon plusieurs facteurs : la taille de vos repositories, votre connexion réseau, la configuration de votre système, et la façon dont vous utilisez l'outil. Cette section vous apprendra à identifier les goulots d'étranglement et à optimiser votre expérience pour une utilisation plus fluide et rapide.

## Comprendre les facteurs de performance

### Sources de lenteur courantes

**Facteurs réseau :**
- Connexion internet lente ou instable
- Latence élevée vers les serveurs GitHub
- Limites de bande passante
- Problèmes de proxy ou firewall

**Facteurs API :**
- Limites de taux d'API atteintes
- Requêtes inefficaces avec trop de données
- Appels API redondants
- Timeout mal configurés

**Facteurs locaux :**
- Cache corrompu ou surchargé
- Configuration sous-optimale
- Extensions lentes ou buggées
- Système de fichiers lent

**Facteurs de données :**
- Repositories très volumineux
- Historique Git complexe
- Nombreuses branches ou tags
- Gros fichiers binaires

### Identifier les problèmes de performance

#### Mesurer le temps d'exécution

```bash
# Mesurer le temps d'une commande
time gh repo list

# Exemple de sortie
# real    0m2.345s
# user    0m0.123s
# sys     0m0.045s
```

#### Mode debug pour analyser les requêtes

```bash
# Voir les requêtes API en détail
GH_DEBUG=1 gh repo list

# Analyser seulement les requêtes HTTP
GH_DEBUG=1 gh repo list 2>&1 | grep "HTTP"

# Mesurer les temps de réponse
GH_DEBUG=1 time gh repo list
```

#### Identifier les commandes lentes

```bash
# Tester différentes commandes
time gh repo list --limit 10
time gh repo list --limit 100
time gh issue list
time gh pr list
```

## Optimisation des requêtes API

### Limiter les résultats

La façon la plus simple d'améliorer les performances est de limiter la quantité de données récupérées :

```bash
# Limiter le nombre de résultats
gh repo list --limit 20           # Au lieu de tous les repos
gh issue list --limit 50          # Au lieu de toutes les issues
gh pr list --limit 30             # Au lieu de toutes les PRs

# Utiliser des filtres pour réduire les données
gh issue list --state open        # Seulement les issues ouvertes
gh pr list --author @me           # Seulement vos PRs
gh repo list --visibility public  # Seulement les repos publics
```

### Sélectionner uniquement les champs nécessaires

Avec le format JSON, demandez seulement les données dont vous avez besoin :

```bash
# Au lieu de récupérer tous les champs
gh repo list --json

# Sélectionnez seulement ce qui vous intéresse
gh repo list --json name,description,stargazerCount

# Pour les issues
gh issue list --json number,title,state
# Au lieu de --json (qui récupère tout)

# Pour les PRs
gh pr list --json number,title,author,state
```

### Utiliser le cache intelligemment

```bash
# Sauvegarder les résultats pour éviter les requêtes répétées
gh repo list --json name,url > repos_cache.json

# Utiliser le cache au lieu de requêter à nouveau
cat repos_cache.json | jq -r '.[].name'

# Renouveler le cache seulement quand nécessaire
# Par exemple, une fois par jour
```

## Optimisation de la configuration

### Configuration réseau optimale

```bash
# Augmenter les timeouts si vous avez une connexion lente
export GH_REQUEST_TIMEOUT=60s

# Utiliser SSH au lieu de HTTPS (souvent plus rapide)
gh config set git_protocol ssh

# Configurer Git pour de meilleures performances
git config --global core.preloadindex true
git config --global core.fscache true
git config --global gc.auto 256
```

### Optimisation de la pagination

```bash
# Désactiver la pagination pour les scripts
gh config set pager ""
export GH_PAGER=cat

# Ou utiliser un pager rapide
gh config set pager "less -F -X"
```

### Optimisation de l'éditeur

```bash
# Utiliser un éditeur léger pour les tâches rapides
gh config set editor nano

# Ou configurer VS Code pour s'ouvrir rapidement
gh config set editor "code --wait --new-window"
```

## Gestion du cache

### Comprendre le cache GitHub CLI

La CLI GitHub utilise un cache pour améliorer les performances :

```bash
# Localisation du cache
echo $HOME/.cache/gh/

# Voir la taille du cache
du -sh ~/.cache/gh/
```

### Nettoyage du cache

```bash
# Nettoyer complètement le cache
rm -rf ~/.cache/gh/

# Nettoyer sélectivement (selon les dossiers présents)
rm -rf ~/.cache/gh/api_cache/
```

### Optimisation du cache

```bash
# Vérifier si le cache cause des problèmes
mv ~/.cache/gh/ ~/.cache/gh_backup/
# Tester les performances sans cache
time gh repo list

# Restaurer si les performances sont pires
mv ~/.cache/gh_backup/ ~/.cache/gh/
```

## Optimisation des extensions

### Identifier les extensions lentes

```bash
# Lister les extensions installées
gh extension list

# Tester les performances avec et sans extensions
time gh repo list

# Désinstaller temporairement les extensions suspectes
gh extension remove slow-extension
time gh repo list  # Tester à nouveau
```

### Extensions recommandées pour la performance

```bash
# Extensions qui améliorent la productivité sans ralentir
gh extension install dlvhdr/gh-dash    # Dashboard efficace
gh extension install mislav/gh-branch  # Gestion de branches optimisée
```

### Éviter les extensions problématiques

- Extensions qui font beaucoup d'appels API
- Extensions non maintenues
- Extensions avec des bugs de performance
- Extensions qui modifient la configuration globale

## Optimisation pour les gros repositories

### Stratégies pour les projets volumineux

```bash
# Utiliser des clones superficiels
gh repo clone owner/big-repo -- --depth 1

# Limiter l'historique Git
git config --global clone.defaultRemoteName origin
git config --global fetch.prune true
```

### Filtrage intelligent

```bash
# Filtrer par organisation pour réduire le bruit
gh repo list --org your-org

# Utiliser des recherches spécifiques
gh repo list --search "language:javascript"
gh issue list --search "assignee:@me state:open"
```

### Pagination manuelle

```bash
# Au lieu de récupérer toutes les pages d'un coup
gh repo list --limit 100 | head -20

# Utiliser la pagination native
gh api repos/owner/repo/issues --paginate | jq '.[].title'
```

## Optimisation réseau

### Configuration proxy et firewall

```bash
# Si vous êtes derrière un proxy
export HTTP_PROXY=http://proxy.company.com:8080
export HTTPS_PROXY=http://proxy.company.com:8080

# Vérifier la connectivité
curl -I https://api.github.com
```

### Optimisation DNS

```bash
# Vérifier la résolution DNS
nslookup github.com
nslookup api.github.com

# Utiliser des DNS rapides (8.8.8.8, 1.1.1.1)
# Configuration système selon votre OS
```

### Test de connectivité

```bash
# Tester la latence vers GitHub
ping github.com

# Tester la bande passante
curl -w "@curl-format.txt" -o /dev/null -s https://github.com/

# Fichier curl-format.txt :
#      time_namelookup:  %{time_namelookup}\n
#         time_connect:  %{time_connect}\n
#      time_appconnect:  %{time_appconnect}\n
#     time_pretransfer:  %{time_pretransfer}\n
#        time_redirect:  %{time_redirect}\n
#   time_starttransfer:  %{time_starttransfer}\n
#                     ----------\n
#           time_total:  %{time_total}\n
```

## Scripts d'optimisation

### Script de diagnostic de performance

```bash
#!/bin/bash
# gh-perf-test.sh - Test de performance GitHub CLI

echo "🔍 Diagnostic de performance GitHub CLI"
echo "======================================"

echo ""
echo "📊 Informations système :"
echo "Version CLI : $(gh --version)"
echo "Configuration Git : $(git --version)"
echo "Cache size : $(du -sh ~/.cache/gh/ 2>/dev/null || echo 'Pas de cache')"

echo ""
echo "🌐 Test de connectivité :"
echo "Ping GitHub : $(ping -c 1 github.com | grep 'time=')"

echo ""
echo "⚡ Tests de performance :"

echo -n "repo list (limité) : "
time gh repo list --limit 10 2>/dev/null | wc -l

echo -n "issue list (limité) : "
time gh issue list --limit 10 2>/dev/null | wc -l

echo -n "API rate limit : "
time gh api rate_limit 2>/dev/null | jq '.rate.remaining'

echo ""
echo "✅ Diagnostic terminé"
```

### Script d'optimisation automatique

```bash
#!/bin/bash
# gh-optimize.sh - Optimisation automatique

echo "🚀 Optimisation GitHub CLI"
echo "========================="

# Nettoyer le cache si trop volumineux
CACHE_SIZE=$(du -s ~/.cache/gh/ 2>/dev/null | cut -f1)
if [ "$CACHE_SIZE" -gt 100000 ]; then
    echo "🧹 Nettoyage du cache (trop volumineux)"
    rm -rf ~/.cache/gh/
fi

# Configuration optimale
echo "⚙️ Configuration optimale"
gh config set git_protocol ssh
gh config set pager cat

# Vérifier les extensions
echo "🔌 Vérification des extensions"
EXTENSIONS=$(gh extension list | wc -l)
if [ "$EXTENSIONS" -gt 5 ]; then
    echo "⚠️ Beaucoup d'extensions installées ($EXTENSIONS)"
    echo "Consider removing unused extensions"
    gh extension list
fi

echo "✅ Optimisation terminée"
```

## Monitoring et benchmarking

### Créer des benchmarks personnalisés

```bash
# Créer un fichier de benchmark
cat > gh-benchmark.sh << 'EOF'
#!/bin/bash

echo "Benchmark GitHub CLI - $(date)"
echo "================================"

# Test 1 : Lister les repositories
echo -n "Repos (10) : "
time gh repo list --limit 10 > /dev/null

# Test 2 : Lister les issues
echo -n "Issues (10) : "
time gh issue list --limit 10 > /dev/null

# Test 3 : API rate limit
echo -n "Rate limit : "
time gh api rate_limit > /dev/null

echo "Benchmark terminé"
EOF

chmod +x gh-benchmark.sh
```

### Suivre les performances dans le temps

```bash
# Créer un log de performance
./gh-benchmark.sh >> performance.log

# Analyser les tendances
grep "real" performance.log | tail -10
```

## Optimisation pour différents cas d'usage

### Pour les scripts automatisés

```bash
# Configuration optimale pour scripts
export GH_PROMPT_DISABLED=1
export NO_COLOR=1
export GH_PAGER=cat

# Utiliser des timeouts courts
export GH_REQUEST_TIMEOUT=30s

# Limiter les données
gh repo list --limit 50 --json name,url
```

### Pour l'usage interactif

```bash
# Configuration pour l'usage humain
gh config set pager "less -R"
export GH_PAGER="less -R"

# Utiliser des alias courts
gh alias set r "repo list --limit 20"
gh alias set i "issue list --limit 20"
gh alias set p "pr list --limit 20"
```

### Pour les équipes

```bash
# Configuration partagée optimisée
gh config set git_protocol ssh
gh config set prompt enabled

# Alias d'équipe optimisés
gh alias set team-repos "repo list --org team-name --limit 50"
gh alias set my-work "pr list --search 'assignee:@me' --limit 20"
```

## Résolution de problèmes de performance

### Diagnostic étape par étape

```bash
# 1. Identifier la commande lente
time gh slow-command

# 2. Tester avec des paramètres réduits
time gh slow-command --limit 10

# 3. Vérifier sans cache
rm -rf ~/.cache/gh/
time gh slow-command

# 4. Tester sans extensions
gh extension list
# Désinstaller temporairement les extensions suspectes

# 5. Mode debug
GH_DEBUG=1 gh slow-command 2>&1 | grep -E "(HTTP|time)"
```

### Solutions par ordre de priorité

1. **Limiter les données** : `--limit`, `--json` avec champs spécifiques
2. **Nettoyer le cache** : `rm -rf ~/.cache/gh/`
3. **Optimiser la configuration** : timeouts, protocoles
4. **Gérer les extensions** : supprimer celles non utilisées
5. **Améliorer le réseau** : DNS, proxy, connectivité

## Bonnes pratiques de performance

### Développement de scripts

- ✅ Toujours utiliser `--limit` dans les scripts
- ✅ Sauvegarder les résultats pour éviter les requêtes répétées
- ✅ Utiliser `--json` avec des champs spécifiques
- ✅ Gérer les timeouts et erreurs
- ❌ Ne pas faire de boucles avec des appels API

### Usage quotidien

- ✅ Nettoyer régulièrement le cache
- ✅ Limiter le nombre d'extensions
- ✅ Utiliser des alias pour les commandes fréquentes
- ✅ Configurer des timeouts appropriés
- ❌ Ne pas récupérer plus de données que nécessaire

### Monitoring

- ✅ Surveiller la taille du cache
- ✅ Tester régulièrement les performances
- ✅ Documenter les optimisations qui marchent
- ✅ Partager les bonnes pratiques avec l'équipe

---

**🎯 Points clés à retenir :**
- La performance dépend principalement de la quantité de données récupérées
- Utilisez toujours `--limit` et des champs spécifiques avec `--json`
- Le cache peut être un allié ou un problème selon sa gestion
- Les extensions peuvent significativement impacter les performances
- Le réseau est souvent le goulot d'étranglement principal

**💡 Règle d'or :** Récupérez seulement les données dont vous avez vraiment besoin, quand vous en avez besoin.

**➡️ Prochaine étape :** Dans la section 12.3, nous explorerons la sécurité et la gestion des accès avec la CLI GitHub.

⏭️
