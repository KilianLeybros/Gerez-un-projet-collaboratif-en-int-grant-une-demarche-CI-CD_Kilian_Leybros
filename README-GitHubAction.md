# Documentation du Pipeline CI/CD BobApp

## 1 Explication des étapes du pipeline GitHub Actions

### 1.1 Déclencheurs du Pipeline

Le pipeline est déclenché par les événements suivants :

- **pull_request** sur la branche `master`
- **push** sur la branche `master`

### 1.2 Jobs du Pipeline

#### 1.2.1 Job Backend

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

#### 1.2.2 Job Frontend

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

#### 1.2.3 Job Docker Build

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
 
## 2. KPIs Proposés

- **Code Coverage**
  - **Overall**
      - Coverage cible pour le frontend : 80 % (actuel : 83,3 %).
      - KPI cible pour le backend : 35% (actuel : 38,8 %).
  - **Nouveau code**
      - KPI cible pour le frontend : 80 %.
      - KPI cible pour le backend : 80 %.
- **Bugs**
  - Objectif pour les deux applications : 0 bug (actuel pour le backend : 1 bug détecté, note D).
- **Security Hotspots**
  - Objectif : 100 % de Security Hotspots examinés et résolus (actuel backend : 2 Hotspots détectés, note E).

## 3 Analyse des Métriques et des Retours Utilisateurs

### 3.1 Coverage
- **Backend** : La couverture du code back-end est de 38,8 %, ce qui est bien en dessous de l'objectif de 80 %. Cela laisse supposer que de nombreuses portions de code ne sont pas testées, ce qui augmente le risque de bugs.
- **Frontend** : La couverture du code front-end est de 83,3 %, ce qui est conforme.

![UserScore1](https://badgen.net/static/stars/★☆☆☆☆)
"Je mets une étoile car je ne peux pas en mettre zéro ! Impossible de poster une suggestion de blague, le boutton tourne et fait planter mon navigateur !"

L'avis de cet utilisateur laisse à penser qu'il y a potentiellement un bug côté front end, hors sonar cloud n'en a pas détecter, on peut tout de même tester l'application backend afin d'obtenir un taux de couverture de 80 %, ce qui corrigerai potentiellement ce problème et/ou exclurai la partie backend, un bug backend n'est pas à exclure

### 3.2 Bugs
- **Backend** : Un bug a été détecté dans la partie back-end, ce qui est préoccupant, car cela pourrait affecter la stabilité du projet.
- **Frontend** : Aucun bug détecté.

![UserScore2](https://badgen.net/static/stars/★★☆☆☆)
"#BopApp j'ai remonté un bug sur le post de vidéo il y a deux semaines et il est encore présent ! Les devs vous faites quoi ????"

Le bug détecté par SonarCloud côté back-end pourrait être la cause de cet avis utilisateur. De plus, l'utilisateur se plaint du temps d'attente pour voir un correctif déployé. La mise en place du pipeline CI/CD devrait améliorer cela en facilitant et en automatisant le déploiement de l'application. Cela rejoint également l'avis suivant :

![UserScore3](https://badgen.net/static/stars/★☆☆☆☆)
"Ca fait une semaine que je ne reçois plus rien, j'ai envoyé un email il y a 5 jours mais toujours pas de nouvelles..."

### 3.3 Security Hotspot

La présence de 2 Security Hotspots non résolus et d'une note E est critique dans l'application, surtout si celle-ci traite des données utilisateur. Il s'agit d'un problème à régler en priorité, de plus, l'un des deux problèmes de sécurité est lié au bug back-end détecté.

## 4. Recommandations
- **Prioriser la correction des Security Hotspots** : Les problèmes de sécurité doivent être corrigés en priorité, en raison de leur impact sur la fiabilité et la confiance des utilisateurs. De plus, l'un des deux Security Hotspots provient de la même source que le bug. Nous pouvons éventuellement corriger les deux problèmes en même temps.
- **Corriger le bug back-end** : Le bug back-end doivent être analysé et corrigé pour améliorer l'expérience utilisateur et la fiabilité de l'application.
- **Améliorer la couverture de test du backend** : La couverture du backend doit être augmentée au-delà de 80 % afin d'améliorer la robustesse et de minimiser les bugs.
- **Réfactoriser le code et mettre à jour les librairies** : Le code pourrait être refactorisé afin d'éviter le code semelle et d'avoir un code plus maintenable. Les bibliothèques du projet pourraient également être mises à jour afin de réduire la dette technique et de potentiels bugs provenant de bibliothèques obsolètes.

