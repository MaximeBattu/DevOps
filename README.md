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

### 1-5 Document your publication commands and published images in dockerhub.
