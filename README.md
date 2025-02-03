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

