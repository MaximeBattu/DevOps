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

### 1-2 Why do we need a multistage build? And explain each step of this dockerfile
