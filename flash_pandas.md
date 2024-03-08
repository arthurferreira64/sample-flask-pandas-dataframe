# Projet fil-rouge

## Cahier des charges du fil-rouge
Le société DataMine veut mettre dans une chaîne CI/CD utilisant Jenkins son developpement
de produit informatique qui est, pour le moment, une application Python Flask de type data mining utilisant la librairie Pandas de Python .
Vous devez compléter le projet github existant, en précisant votre demarche pour porter ce projet dans la chaine ci/cd.

**Etapes:**
* Etudier le projet suivant   
  **https://github.com/app-generator/sample-flask-pandas-dataframe.git**  
  Faire un fork de toutes les branches dans votre repo perso github  
  etudiez le projet le fichier README.md, la partie setup.   
  Faire une clone de votre projet forke dans votre directory de travail sur votre laptop et
  dans la vm google.
* Faire fonctionner, en ligne de commande,  le projet dans un shell, precisez les commandes necessaires pour realiser ca dans un fichier **flask_pandas.md**, l'application flask doit etre accessible par l'adresse publique gcp google et par le **port 31201**. Lire correctement le fichier README du projet pour trouver les commandes necessaires. Faire regulierement des git commit, push pour enregister votre projet
* dans le projet, écrire un fichier **Dockerfile** pour conteneuriser cette application (pensez a faire git commit, push)
* en dehors de jenkins, sur votre vm google, creez une image **flask-panda**. Mettre l'image dans le repository docker hub
* en dehors de jenkins, sur votre vm google,  faire une docker run et verifier que l'application est disponible sur le port 31201.
* dans jenkins creez un job **flask-panda-jmeter**, creer un test plan pour verifier si l'application affiche des data.
* chainer les jobs jenkins pour faire un pipeline graphique
* mettre a jour le jenkins plugin de goland pour lancer des build depuis votre IDE goland
* mettre un webhook dans votre projet github, pour demarrer automatiquement votre ci/cd a chaque commit.


## Installation 
    
1. Clonez le dépôt GitHub :
    ```bash
    git clone https://github.com/arthurferreira64/sample-flask-pandas-dataframe.git
    cd sample-flask-pandas-dataframe
    ```

2. Activez votre environnement virtuel Python :
    ```bash
    source ../docker-aston-poec/venv/bin/activate
    ```

3. Installez les dépendances Python à l'aide de pip :
    ```bash
    pip3 install -r requirements.txt
    ```

## Initialisation de la base de données

1. Ouvrez un shell Flask :
    ```bash
    flask shell
    ```

2. Dans le shell Flask, importez l'interface SqlAlchemy et créez la base de données SQLite avec la table de données :
    ```python
    from app import db  # import SqlAlchemy interface
    db.create_all()     # create SQLite database and Data table
    quit()
    ```

3. Chargez les données CSV dans la base de données :
    ```bash
    flask load-data titanic-min.csv
    ```

## Exécution de l'application

1. Définissez la variable d'environnement `FLASK_APP` :
    ```bash
    export FLASK_APP=app.py
    ```

2. Lancez l'application Flask :
    ```bash
    flask run --host=0.0.0.0 --port=31201
    ```

3. Accédez à l'application à l'adresse suivante dans votre navigateur : [http://34.155.11.159:31201](http://34.155.11.159:31201)

## Création et déploiement de l'image Docker

1. Créez le fichier Dockerfile et le script d'initialisation de la base de données (init_database.sh).

2. Construisez l'image Docker à partir du Dockerfile :
    ```bash
    docker build -t flask-panda .
    ```

3. Connectez-vous à Docker Hub :
    ```bash
    docker login -u lastonexx41
    ```

4. Taguez l'image avec votre nom d'utilisateur Docker Hub :
    ```bash
    docker image tag flask-panda:latest lastonexx41/flask-panda:latest
    ```

5. Poussez l'image vers Docker Hub :
    ```bash
    docker push lastonexx41/flask-panda:latest
    ```

## Exécution de l'application avec Docker

1. Lancez un conteneur Docker à partir de l'image :
    ```bash
    docker run -d -p 31201:5000 flask-panda
    ```

# Git, Jenkins et Goland
## Webhook sur dépot git
Ajout de l'ip dans webhook pour détecter les modifications sur mon dépot git.

![Webhook](/media/screen1.png "Webhook")

## Configuration GoLand Avec Jenkins
Configuration du plugin Jenkins dans GoLand pour afficher mes différents jobs

![GoLand](/media/screen3.png "GoLand")

## Visualisation du pipeline sur Jenkins et Chainage
Création d'une pipeline avec 2 tâches comme demandé

![Pipeline](/media/screen2.png "Pipeline")
- **flask-pandas-docker** : Il est conçu pour récupérer automatiquement les informations à chaque "push" sur le dépôt Git, grâce à un webhook. Il utilise ensuite ces informations pour construire une nouvelle image Docker et la déployer sur le serveur. Il convient de noter qu'il est recommandé de prévoir également l'arrêt et la suppression de l'ancienne instance Docker pour éviter tout problème potentiel.
- **flask-pandas-jmeter** : Il est dédié à la vérification de l'existence des données. Il est conçu pour être déclenché uniquement si l'exécution de Flask-Pandas-Docker n'a pas généré d'erreur.

## Job flask panda jmeter
Création dun job flask-panda-jmeter

J'ai mis Une étape de builds avec exécution d'un script shell :

```shell
jmeter -JUSER=20 -JDELAY=10 -Jjmeter.save.saveservice.output_format=xml -Jjmeter.save.saveservice.response_data.on_error=true -n -t flask_pandas_test_plan.jmx  -l testresult.jlt
```

Mon test ce lance pour 20 utilisateurs avec un délai de 10 secondes.
Mon test JMeter est conçu pour vérifier la présence de données sur la page /data. En effet, si JMeter détecte un résultat différent de "Rows = 0", cela signifie que des données existent. De plus, nous pouvons également vérifier que "Rows = 5" pour nous assurer qu'il y a toujours cinq données présentes.

Après cette vérification, j'ai ajouté des étapes supplémentaires à exécuter après la construction. Ces étapes incluent l'analyse de la sortie de la console et la publication d'un test de performance.
