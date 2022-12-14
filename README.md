*Flaskez* is a multipurpose flask extension. It exists both to make creating websites easier, but also to add extra functionality to flask.

Installation:
-
```shell
pip install flaskez
```

Import as:
-
```python
import flaskez
```

Example program:
-
```python
import flaskez

app = flaskez.create_app(__name__)

@app.route("/")
def home():
    return "Hello!"
```
In this example, the syntax and everything is exactly like flask. The bigger help comes in to play when you are writing more complex programs.

---
Example 2:
-
```python
import flaskez

app, db = flaskez.create_app(
    __name__,
    config={
        "SQLALCHEMY_DATABASE_URI": "sqlite:///users.sqlite3",
        "SQLALCHEMY_TRACK_MODIFICATIONS": False
    },
    create_db=True
)

@app.route("/")
def home():
    return "Hello!"

if __name__ == '__main__':
    app.run()
```
This program generates an SQLAlchemy database using [flask-SQLAlchemy](https://pypi.org/project/flask-sqlalchemy/).
In this current example the database is not used.
I am currently working on making an importable universal database model.
The config dictionary sets ``app.config["SQLALCHEMY_DATABASE_URI"]`` to ``"sqlite:///users.sqlite3"``, and ``app.config["SQLALCHEMY_TRACK_MODIFICATIONS"]`` to ``False``.
It also tells the function that we should create a database.
Depending on if create_db is true or not, the program either returns a flask.Flask object, or a tuple with flask.Flask and SQLAlchemy.

By default, the application generates a secret key using:
```python
app.secret_key = str(
            time.time()) + str(
            random.randrange(1, 10000)) + str(
            secrets.randbelow(100000) * random.randrange(1, 100)) + str(
            secrets.randbelow(1000000000) * 0.0001 * time.time())
```
This can be disabled by settings ``generate_secret_key`` to ``False``:
```python
import flaskez

app, db = flaskez.create_app(
    __name__,
    config={
        "SQLALCHEMY_DATABASE_URI": "sqlite:///users.sqlite3",
        "SQLALCHEMY_TRACK_MODIFICATIONS": False
    },
    generate_secret_key=False,
    create_db=True
)
```
This will set the secret key to ``"DefaultKey"``, but can be changed using ``secret_key="my_secret_key"``.

---
Example 3:
-
The ``create_app()`` function has a parameter called run, which you can use to run the flask.Flask object directly from the function.
This requires having defined blueprints beforehand.
```python
import flaskez
from flask import Blueprint

routes = Blueprint('routes', __name__)  # Can be placed inside another file and then imported

@routes.route("/")
def home():
    return "Hello!"

app = flaskez.create_app(
    __name__,
    run=True,
    routes=[
        {
            'blueprint': routes
        }
    ]
)
```
---
**All the parameters for flaskez.create_app() are:**
* app_name: Name of the Flask application. Usually \_\_name__ works just fine.
* *args: Optional positional arguments. Passed to the flask.Flask().register_blueprint() function.
* run: Optional bool if you want the function to run the application directly.
* run_kwargs: Optional dict if you want extra keyword arguments passed to the flask.Flask().run() function.
* config: Dictionary. Configuration for the flask.Flask object.
* secret_key: String. Variable if you want to define a custom secret key for the application.
* generate_secret_key: Bool. Variable if you want to generate a secret key. Uses python's random and secrets modules.
* routes: List. A list with dictionaries of all blueprints for the application. Format: routes=[{'path': 'routes.routes', 'blueprint': 'routes'}] or routes=[{'blueprint': flask.Blueprint(*args, **kwargs)}] Path can also be the imported file. An optional key 'prefix' can be added to specify a prefix for the url (an extra / (route) before the unique route of the web page). It is easier to use the create_blueprint() function (see its docs for more info).
* error_pages: List. A list with dictionaries of all error pages for the application. Format: error_pages=[{'path': 'routes.routes', 'code': '404', 'function': 'not_found'}]
* permanent_session_lifetime: Dictionary. Permanent session lifetime variable for flask.sessions.
* create_db: Bool. Optionally create a database (using flask-sqlalchemy). Used for login, etc.
* flask_kwargs: A dictionary for arguments passed to the creation of the flask.Flask object (kwargs and args are already used for blueprints)
* db_kwargs: A dictionary for arguments passed to the creation of the flask_sqlalchemy.SQLAlchemy object (kwargs and args are already used for blueprints)
* **kwargs: Optional keyword arguments. Passed to the register_blueprint() function.
