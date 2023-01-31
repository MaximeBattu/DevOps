# TP1 : Découverte de docker

## Mise en place de la base de données pgsql

```Dockerfile
FROM postgres:14.1-alpine

ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd

COPY dumps/ /docker-entrypoint-initdb.d/
```

Network : docker network create app-network <br>
Admnier : docker run -p 8090:8080 --net=app-network --name=adminer -d adminer <br>
Lancement du postgres : docker run -P --name [Nom de ton app] -d --net=app-network [USERNAME]/[Nom de ton app]

Persistance des données : `docker run -P --name [Nom de ton app] -d --net=app-network -v /my/own/datadir:/var/lib/postgresql/data [USERNAME]/[Nom de ton app]`

### 1-1 Document your database container essentials: commands and Dockerfile.

### 1-2 Why do we need a multistage build? And explain each step of this dockerfile

### 1-3 Document docker-compose most important commands. 

### 1-4 Document your docker-compose file.

```yaml
version: '3.7'

# Création des services
services:
      # Service backend | api
      backend:
         # Chemin vers le dockerfile
         build: /api/
         # Nom du container : api
         container_name: api
         # Réseau associé
         networks:
            - app-network
         # Dépendence du container : database
         depends_on:
            - database
      # Service database
      database:
         # Chemin vers le dockerfile
         build: /data/
         # Nom du container : database
         container_name: database
         # Réseau associé
         networks:
            - app-network
      # Service httpd
      httpd:
         # Chemin vers le dockerfile
         build: /http/
         # Nom du container : apache
         container_name: apache
         # Port exposé
         ports:
            - 80:80
         # Réseau associé
         networks:
            - app-network
         # Dépendence du container : backend
         depends_on:
            - backend

# Création du réseau
networks:
      app-network:
         # Configuration par défaut
         driver: bridge
```

### 1-5 Document your publication commands and published images in dockerhub.

Création des tags
- `docker tag docker-database maximebattu/docker-database:1.0`
- `docker tag docker-backend maximebattu/docker-api:1.0`
- `docker tag docker-httpd maximebattu/docker-web:1.0`

Push des images sur docker hub
- `docker push docker-database:1.0`
- `docker push docker-api:1.0`
- `docker push docker-web:1.0`

Images sur le [Docker Hub](https://hub.docker.com/u/maximebattu)

> Tips : Why do we put our images into an online repo ? (à compléter)

Il est important de mettre ses images sur une répertoire en ligne, tel que Docker Hub pour tout simplement pouvoir réutiliser, partager ou récupérer des images fonctionnelles entre différents postes / personnes.

Cela permete également de versionner différentes versions d'une images, avec l'évolution du technologie (passage de pgsql 14 à 15) et d'améliorer les performances d'installation. Ces images sont stockées sur différents serveurs très performants qui faciliteront le téléchargement des différentes images stockées.

Cela peut s'avérer utile, si par exemple j'ai besoin d'une image docker pour une base de données postegres sql. Autant réutilisé une image déjà existante qui est déjà configurée et où le travail sera moindre.