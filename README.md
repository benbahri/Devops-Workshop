## Introduction
Ce workshop vise à simplifier la culture DevOps pour les debutants


## Setup your environment

Pour ce workshop, nous aurons besoin de java 8, maven, git et docker. Tout ces requis sont disponibles pour les systèmes d'exploitation Linux et Windows

### Install Java 8

#### On Linux Centos/RHEL
<details>
  <summary>Click to expand!</summary>

1.	Nous utiliserons open java pour notre démo, Obtenez la dernière version de http://openjdk.java.net/install/
  ```
  sudo yum install java-1.8*
  ```

2.	Configuration de la variable d'environnement JAVA_HOME
  1.	Déterminez le bon emplacement de la version JAVA8.
  ```
  find /usr/lib/jvm/java-1.8* | head -n 3
  ```
  2.	Créez un fichier appelé java.sh dans le répertoire /etc/profile.d/
  ```
  vi /etc/profile.d/java.sh
  ```
  3.	Ajoutez le contenu suivant
  ```
  #!/bin/bash
  export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.282.b08-1.el7_9.x86_64
  export PATH=$PATH:$JAVA_HOME
  ```
  NB: vous devez changer le chemin du java /usr/lib/jvm/java-1.8.0-*****x86_64. Par le chemin généré dans la commande précédente.
  4.	Enregistrez et fermez le fichier. Rendez-le exécutable à l'aide de la commande suivante.
  ```
  chmod +x /etc/profile.d/java.sh
  ```
  5.	Ensuite, définissez les variables d'environnement de manière permanente en exécutant la commande suivante :
  ```
  source /etc/profile.d/java.sh
  ```
  6.	Maintenant, vérifiez la version de fourmi en utilisant la commande :
  ```
  java -version
  ```

</details>

#### On Linux Ubuntu
<details>
  <summary>Click to expand!</summary>

- Pour installer OpenJDK 8, exécutez la commande suivante :
```
sudo apt install openjdk-8-jdk
```
- Vérifiez que celui-ci est installé avec :

  ```
  java -version
  ```

  Vous verrez une sortie de ce type :

  Output
  ```
  openjdk version "1.8.0_162"
  OpenJDK Runtime Environment (build 1.8.0_162-8u162-b12-1-b12)
  OpenJDK 64-Bit Server VM (build 25.162-b12, mixed mode)
  ```
  Il est également possible d'installer uniquement le JRE, ce que vous pouvez faire en exécutant sudo apt install openjdk-8-jre.

-	Configuration de la variable d'environnement JAVA_HOME
  1.	Déterminez le bon emplacement de la version JAVA8.
  ```
  find /usr/lib/jvm/java-1.8* | head -n 3
  ```
  2.	Créez un fichier appelé java.sh dans le répertoire /etc/profile.d/
  ```
  vi /etc/profile.d/java.sh
  ```
  3.	Ajoutez le contenu suivant
  ```
  #!/bin/bash
  export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.282.b08-1.el7_9.x86_64
  export PATH=$PATH:$JAVA_HOME
  ```
  NB: vous devez changer le chemin du java /usr/lib/jvm/java-1.8.0-*****x86_64. Par le chemin généré dans la commande précédente.
  4.	Enregistrez et fermez le fichier. Rendez-le exécutable à l'aide de la commande suivante.
  ```
  chmod +x /etc/profile.d/java.sh
  ```
  5.	Ensuite, définissez les variables d'environnement de manière permanente en exécutant la commande suivante :
  ```
  source /etc/profile.d/java.sh
  ```
  6.	Maintenant, vérifiez la version de fourmi en utilisant la commande :
  ```
  java -version
  ```

</details>

### Install maven

#### On Windows
<details>
  <summary>Click to expand!</summary>

Etape 1: Télécharger Maven
- Télécharger Maven depuis son site web officiel https://maven.apache.org/download.cgi/
- Choisir une version adaptée au système d’exploitation hôte.

Etape 2: Installer Maven
- Ouvrez l'invite de commande.
- À partir de l'invite de commande, accédez au répertoire où le fichier apache-maven-x.x.x-bin.zip est enregistré et décompressez l’archive sous le chemin :
```
C:\maven\apache-maven-x.x.x
```
- Exécutez les commandes suivantes pour paramétrer les variables d’environnement requises pour le fonctionnement de Maven:
 - Définir la variable d'environnement JAVA_HOME :
  ```
  SET JAVA_HOME=”C:\Program Files\Java\jdk1.8.0_xxx”
  ```
 - Définir la variable d'environnement M2_HOME :
  ```
  SET M2_HOME=C:\integ_continue\maven\apache-maven-x.x.x
  ```
 - Définir la variable d'environnement M2 :
  ```
  SET M2=%M2_HOME%\bin
  ```
 - Ajouter les commandes Maven dans le PATH du système d’exploitation :
  ```
  SET PATH=%PATH%;%M2%
  ```

- Testez l’installation de Maven en exécutant la commande:
```
mvn -version
```
</details>


#### On Linux (Centos/RHEL/Ubuntu/Debian)
<details>
  <summary>Click to expand!</summary>

- Télécharger Maven depuis son site web officiel https://maven.apache.org/download.cgi/
- Choisir et télécharger une version adaptée au système d’exploitation hôte.
```
wget https://downloads.apache.org/maven/maven-3/3.8.0/binaries/apache-maven-3.8.0-bin.tar.gz
```
- Extraire l'archive de distribution dans le dossier /opt
```
tar xzvf apache-maven-* -C /opt
```
- Créez un fichier appelé maven.sh dans le répertoire /etc/profile.d/
```
vi /etc/profile.d/maven.sh
```
- Ajoutez le contenu suivant
```
#!/bin/bash

export M2_HOME=/opt/apache-maven-3.6.3
export M2=$M2_HOME/bin
export PATH=$PATH:$M2
```
NB: vous devez changer le chemin de l’outil maven par celui installé dans votre instance.
- Enregistrez et fermez le fichier. Rendez-le exécutable à l'aide de la commande suivante.
```
chmod +x /etc/profile.d/maven.sh
```
- Ensuite, définissez les variables d'environnement de manière permanente en exécutant la commande suivante :
```
source /etc/profile.d/maven.sh
```
- Maintenant, vérifiez la version de fourni en utilisant la commande :
```
mvn -version
```
</details>
