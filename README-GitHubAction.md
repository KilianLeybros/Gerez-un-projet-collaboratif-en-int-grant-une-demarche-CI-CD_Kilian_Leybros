# Documentation Technique du Pipeline CI/CD BobApp

Ce document décrit le pipeline CI/CD mis en place pour **BobApp**, l'automatisation des tests, l'analyse de code et la construction des images Docker.

## Déclencheurs du Pipeline

Le pipeline est déclenché par les événements suivants :

- **pull_request** sur la branche `master`
- **push** sur la branche `master`

## Jobs du Pipeline

### 1. Job Backend

#### Description

Ce job exécute les tests pour le backend de l'application et effectue une analyse de code avec SonarCloud.
Il s'exécute uniquement lors d'une **pull_request** sur la branche `master`.

#### Étapes

- **Récupération du code**
    - Utilise `actions/checkout@v4` pour récupérer le code source du dépôt.

- **Java Setup**
    - Configure l'environnement Java avec la version 17 en utilisant `actions/setup-java@v4`.

- **Installation des dépendances et test backend**
    - Exécute `mvn clean install` pour installer les dépendances Maven et exécuter les tests.

- **Génération d'un rapport JUnit dans le pipeline**
    - Utilise `phoenix-actions/test-reporting@v8` pour générer un rapport de test JUnit dans le pipeline.

- **Récupération du rapport de couverture XML**
    - Utilise `actions/upload-artifact@v4` pour télécharger le rapport de couverture de code au format XML.

- **Vérification de l'existence du projet SonarCloud**
    - Vérifie si le projet est déjà présent dans SonarCloud et le crée si ce n'est pas le cas.

- **Mise en cache de SonarCloud**
    - Met en cache les fichiers SonarCloud pour accélérer les futures analyses avec `actions/cache@v4`.

- **Analyse de SonarCloud pour le backend**
    - Exécute l'analyse de code avec SonarCloud pour le backend.

### 2. Job Frontend

#### Description

Ce job exécute les tests pour le frontend de l'application et effectue également une analyse de code avec SonarCloud.
Il s'exécute uniquement lors d'une **pull_request** sur la branche `master`.

#### Étapes

- **Récupération du code**
    - Utilise `actions/checkout@v4` pour récupérer le code source.

- **Node.js Setup**
    - Configure l'environnement Node.js avec la version 20 en utilisant `actions/setup-node@v4`.

- **Java Setup**
    - Configure Java pour les dépendances potentielles avec `actions/setup-java@v4`.

- **Installation des dépendances**
    - Exécute `npm install` pour installer les dépendances du projet frontend.

- **Test frontend**
    - Exécute `npm run test` pour tester le frontend.

- **Génération d'un rapport JUnit dans le pipeline**
    - Utilise `phoenix-actions/test-reporting@v8` pour générer un rapport de test JUnit dans le pipeline.

- **Récupération des rapports de couverture**
    - Utilise `actions/upload-artifact@v4` pour télécharger les rapports de couverture de tests.

- **Vérification de l'existence du projet SonarCloud**
    - Vérifie et crée le projet dans SonarCloud si nécessaire.

- **Mise en cache de SonarCloud**
    - Met en cache les fichiers SonarCloud pour les futures analyses.

- **Analyse de SonarCloud pour le frontend**
    - Exécute l'analyse de code avec SonarCloud pour le frontend.

### 3. Job Docker Build

#### Description

Ce job construit les images Docker et les publie sur Docker Hub uniquement lors d'un **push** sur la branche `master`.

#### Étapes

- **Checkout du code**
    - Utilise `actions/checkout@v4` pour récupérer le code source.

- **Installation de Docker Compose**
    - Exécute des commandes pour installer Docker Compose.

- **Connexion à Docker Hub**
    - Utilise `docker/login-action@v3` pour se connecter à Docker Hub avec des informations d'authentification stockées en secret.

- **Build des images Docker**
    - Exécute `docker-compose -f docker-compose.yaml build` pour construire les images.

- **Tag des images Docker**
    - Tag les images Docker pour les préparer à la publication.

- **Pousser les images sur Docker Hub**
    - Publie les images Docker sur Docker Hub.

