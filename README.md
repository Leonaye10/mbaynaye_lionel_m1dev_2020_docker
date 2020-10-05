# Projet Docker

## Les Objectifs

1. Créer un/des fichier(s) DockerFile(s) pour builder notre image pour les serveurs de production
2. Créer un fichier docker-compose.yml pour démarrer nos containers, nos services
3. Modifier la documentation qui va expliquer le contenu de ces deux fichiers :
  a. DockerFile(s) pour builder notre image
  b. Docker-compose pour démarrer le(s) container(s) (Bien décrire l’ensemble des différentes étapes d’exécution des commandes associés aux services)

## Les fichiers DockerFiles

Les fichiers DockerFiles nous permettent de builder les images sur les serveurs en productions

### DockerFile FrontEnd

```dockerfile
# On crée une couche à partir de l'image Node avec la version 14
FROM node:12
# On définis le répertoire de travail ici qui est le frontend
WORKDIR /app
# Ici on rajoute tous les packages json dans le répertoire de base
COPY package*.json ./
# Ici on le module sous Node
RUN npm install
# On copie tous les fichiers du répertoire
COPY . .
# On définis notre port
EXPOSE 3000
# Lors du lancement du conteneur on lance la commande
CMD ["npm", "start"]
```

### DockerFile BackEnd

```dockerfile
# On crée une couche à partir de l'image Node avec la version 14
FROM node:12
# On définis le répertoire de travail ici qui est le backtend
WORKDIR /app
# Ici on rajoute tous les packages json dans le répertoire de base
COPY package*.json ./
# Ici on le module sous Node
RUN npm install
# On copie tous les fichiers du répertoire
COPY . .
# On définis notre port
EXPOSE 8080
# Lors du lancement du conteneur on lance la commande
CMD ["npm", "start"]
```

## Fichier Docker-Compose

Ce fichier permet de décrire et gérer plusieurs conteneurs comme un ensemble de services

```docker-compose
# Definir la version du fichier
version: "3.5"

# Cette permet de dire qu'il y aura plusieurs services
services:
# Le service FrontEnd
    frontend:
# On se base sur l'image node:14
        image: node:14
# Nous permet de builder l'image du fichier docekerFile qui se trouve dans le répertoire frontend
        build: ./frontend
# C'est pour démarrer automatiquement le conteneur en cas de redémarrage du serveur
        restart: always
# On mappe le port 3000 sur le port 3000
        ports:
            - "3000:3000"
# On indique la dépendance du service. Donc le backend démarre avant le frontend
        depends_on: 
            - backend
# Le service BackEnd
    backend:
# On se base sur l'image node:14
        image: node:14
# Nous permet de builder l'image du fichier docekerFile qui se trouve dans le répertoire frontend
        build: ./backend
# C'est pour démarrer automatiquement le conteneur en cas de redémarrage du serveur
        restart: always
# On mappe le port 3000 sur le port 3000
        ports:
            - "8080:8080"
# On indique la dépendance du service. Donc le backend démarre avant le frontend
        depends_on:
            - mongo
# Le service Mongo
    mongo:
        image: mongo
        ports:
            - 27017:27017
```

## Les étapes du projet

La premier des choses a faire dans ce projet est de créer un fichier dockerFile pour chaque partie du projet (FrontEnd et Backend). Ensuite, il faut créer un fichier Docker-Compose qu'on mettra a la racine du projet. Le docker compose nous permettra de builder plusieurs services en retrouvant les deux fichiers DockerFiles.
