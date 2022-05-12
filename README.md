# docker-compose


## Déploiement en production : Ansible

Pour déployer sur le serveur de production de Statutis, nous utilisons le rôle Ansistrano déclenché par une Github Action, qui va se connecter sur le serveur de production et déployer notre application, en utilisant notamment Docker et Docker Compose.

## Déploiement en local

Pour déployer l'application statutis sur votre machine, vous devez disposer de Docker et Docker Compose.

### Lancement de l'api

Pour démarrer le serveur de l'api, il suffit d'utiliser le fichier `docker-compose.yaml`.

```bash
docker-compose up -d
```

Cela aura pour effet de démarrer 3 conteneurs :
 - Une base de données Postgres
 - Le conteneur de la tâche planifiée
 - Le serveur de l'API, exposant un serveur web sur le port 3001

Vous pouvez donc par la suite accéder à la documentation de l'API : `http://localhost:3001/swagger`.

***EN PRODUCTION : il faut absolument modifier la variable d'environnement `JWT__secret` du conteneur `api`.***


### Lancement du site frontend

Il faut tout d'abord cloner le dépôt:

```bash
git clone https://github.com/Statutis/frontend.git
```

Puis modifier le fichier d’environnement `.env`, afin d'indiquer l'url du site et de l'API. 
Voici ce fichier dans notre cas : 

```env
APP_URL=http://localhost:3001
APP_API_URL=http://localhost:3000
```

Une fois la configuration achevée, il ne nous reste plus qu'à compiler et lancer notre application

```bash
docker run --rm -it -v "$(pwd):/app" -w "/app" node yarn
docker run --rm -it -v "$(pwd):/app" -w "/app" node yarn build
docker run --rm -it -v "$(pwd):/app" -w "/app" -p 3001:3001 node yarn preview --port 3001 --host
```

On peut alors accéder au site : `http://localhost:3000`

***EN PRODUCTION : Il vaut mieux utiliser un serveur web comme NGINX.***
