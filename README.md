# student-list

Ce repo est une simple application simple pour lister les étudiants avec un serveur Web (PHP) et une API (Flask)

![project](https://user-images.githubusercontent.com/18481009/84582395-ba230b00-adeb-11ea-9453-22ed1be7e268.jpg)

------------

## Application

L'application sur laquelle vous allez travailler s'appelle "*student_list*", cette application est très basique et permet à POZOS d'afficher la liste des étudiants avec leur âge.

student_list a deux modules :

- le premier module est une API REST (avec authentification de base nécessaire) qui envoie la liste des souhaits de l'étudiant sur la base d'un fichier JSON
- Le deuxième module est une application Web écrite en HTML + PHP qui permet à l'utilisateur final d'obtenir une liste d'étudiants

Votre travail consiste à construire un conteneur pour chaque module et à les faire interagir les uns avec les autres

Les fichiers que vous devez fournir (dans votre livraison) sont ***Dockerfile*** et ***docker-compose.yml*** (actuellement les deux sont vides)

Il est maintenant temps de vous expliquer le rôle de chaque fichier :

- docker-compose.yml : pour lancer l'application (API et web app)
- Dockerfile : le fichier qui servira à construire l'image de l'API (des détails seront donnés)
- student_age.json : contient le nom de l'étudiant avec l'âge au format JSON
- student_age.py : contient le code source de l'API en python
- index.php : page PHP sur laquelle l'utilisateur final sera connecté pour interagir avec le service afin de lister les élèves par âge. Vous devez mettre à jour la ligne 29 avant d'exécuter le conteneur de site Web pour que ***api_ip_or_name*** et ***port*** correspondent à votre déploiement en utilisant des variables d'environnement php (regarder les lignes 20, 21 pour voir comment lire les env var en PHP).

## Build et test du conteneur simple_api

POZOS vous donnera des informations pour construire le conteneur API

- Image de base

Comme image de base vous devez utiliser "python:2.7-stretch"

- Mainteneur

N'oubliez pas de spécifier les informations du responsable

- Ajouter le code source

Vous devez copier le code source de l'API dans le conteneur à la racine "/" chemin

- Prérequis

L'API utilise le moteur FLASK, voici une liste du package que vous devez installer

```
apt-get update -y && apt-get install python-dev python3-dev libsasl2-dev python-dev libldap2-dev libssl-dev -y
pip install flask==1.1.2 flask_httpauth==4.1.0 flask_simpleldap python-dotenv==0.14.0
```

- Données persistantes

Créez un dossier de données à la racine "/data" où les données seront stockées.

Vous utiliserez ce dossier pour monter la liste des étudiants

- Port API

Pour interagir avec cette API, exposez le port 5000

- CMD

Lorsque le conteneur démarre, il doit exécuter student_age.py (copié à l'étape 4), il devrait donc ressembler à quelque chose comme

`CMD [ "python", "./student_age.py" ]`

Créez votre image et essayez de l'exécuter (n'oubliez pas de monter le fichier *student_age.json* à l'adresse */data/student_age.json* dans le conteneur), consultez les journaux et vérifiez que le conteneur écoute et est prêt à répondre

Exécutez cette commande pour vous assurer que l'API répond correctement

`curl -u toto:python -X GET <http://<adresse> IP de l'hôte>:<port exposé de l'API>/pozos/api/v1.0/get_student_ages

**Félicitations ! Vous êtes maintenant prêt pour la prochaine étape (docker-compose.yml)**

## Infrastructure As Code

Après avoir testé votre image d'API, vous devez tout assembler et la déployer à l'aide de docker-compose.

Le fichier ***docker-compose.yml*** va déployer deux services :

- website : l'interface utilisateur final avec les caractéristiques suivantes
  - image : php:apache
  - environnement : vous fournirez le USERNAME le PASSWORD, le nom du conteneur de l'api et son port pour permettre à l'application Web d'accéder à l'API via l'authentification
  - volumes : pour éviter que l'image php:apache ne s'exécute avec le site Web par défaut, nous allons lier le site Web donné par POZOS à utiliser. Vous devez avoir quelque chose comme `./site Web :/var/www/html`
  - depend_on : vous devez vous assurer que l'API démarre avant le site Web
  - port : ne pas oublier d'exposer le port
- API : l'image créée avant doit être utilisée avec les spécifications suivantes
  - image : le nom de l'image construite précédemment
  - volumes : vous monterez le fichier student_age.json dans /data/student_age.json
  - port : n'oubliez pas d'exposer le port

Supprimer votre conteneur créé précédemment

Exécutez votre docker-compose.yml

Enfin, accédez à votre site Web et cliquez sur le bouton "List Student"

**Si la liste des étudiants apparaît, vous avez réussi à dockeriser l'application POZOS !**
