# TP Docker_4_Compose - Ynov DevOps B3


## **Rendu :** Un fichier MD : DEVOPS_DOCKER4_[NOM]\_[PRENOM].md

Vous avez un template de rendu dans le repo. 
Pour chaque étape, documenter vos actions : 

        Screenshot commande + output
        Explication d'une ou 2 ligne sur ce que fait la commande
        
A chaque exercice rajouter un titre et le nom de l'exercice. La syntaxe ainsi que l'upload de l'image sont décrit dans des liens en haut du template.

:bangbang::bangbang: Pensez à renommer le fichier avec votre **Nom** et **Prénom**

:sparkles: Une fois le TP et le rendu terminé commitez et pushez le dans le repo. :sparkles:
  
### Tips   
Si vous avez des problèmes sur une command utilisez `docker [command] --help`.

:raising_hand: Si vous avez des soucis n'hésitez pas à m'appeler. 

## Avant Exercice : 
Pensez à installer `docker-compose` : 

Pour Linux : 
```bash 
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

Pour les autres : https://docs.docker.com/compose/install/ 

## Exercice 1 : Wordpress

Vous souhaitez simplifier l'installation des Wordpress de vos clients.
   
Chaque client a besoin de : 
- une base de données MySQL ;
- le système de fichiers Wordpress.

Vous souhaitez faire en sorte que si le container se stop en cas de soucis, il se relance automatiquement. Pour que vous n'ayez pas besoin d'avoir à réaliser d'action. 

Dans votre premier service `db` il vous faudra : 

- qu'il redémarre automatiquement en cas de crash
- Faire un volume persistent pour sauvegarder les données du site Wordpress dans un fichier dédié pour un client
- Définir en tant que d'envvar le `Username`, `Password`, `Databse name`, `Root password`

Dans le second service `Wordpress` il faudra : 
- Le container WP doit démarrer si le container `db` démarre
- Le WP devra être accessible depuis le port 8000
- Il faudra renseigner en variable d'environnement les informations nécessaire pour se connecter à la base de données

## Exercice 2 : Stack ELKT

La stack Elastic permet de centraliser tout les logs de nos containers  au même endroit, nous allons la coupler à Trafiek pour faciliter la gestion de nos containers.

- Télécharger la configuration Filebeat : `curl -L -O https://raw.githubusercontent.com/elastic/beats/7.10/deploy/docker/filebeat.docker.yml`
- `mv filebeat.docker.yml filebeat.yml && chown root filebeat.yml`
- Rajouter au docker-compose existant le service trafiek
  - image: traefik:v2.3
  - command: --api.insecure=true --providers.docker
  - ports: 80 et 8080
  - volumes: /var/run/docker.sock:/var/run/docker.sock
- Expliquer en quelques mots le fichier `docker.sock`
- BONUS : Expliquez pourquoi le partage de ce fichier impacte la sécurité du serveur. 


