# Projet Docker

## Objectif du projet

1 - Relier le backend avec la base de données Mongo

2 - Créer des fichiers DockerFiles pour le backend et le frontend afin de pouvoir builder des images sur le serveurs

3 - Créer un fichier Docker-Compose afin de builder plusieurs images en meme en tant que des services

Bonus ==> Mettre a jour les images sur la registry en public


## Les fichiers DockerFiles

Les fichiers DockerFiles nous permettent de builder les images sur les serveurs en productions

### DockerFile FrontEnd

```dockerfile
# On crée une couche à partir de l'image Node avec la version 14
FROM node:14
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
# On crée une couche à partir de l'image Node avec la version 12
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

+## Fichier Docker-Compose

Ce fichier permet de décrire et gérer plusieurs conteneurs comme un ensemble de services

```docker-compose
# Definir la version du fichier
version: "3.5"

# Cette permet de dire qu'il y aura plusieurs services
services:
# Le service FrontEnd
    frontend:
# On se base sur l'image node:12
        image: node:12
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
# On se base sur l'image node:12
        image: node:12
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

