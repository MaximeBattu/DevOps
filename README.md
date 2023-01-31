# TP1 : Découverte de docker

## Mise en place de la base de données pgsql

```Dockerfile
FROM postgres:14.1-alpine

ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd

COPY dumps/ /docker-entrypoint-initdb.d/
```

### 1-1 Document your database container essentials: commands and Dockerfile.

Network : `docker network create app-network <br>`
Admnier : `docker run -p 8090:8080 --net=app-network --name=adminer -d adminer` <br>
Lancement du postgres : `docker run -P --name [Nom de ton app] -d --net=app-network [USERNAME]/[Nom de ton app]`

Persistance des données : `docker run -P --name [Nom de ton app] -d --net=app-network -v /my/own/datadir:/var/lib/postgresql/data [USERNAME]/[Nom de ton app]`


### 1-2 Why do we need a multistage build? And explain each step of this dockerfile

Le multistage build permet de séparer les différentes étapes de la construction de l'image. Cela permet de ne pas avoir à installer des dépendances inutiles dans l'image finale. Par exemple, dans le cas d'un projet Java, on peut installer Maven dans l'image de build, puis copier le jar compilé dans l'image finale qui n'aura besoin que de Java pour fonctionner.

Dockerfile multistage :
```Dockerfile
# Build
FROM maven:3.8.6-amazoncorretto-17 AS myapp-build
# Création d'une variable d'environnement pour le chemin d'accès au dossier de l'app
ENV MYAPP_HOME /opt/myapp
# Définition du dossier de travail
WORKDIR $MYAPP_HOME
# Copie des fichiers de dépendances et de code source
COPY pom.xml .
COPY src ./src
# Compilation de l'app
RUN mvn package -DskipTests

# Run
FROM amazoncorretto:17
# Création d'une variable d'environnement pour le chemin d'accès au dossier de l'app
ENV MYAPP_HOME /opt/myapp
# Définition du dossier de travail
WORKDIR $MYAPP_HOME
# Copie du jar compilé depuis l'étape précédente (myapp-build)
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar

# Lancement de l'app
ENTRYPOINT java -jar myapp.jar
```

### 1-3 Document docker-compose most important commands.

Il y a deux commandes à utiliser lors de l'utilisation de docker-compose :
- `docker compose build` : Permet de construire les images à à partir d'un fichier `docker-compose.yml` qui donne les emplacement de chaque Dockerfile pour chacun des services (API/DB/Web)
- `docker compose up` : Permet de lancer les services définis dans le fichier `docker-compose.yml` et de les lier entre eux. Il est possible de lancer les services en arrière plan avec l'option `-d`

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