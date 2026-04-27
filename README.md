# Valorisation Donnée Météo

[![CI Pipeline](https://github.com/lydia-bedri/infoclimat/actions/workflows/ci.yml/badge.svg)](https://github.com/lydia-bedri/infoclimat/actions/workflows/ci.yml)
[![Scorecard](https://api.scorecard.dev/projects/github.com%2FLydia-BEDRI%2FInfoClimat/badge)](https://scorecard.dev/viewer/?uri=github.com/Lydia-BEDRI/InfoClimat)
[![OpenSSF Best Practices](https://bestpractices.coreinfrastructure.org/projects/12672/badge)](https://bestpractices.coreinfrastructure.org/projects/12672)

Projet Data For Good - Saison 14

## Structure du projet

```text
├── backend/    # API et traitement des données (Python)
├── frontend/   # Interface utilisateur
```

## Pour commencer

Consultez les README de chaque sous-projet :

- [Backend](backend/README.md)
- [Frontend](frontend/README.md)

## Docker Hardened Images

Le projet utilise désormais les images Docker Hardened suivantes pour les builds de production :

- Backend Python: `dhi.io/python:3.14-dev` pour le build, `dhi.io/python:3.14` pour le runtime
- Frontend Node.js: `dhi.io/node:24-dev` pour le build, `dhi.io/node:24` pour le runtime

Ces images servent de base aux Dockerfiles multi-stage du backend et du frontend afin de réduire la surface d'attaque et de rendre les conteneurs plus proches d'un usage production.

### Prérequis

Les images hardened sont tirées depuis le registry `dhi.io`. Avant de builder les images Docker du projet, il faut être authentifié :

```bash
docker login dhi.io
```

### Build production

```bash
docker login dhi.io
docker compose -f docker-compose.prod.yml build backend frontend
docker compose -f docker-compose.prod.yml up -d
```

Le backend expose ses métriques sur `http://localhost:8000/metrics` et via Nginx sur `http://localhost:8080/metrics`.

### Build développement (sans DHI)

```bash
docker compose -f docker-compose.dev.yml build backend frontend
docker compose -f docker-compose.dev.yml up -d
```

## Observabilité (Prometheus + Grafana)

La stack de développement inclut Prometheus et Grafana pour l'observabilité des métriques.

### Lancer la stack

```bash
docker compose -f docker-compose.dev.yml up -d --build
```

### URLs utiles

| Service                      | URL                             | Credentials   |
| ---------------------------- | ------------------------------- | ------------- |
| Frontend                     | <http://localhost:8080>         | -             |
| API backend (via Nginx)      | <http://localhost:8080/api/v1/> | -             |
| Endpoint metrics (via Nginx) | <http://localhost:8080/metrics> | -             |
| Endpoint metrics (direct)    | <http://localhost:8000/metrics> | -             |
| Prometheus UI                | <http://localhost:9090>         | -             |
| Grafana UI                   | <http://localhost:3000>         | admin / admin |

### Configuration Prometheus

Le fichier de configuration est dans [monitoring/prometheus/prometheus.yml](monitoring/prometheus/prometheus.yml).

**Cibles configurées :**

- Service backend sur `backend:8000`
- Endpoint `/metrics` exposé par Django (via `django-prometheus`)
- Scraping toutes les 15 secondes par défaut

**Vérifier les cibles :**

1. Ouvrir <http://localhost:9090/targets>
2. La cible `backend-django` doit être en état **UP**

### Dashboard Grafana

**Provisionnement automatique :**

- Datasource Prometheus créée automatiquement au démarrage
- Dashboard "InfoClimat Backend Observability" chargé depuis [monitoring/grafana/dashboards/backend-observability.json](monitoring/grafana/dashboards/backend-observability.json)

**Accéder au dashboard :**

1. Ouvrir <http://localhost:3000>
2. Se connecter avec les identifiants `admin / admin`
3. Accéder à **Dashboards** → **InfoClimat Backend Observability**

### Générer des métriques

Les métriques sont collectées automatiquement sur chaque requête API. Pour voir les graphes alimentés :

```bash
for i in {1..10}; do
  curl -s http://localhost:8080/api/v1/stations/ > /dev/null
done

```

### Format des métriques

Le format Prometheus est accessible en texte brut :

```bash
curl http://localhost:8000/metrics
# Ou via Nginx :
curl http://localhost:8080/metrics
```

Exemple de métriques disponibles :

- `django_http_requests_total_by_method` : nombre total de requêtes par méthode HTTP
- `django_http_responses_total_by_status` : nombre de réponses par code HTTP
- `django_http_request_duration_seconds_*` : durée des requêtes
- Métriques Python standard : GC, threads, etc.

## Contribuer

Ce projet fait partie de la saison 14 de Data For Good.

## Utilisation de Git

Le projet utilise la branche `main` comme branche principale.

## Workflow de contribution

Lire attentivement nos bonnes pratiques de développement : [Branches et commits : Workflow de contribution](https://outline.services.dataforgood.fr/doc/onboarding-dev-OFGKWOcxOn)

Extraits :

### :tanabata_tree:Branches et commits : Workflow de contribution

Pour que tout le monde adopte les mêmes pratiques, nous avons posé des principes relatifs aux branches et aux commits. A lire impérativement avant de commencer.

#### 0. **Paramétrer git**

```bash
git config --local pull.rebase merges
git config --local rebase.autostash true
```

- `pull.rebase merges` applique vos commits locaux par-dessus le remote.
- `rebase.autostash true` permet de stasher automatiquement vos changements locaux non commités avant de faire un pull/rebase, et de les réappliquer après. Cela évite les conflits liés à des changements locaux non commités lors du pull/rebase.

#### 1. **Créer une branche depuis** `**main**`

```bash
git checkout main
git pull origin main
git checkout -b <type>/(<scope>/)?<description-courte>
```

Convention de nommage des branches :

- `feat/scope/nom-feature` : nouvelle fonctionnalité
- `fix/scope/nom-bug` : correction de bug
- `docs/sujet` : documentation
- `refactor/sujet` : refactoring de code
- `chore/sujet` : tâche de maintenance

Exemple : `feat/itn/ajout-carte-meteo` ou `fix/ecarts-normales/erreur-chargement-donnees`

#### 2. **Faire des commits atomiques**

Un commit atomique = une seule modification logique. Cela permet de :

- Faciliter la relecture du code
- Simplifier un éventuel rollback
- Garder un historique clair

```bash
git add <fichiers-concernés>
git commit -m "<type>: (<scope>:)? <description>"
```

Format des messages de commit :

- `feat: itn: ajoute le composant carte météo`
- `fix: ecarts normales: corrige l'affichage des températures négatives`
- `docs: readme: mise à jour installation`
- `refactor: parser: simplifie la logique de parsing`
- `test: parser: ajoute les tests unitaires`
- `chore: npm: met à jour les dépendances`

Vous n'êtes pas obligé d'utiliser le terminal, vous pouvez utiliser n'importe quelle interface graphique, notamment celle de VSCode et JetBrains.

#### 3. **Pousser sa branche et créer une Pull Request (PR)**

```bash
git push origin <nom-de-ta-branche>
```

Puis sur GitHub, créer une PR vers `main` en :

- Donnant un titre clair et descriptif
- Remplissant le template de PR
- Assignant des reviewers

#### 4. **Review de code**

Chaque PR doit être relue par au moins une personne avant d'être mergée.

En tant que reviewer :

- Vérifier que le code fonctionne et respecte les conventions du projet
- Poser des questions si quelque chose n'est pas clair
- Proposer des améliorations de manière constructive

En tant qu'auteur :

- Répondre aux commentaires
- Effectuer les modifications demandées
- Demander une nouvelle review si nécessaire

#### 5. **Merge avec squash commit**

Une fois la PR approuvée, on merge en utilisant **"Squash and merge"** sur GitHub. Cela combine tous les commits de la branche en un seul commit sur `main`, ce qui garde un historique propre.

### Bonnes pratiques

- **Synchroniser régulièrement** sa branche avec `main` pour éviter les conflits :

```bash
git checkout main
git pull origin main
git checkout <ta-branche>
git rebase main
```

- **Ne jamais pusher directement sur** `**main**`
- **Garder ses PRs petites** : une PR = une fonctionnalité ou un fix. Les grosses PRs sont difficiles à relire
- **Tester son code** avant de pousser

### :male_technologist:Éditeur de code

Nous conseillons (surtout pour les débutants) de travailler avec [Visual Studio Code](https://code.visualstudio.com/) (VSCode pour les intimes).
Voici un [tuto](https://data-for-good.slack.com/archives/C08B329AG7M/p1738330293159749) pour l'usage de VSCode, l’installation de Python, et faire tourner son premier notebook dans VSCode.

Pour les plus avancés, nous conseillons la suite JetBrains, notamment WebStorm et PyCharm en version gratuite.

Pensez à activer le formattage et le fix automatique lors de la sauvegarde :

- [VSCode](.vscode/settings.json)
- JetBrains : Tools → Actions on Save :
  - Reformat Code
  - Optimize Imports
  - Run eslint --fix
  - Run Prettier

## Installation des pre-commit hooks

Ce projet utilise [pre-commit](https://pre-commit.com/) pour automatiser la vérification de la qualité du code avant chaque commit.

### Installer pre-commit sur votre machine

#### Via pip

```bash
pip install pre-commit
```

### Activer les hooks

```bash
# À la racine du projet
pre-commit install
```

### Configuration

Le projet utilise deux configurations de pre-commit :

1. **Configuration racine** (`.pre-commit-config.yaml`) :
   - Exécute les hooks backend et frontend
   - Vérifie les conflits de merge, les fins de ligne, etc.

2. **Configuration backend** (`backend/.pre-commit-config.yaml`) :
   - Utilise Ruff pour le linting et le formatting Python
   - Ignore le code DJ001 (docstring pour les classes privées)

### Exécution manuelle

Pour exécuter tous les hooks sur tous les fichiers :

```bash
pre-commit run --all-files
```

Pour exécuter uniquement les hooks backend :

```bash
cd backend && uv run pre-commit run --all-files --config=.pre-commit-config.yaml
```

Pour exécuter uniquement les hooks frontend :

```bash
cd frontend && npm run check
```

### Résolution des problèmes courants

#### Problème d'environnement Node.js

Si vous obtenez une erreur "eslint: command not found" :

```bash
cd frontend
npm install --legacy-peer-deps
```

### Outils utilisés

- **Backend** : Ruff (linting + formatting)
- **Frontend** : ESLint + Prettier
- **Commun** : vérification des conflits, fins de ligne, etc.
