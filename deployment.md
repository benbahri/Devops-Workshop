Le déploiement de l'application nécessite une environnement d'exécution convenable respectant les requis de l'application en termes de dépendances et de leurs versions.
Pour cela, il est préférable d'éviter l'installation des environnements d'exécution (serveurs d'application, serveurs de bases de données ...) sur le système d'exploitation, et utiliser à la place des conteneurs Docker


### Vue d'ensemble
Dans ce didacticiel, nous allons apprendre à déployer un fichier Java WAR dans un conteneur Docker .

Nous allons déployer le fichier WAR sur Apache Tomcat , un serveur Web gratuit et open source largement utilisé dans la communauté Java.

## Installer Docker

### Windows: install Docker Desktop
[https://docs.docker.com/desktop/windows/install/](https://docs.docker.com/desktop/windows/install/)

### Ubuntu : Install using the repository

- Set up the repository
Update the apt package index and install packages to allow apt to use a repository over HTTPS:
```
 sudo apt-get update
 sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```
- Add Docker’s official GPG key:
```
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
Use the following command to set up the stable repository. To add the nightly or test repository, add the word nightly or test (or both) after the word stable in the commands below. Learn about nightly and test channels.
```
 echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
- Install Docker Engine
Update the apt package index, and install the latest version of Docker Engine and containerd, or go to the next step to install a specific version:
```
 sudo apt-get update
 sudo apt-get install docker-ce docker-ce-cli containerd.io
```

### Déployer un fichier WAR sur Tomcat
WAR (Web Application Archive) est un fichier d'archive compressé qui regroupe tous les fichiers liés aux applications Web et leur structure de répertoires.

Pour simplifier les choses, déployer un fichier WAR sur Tomcat n'est rien d'autre que copier ce fichier WAR dans le répertoire de déploiement du serveur Tomcat. Le répertoire de déploiement sous Linux est $CATALINA_HOME/webapps . $CATALINA_HOME désigne le répertoire d'installation du serveur Tomcat.

Après cela, nous devons redémarrer le serveur Tomcat , qui extraira le fichier WAR dans le répertoire de déploiement.

### Déployer WAR dans Docker Container
Supposons que nous ayons un fichier WAR pour notre application, `calculator.war` , que nous devons déployer sur le serveur Tomcat.

Pour atteindre notre objectif, nous devons d'abord créer un Dockerfile. Ce Dockerfile inclura toutes les dépendances nécessaires pour exécuter notre application.

De plus, nous allons créer une image Docker à l'aide de ce Dockerfile suivi de l'étape de lancement du conteneur Docker.

Plongeons maintenant dans ces étapes une par une.

1. Créer un fichier Dockerfile

Nous utiliserons la dernière image Docker de Tomcat comme image de base pour notre Dockerfile. L'avantage d'utiliser cette image est que toutes les dépendances/packages nécessaires sont pré-installés. Par exemple, si nous utilisons les dernières images Docker Ubuntu/CentOS, nous devons installer manuellement Java, Tomcat et les autres packages requis.

Étant donné que tous les packages requis sont déjà installés, il nous suffit de copier le fichier WAR, calculator.war , dans le répertoire de déploiement du serveur Tomcat. C'est ça!

Regardons de plus près :

```
$ cat Dockerfile
FROM tomcat

COPY target/calculator.war /usr/local/tomcat/webapps/
```
$CATALINA_HOME/webapps désigne le répertoire de déploiement de Tomcat . Ici, CATALINA_HOME pour l'image Docker officielle de Tomcat est  /usr/local/tomcat . En conséquence, le répertoire de déploiement complet s'avérerait être  /usr/local/tomcat/webapps .

L'application que nous avons utilisée ici est très simple et ne nécessite aucune autre dépendance.

2. Créer l'image Docker

Créons maintenant l'image Docker en utilisant le Dockerfile que nous venons de créer :
```
$ docker build -t calculator .
Sending build context to Docker daemon  19.97kB
Step 1/2 : FROM tomcat
 ---> 710ec5c56683
Step 2/2 : COPY target/calculator.war /usr/local/tomcat/webapps/
 ---> Using cache
 ---> 8b132ab37a0e
Successfully built 8b132ab37a0e
Successfully tagged calculator:latest
```
La commande docker build créera une image Docker avec une étiquette `calculator`.

Assurez-vous de créer l'image Docker à partir du répertoire où se trouve le Dockerfile. Dans notre exemple ci-dessus, nous sommes dans le répertoire `calculator` lorsque nous construisons l'image Docker.

3. Exécuter le conteneur Docker

Jusqu'à présent, nous avons créé un Dockerfile et construit une image Docker à partir de celui-ci. Exécutons maintenant le conteneur Docker :
```
$ docker run -itd -p 8080:8080 --name my_application_container calculator
e90c61fdb4ac85b198903e4d744f7b0f3c18c9499ed6e2bbe2f39da0211d42c0
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
e90c61fdb4ac        calculator               "catalina.sh run"   6 seconds ago       Up 5 seconds        0.0.0.0:8080->8080/tcp   my_application_container
```
Cette commande lancera un conteneur Docker avec le nom  my_application_container en utilisant l'image Docker `calculator`.

Le port par défaut du serveur Tomcat est 8080. Par conséquent, lors du démarrage du conteneur Docker, assurez-vous de toujours lier le port de conteneur 8080 à tout port hôte disponible. Nous avons utilisé le port hôte 8080 pour plus de simplicité ici.

## Intégrer docker dans gitlab ci

Maintenant nous avons le fichier Dockerfile faisant partie des fichiers du projet. Il reste à modifier le pipeline ci pour la prise en charge de la génération du conteneur à la suite de la construction.

- Ajouter les deux étapes package et deploy dans la liste des étapes:
```
  - package
  - deploy
```

- Ajouter une action de packaging pour préparer le fichier calculator.war
```
packaging-job:
  stage: package
  script:
  - mvn package
```

- Ajouter une action pour le build du conteneur à partir du Dockerfile:
```
deploying-job:
  stage: deploy
  script:
  - docker build -t calculator .
```
