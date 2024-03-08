# Flask Pandas DataFrame

## Cahier des charges du fil-rouge
Le société DataMine veut mettre dans une chaîne CI/CD utilisant Jenkins son developpement
de produit informatique qui est, pour le moment, une application Python Flask de type data mining utilisant la librairie Pandas de Python .
Vous devez compléter le projet github existant, en précisant votre demarche pour porter ce projet dans la chaine ci/cd.

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
