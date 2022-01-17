Control your code versioning
---

## Init your project folder

Dans le repertoire de votre projet, commencez par initialiser un git repository local, qui va permettre de suivre les changements dans votre projet.
```
git init

Initialized empty Git repository in /path/to/your-project/.git/

```

- Télécharger le projet maven compressé [calculator.zip](calculator.zip)
- Décompresser le projet et accéder au répertoire calculator
```
unzip calculator.zip
cd calculator
```
- Initialiser un repo git à partir de ce répertoire
```
git init
```

## Add files and folders to staging area

Le staging area est une zone de préparation dans laquelle on indique les fichiers qui seront considérés dans le prochain commit.

Pour ajouter tout vos fichiers dans le staging area, exécutez la commande git add
```
git add .
```
comme vous pouvez spécifier les noms des fichiers :
```
git add file1 file2
```

- Dans le répertoire calculator, préparez votre staging area contenant les fichiers du projet:
```
git add .
```
- Vérifiez ce qui a été retenu dans le staging area avec la commande:
```
git status
```


## Commit your changes

Un commit valide les changements effectués sur l'espace de travail par un snapshot de l'état des fichiers stockés temporairement dans le staging area.

Un commit est distingué par un identifiant unique et une description.

Pour créer votre commit exécuter le commande:
```
git commit -m "describe your commit"
```

- Dans le répertoire calculator, commitez l'état initial du projet:
```
git commit -m "Initial commit - calculator servelet initialized"
```

## Evolute your code

Nous allons modifier notre projet en ajoutant un block de code dans le fichier `src/main/java/com/devops/calculator/CalculatorService.java` pour créer une fonction d'addition:

```java
    @GET
    @Path("add")
    @Produces(MediaType.APPLICATION_JSON)
    public CalculatorResponse Add(@QueryParam("x") int x, @QueryParam("y") int y) {
        return new CalculatorResponse(x, y, x + y);
    }
```

- Ajouter le fichier modifié dans le staging area:
```
git add src/main/java/com/devops/calculator/CalculatorService.java
```

- Commiter le changement
```
git commit -m "Create addition function"
```

## Git branching

La branche par défaut  est **master** ou **main**, en général elle contient le code validé du projet et elle est protégée à la modification que par le propriétaire ou les membres autorisés.

Git autorise la création des branches concurrentes ou temporaires pour développer un fonctionnalité ou tester un code, à partir d'un commit spécifique.

On peut merger deux branches pour mixer leurs codes ensemble.

- Créez une branche à partir de **master**, appelée **operations**
```
git branch operations
```
- Changez le head sur la branche **operations**
```
git checkout operations
```
- Ajoutez le code suivant dans la classe CalculatorService:
```Java
    @GET
    @Path("sub")
    @Produces(MediaType.APPLICATION_JSON)
    public CalculatorResponse Sub(@QueryParam("x") int x, @QueryParam("y") int y) {
        return new CalculatorResponse(x, y, x - y);
    }
```
- Créez un commit dans la branche **operations**
```
git add src/*
git commit -m "Create substruction function"
```
- Retournez vers la branche **master**, vous trouvez que le nouveau code est disparu, parce qu'il fait partie seulement de la branche **operations** jusqu'à présent:
```
git checkout master
```

### Merger deux branches (Fast-forward)
Si le code de la nouvelle opération est validé, vous pouvez l'ajouter  à la branche **master**.
- Vérifier que vous êtes toujours sur la branche **master** qui va recevoir le merge:
```
git branch
```
- Exécutez le merge, ce merge est de type fast-forward parce que la branche **master** n'a pas été changé:
```
git merge operations
```
Vous devez trouver maintenant le nouveau code présent dans la branche **master**.

### Merger et résoudre les conflits
Si on modifie les mêmes fichiers dans deux branches à merger, on risque avoir un conflit au moment du merge.

- Ajouter le code suivant à la classe CalculatorService dans la branche **master**:
```Java
    @GET
    @Path("mul")
    @Produces(MediaType.APPLICATION_JSON)
    public CalculatorResponse Mul(@QueryParam("x") int x, @QueryParam("y") int y) {
        return new CalculatorResponse(x, y, x * y);
    }
```
- Commitez le changement:
```
git add src/*
git commit -m "Create multiplication function"
```
- Changer de branche à **operations**:
```
git checkout operations
```
- Ajouter le code suivant à la classe CalculatorService dans la branche **operations**:
```Java
    @GET
    @Path("div")
    @Produces(MediaType.APPLICATION_JSON)
    public CalculatorResponse Div(@QueryParam("x") int x, @QueryParam("y") int y) {
        return new CalculatorResponse(x, y, x / y);
    }
```
- Commitez le changement:
```
git add src/*
git commit -m "Create division function"
```
- Retourner à la branche **master** et y merger la branche **operations**
```
git checkout master
git merge operations
```
Vous devez avoir la réponse suivante:
```
Auto-merging src/main/java/com/devops/calculator/CalculatorService.java
CONFLICT (content): Merge conflict in src/main/java/com/devops/calculator/CalculatorService.java
Automatic merge failed; fix conflicts and then commit the result.
```
Retourner à votre éditeur, vous devez trouvez le code suivant dans la classe CalculatorService:
```Java
<<<<<<< HEAD
    @Path("mul")
    @Produces(MediaType.APPLICATION_JSON)
    public CalculatorResponse Mul(@QueryParam("x") int x, @QueryParam("y") int y) {
        return new CalculatorResponse(x, y, x * y);
    }
=======
    @Path("div")
    @Produces(MediaType.APPLICATION_JSON)
    public CalculatorResponse Div(@QueryParam("x") int x, @QueryParam("y") int y) {
        return new CalculatorResponse(x, y, x / y);
    }
>>>>>>> operations
```
En fait, git vous indique l'emplacement du conflit et la source de chaque partie.
- Pour résoudre le conflit, il faut garder le code qui doit faire partie de votre application et supprimer le code non nécessaire. Dans notre cas, les deux fonctions sont nécessaires, donc on doit les garder, mais il faut supprimer les traces ajoutées par git:
```Java
    @Path("mul")
    @Produces(MediaType.APPLICATION_JSON)
    public CalculatorResponse Mul(@QueryParam("x") int x, @QueryParam("y") int y) {
        return new CalculatorResponse(x, y, x * y);
    }

    @Path("div")
    @Produces(MediaType.APPLICATION_JSON)
    public CalculatorResponse Div(@QueryParam("x") int x, @QueryParam("y") int y) {
        return new CalculatorResponse(x, y, x / y);
    }
```
- Enregistrez, puis effectuez un commit ordinaire:
```
git add src/*
git commit -m "Merge branch operations"
```

## Publish your project to a shared remote repository

Jusqu'à maintenant, le projet calculator est local dans le disque de votre pc.

Git supporte le développement en équipe en permettant de stocker le projet dans un repository en ligne (github, gitlab, bitbucket ...).

Pour ce faire, il faut créer votre remote repository sur l'un des fournisseurs du service git puis lier ce remote repo à votre repo local. Par la suite vous pouvez pusher vos changement vers le repo distant, ajouter des contributeurs dans votre projet ...

### Create your remote repository on GitLab.com

- Visitez gitlab.com
- Si vous n'avez pas un compte, créez un.
- connectez vous à votre compte
- Créez un repository appelé calculator
- Liez ce repo à votre repo local
```
git remote add origin https://gitlab.com/mrmedtb/calculator.git
```
