# DevOps
Docker

TP:
1-1: Utiliser le flag -e permet de ne pas stocker les mots de passe en clair dans l’image, ce qui améliore la sécurité. Il offre aussi plus de flexibilité en permettant de modifier les valeurs sans changer le Dockerfile. Enfin, cela évite de devoir créer plusieurs images pour différents environnements.

1-2: On a besoin de créer un volume relié à notre container postgres pour pouvoir sauvegarder certaines données même si les images sont éffacées.

1-3: 

Dockerfile : 

FROM postgres:14.1-alpine

ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd

COPY sql/ /docker-entrypoint-initdb.d/

Construire l’image:

docker build -t database .

Créer un volume pour persister les données :

docker volume create pgdata

Lancer le conteneur database :

docker run --name database -e POSTGRES_DB=db -e POSTGRES_USER=usr -e POSTGRES_PASSWORD=pwd -v pgdata:/var/lib/postgresql/data -d database

Vérifier que le conteneur tourne :

docker ps

Se connecter à la base de données :

docker exec -it database psql -U usr -d db

Vérifier les données insérées :

docker exec -it database psql -U usr -d db -c "SELECT * FROM students;"

Arrêter et supprimer le conteneur

docker stop database && docker rm database

Relancer le conteneur avec les données persistantes

docker run --name database -e POSTGRES_DB=db -e POSTGRES_USER=usr -e POSTGRES_PASSWORD=pwd -v pgdata:/var/lib/postgresql/data -d database

1-4: Cela permet de séparer la compilation et l'exécution, réduisant ainsi la taille de l'image finale en n'incluant que le nécessaire pour l'exécution.

1-5: Un reverse proxy améliore la sécurité, la répartition de charge et cache les requêtes pour optimiser les performances. Il sert aussi d’intermédiaire entre les clients et les serveurs backend.

1-6: Docker Compose simplifie la gestion des conteneurs en permettant de définir, configurer et exécuter plusieurs services avec un seul fichier YAML. Il facilite le déploiement et l’orchestration.

1-7: -docker compose up (-d)
     -docker compose down
     -docker compose ps

1-8: version: '3.7'

services:
    simpleapinet:
        build:
            ./Backend API
        networks:
            - app-network
        depends_on:
            - database

    database:
        build:
            ./Database
        networks:
            - app-network

    httpserv:
        build:
            ./HTTP server
        ports:
            - "80:80"
        networks:
            - app-network
        depends_on:
            - simpleapinet

networks:
    app-network:

1-9: 
docker login
docker build -t <dockerhub_username>/<image_name>:<tag> .
docker images
docker tag <image_id> <dockerhub_username>/<image_name>:<tag>
docker push <dockerhub_username>/<image_name>:<tag>

1-10: On met nos images dans un dépôt en ligne pour les rendre accessibles partout, faciliter la collaboration, gérer les versions, automatiser les déploiements et assurer leur sauvegarde.



Github


2-1: Testcontainers est une bibliothèque Java permettant de lancer des conteneurs Docker pour les tests d'intégration. Elle facilite la mise en place d'environnements de test reproductibles en exécutant des bases de données, des files de messages ou d'autres services nécessaires aux tests directement dans des conteneurs Docker.

2-2: name: CI devops 2025

on:
  #to begin you want to launch this job in main and develop
  push:
    branches: 
    - main 
  pull_request:

jobs:
  test-backend: 
    runs-on: ubuntu-22.04
    steps:
     #checkout your github code using actions/checkout@v2.5.0
      - uses: actions/checkout@v4

     #do the same with another action (actions/setup-java@v3) that enable to setup jdk 21
      - name: Set up JDK 21
        uses: actions/setup-java@v3
        with:
          distribution: 'corretto'
          java-version: '21'

     #finally build your app with the latest command
      - name: Build and test with Maven
        run: mvn clean verify
        working-directory: simple-api

Définition explicite des branches main et develop pour le déclenchement du workflow.
Mise à jour de l'action actions/checkout vers la version @v4.
Ajout de la configuration de Java avec actions/setup-java@v3, en spécifiant corretto comme distribution et 21 comme version de JDK.
Ajout de la commande mvn clean verify pour compiler et tester l'application, avec un working-directory défini sur simple-api.

2-3: Pousser les images Docker permet d'automatiser le déploiement, garantir la cohérence des environnements, faciliter la collaboration et intégrer l'application dans un pipeline CI/CD pour une mise en production efficace.

2-4:  mvn -B verify sonar:sonar -Dsonar.projectKey=MathieuMonier_tp-devops-correction-docker -Dsonar.organization=mathieumonier -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=${{ secrets.SONAR_TOKEN }}
Cette commande compile, teste le projet et analyse la qualité du code avec SonarCloud. Elle utilise un jeton sécurisé pour authentifier l’analyse et envoie les résultats à SonarCloud.

-Dsonar.projectKey=MathieuMonier_tp-devops-correction-docker :

Définit l'identifiant unique du projet sur SonarCloud.
-Dsonar.organization=mathieumonier :

Spécifie l'organisation SonarCloud à laquelle appartient le projet.
-Dsonar.host.url=https://sonarcloud.io :

Indique l'URL de SonarCloud, qui héberge l'analyse.
-Dsonar.token=${{ secrets.SONAR_TOKEN }} :

Utilise un jeton d'authentification sécurisé stocké dans les secrets GitHub Actions pour autoriser l'analyse.

  
