# Projet Docker


## Objectif du projet

1 - Relier le backend avec la base de données Mongo

2 - Créer des fichiers DockerFiles pour le backend et le frontend afin de pouvoir builder des images sur le serveurs

3 - Créer un fichier Docker-Compose afin de builder plusieurs images en même en tant que des services

Bonus ==> Mettre a jour les images sur la registry en public

## Backend avec Mongo

Tout d'abord, il faut créer un compte sur MongoDb distant comme Atlas. Ensuite il faut récupérer le lien de la base de données avec le mot de passe afin de faire la connexion avec le backend. Le lien est souvent présenté comme suit :
```javascript
'mongodb+srv://<user_name>:<password>@cluster0.aydot.mongodb.net/test'
```  
La connexion avec mongoose aussi est fait de cette maniere : 
```javascript
mongoose
  .connect(
    'mongodb+srv://<user_name>:<password>@cluster0.aydot.mongodb.net/test', { useNewUrlParser: true , useUnifiedTopology: true }
  )
  .then(result => {
    app.listen(8080);
  })
  .catch(err => console.log(err));
```  

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

## Fichier Docker-Compose


Ce fichier permet de décrire et gérer plusieurs conteneurs comme un ensemble de services. Il doit se trouver à la racine du projet

```dockercompose
# Definir la version du fichier
version: "3.5"

# Cette permet de dire qu'il y aura plusieurs services
services:
# Le service FrontEnd
    frontend:
# On se base sur l'image node:12
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

## Les commandes pour chaque étape

Une fois tous les fichiers créer, il faut entré cette commande dans le terminale pour builder les images a l'aide du Docker-Compose.

```shell
docker-compose up --build
```

En lançant la commande, on retrouvera les images du backend et frontend. En même temps, le backend et le frontend seront lancé sur le serveur et ils seront accessibles sur leur port respectifs.

Avec la commande ci-dessous, on pourra vérifier si les images ont bien été builder
```shell
docker images
```

## Mettre à jour l'image sur le public

Créer un compte DockerHub afin d'avoir un username pour se loger afin de mettre ajour les images sur le public

Premièrement, il faut se loger avec le terminale avec la commande : 

```shell
docker login
```

Deuxièmement, à l'aide de l'ID des images, il faut tager l'image

```shell
docker tag <ID de l'image> usernameDockerhub/<nomDeImage>:<tag>
```

Enfin, il faut mettre à jour les images sur le registry avec la commande suivante : 

```shell
docker push usernameDockerHub/<nomDeImage>
```
