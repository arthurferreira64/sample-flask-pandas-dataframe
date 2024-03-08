git clone https://github.com/app-generator/flask-pandas-dataframe.git
cd flask-pandas-dataframe
source ../docker-aston-poec/venv/bin/activate
pip3 install -r requirements.txt
flask shell
>>> from app import db  # import SqlAlchemy interface
>>> db.create_all()     # create SQLite database and Data table
>>> quit()

flask load-data titanic-min.csv

export FLASK_APP=app.py

flask run --host=0.0.0.0 --port=31201
