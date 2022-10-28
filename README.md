# Flask-SQLAlchemy

## Learning Goals

- Build and run a Flask application on your computer.
- Extend a Flask application to meet the unique requirements of different projects.

***

## Key Vocab

- **Web Framework**: software that is designed to support the development of
  web applications. Web frameworks provide built-in tools for generating web
  servers, turning Python objects into HTML, and more.
- **Extension**: a package or module that adds functionality to a Flask
  application that it does not have by default.
- **Request**: an attempt by one machine to contact another over the internet.
- **Client**: an application or machine that accesses services being provided
  by a server through the internet.
- **Web Server**: a combination of software and hardware that uses Hypertext
  Transfer Protocol (HTTP) and other protocols to respond to requests made
  over the internet.
- **Web Server Gateway Interface (WSGI)**: an interface between web servers
  and applications.
- **Template Engine**: software that takes in strings with tokenized
  values, replacing the tokens with their values as output in a web browser.

***

## Introduction

To get the most out of Flask, it is a good idea to connect it to a database for
long-term storage of important data. Flask works well with SQLAlchemy and
Alembic in their base forms as we saw in Phase 3, but we will be using libraries
specially tailored to Flask: Flask-SQLAlchemy and Flask-Migrate. While these
are mostly the same as SQLAlchemy and Alembic, there are a few important
differences to note before you start developing your own applications.

We will explore the integration of these libraries with Flask applications as
well as these differences in this lesson.

***

## Flask Extensions

Flask is, on its own, a very minimal framework. It provides what we need to
deliver Python code to the web, but that's about it. That being said, it is also
designed to be _extended_. This allows you to choose the tools that you would
like to use for database management, migrations, web forms, authentication, and
much more.

Many Flask extensions, such as Flask-SQLAlchemy and Flask-Migrate, were created
and are maintained by the Pallets Projects community. That being said, Flask is
a _Python_ web framework, and you can use any Python libraries that you think
would benefit your project.

***

## Managing Databases with Flask-SQLAlchemy

Run `pipenv install && pipenv shell` to install Flask, Flask-SQLAlchemy, and
Flask-Migrate in your virtual environment. Enter the following in
`app.py`:

```py
# app/app.py

#!/usr/bin/env python3

from flask import Flask

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///app.db'

if __name__ == '__main__':
    app.run(port=5555)

```

Just as with our other Flask applications, we start off with an application
file that creates an instance of the `Flask` class with the module's name.
We are also taking advantage of this opportunity to start configuring our
database: in Phase 3, we would have defined this in `alembic.ini`. Since we're
using Flask-Migrate instead of pure Alembic, we define the environment variables
in the application itself.

Now that we have a basic application ready to run, let's configure some models.

### Models with Flask-SQLAlchemy

The structure of models in Flask-SQLAlchemy is identical to that of SQLAlchemy
models with one exception: rather than importing individual fields from the
SQLAlchemy module, we import a `SQLAlchemy` class from Flask-SQLAlchemy that
contains all of the same fields as attributes. This behaves similarly to
`declarative_base` in SQLAlchemy.

Let's create some models!

```py
# app/models.py

from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

class Owner(db.Model):
    __tablename__ = 'owners'

    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String, unique=True)

    pets = db.relationship('Pet', backref='owner')

    def __repr__(self):
        return f'<Pet Owner {self.name}>'

class Pet(db.Model):
    __tablename__ = 'pets'
    
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String)
    species = db.Column(db.String)

    owner_id = db.Column(db.Integer, db.ForeignKey('owners.id'))

    def __repr__(self):
        return f'<Pet {self.name}, {self.species}>'

```

As you can see, these are very similar to the models we set up in Phase 3.
rather than using a `Base` as a parent object for each of our models, we use
the `db` object's `Model` class. Inside of the models, we retrieve important
classes and methods through the `db` object. All classes that can be imported
from vanilla `SQLAlchemy` can be accessed through the `db` object.

We can manually add these data models to the database, but there aren't many
valid reasons to do that in your work. Instead, we will generate our database
from our models using Flask-Migrate.

### Flask-Migrate

Like Flask-SQLAlchemy with vanilla SQLAlchemy, Flask-Migrate is a wrapper for
Alembic with minimal changes to allow it to integrate better with Flask
applications. This can become a bit confusing, especially from the command line.
Don't worry though- we'll discuss those nuances in detail here!

Modify `app.py` to mirror the following:

```py
# app/app.py

#!/usr/bin/env python3

from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

from models import db

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///app.db'

db = SQLAlchemy(app)

migrate = Migrate(app, db)

db.init_app(app)

if __name__ == '__main__':
    app.run(port=5555)

```

We have imported the `Migrate` class here to set up our migrations using our
Flask application instance and our `SQLAlchemy` instance. We also initialized
our application for use within our database configuration with
`db.init_app(app)`.

> **NOTE:** `db.init_app(app)` is an easy step to forget! (So don't!)

We're ready to get started with our migrations. The commands for Flask-Migrate
are identical to those in Alembic, with the exception of using `flask db` in
place of `alembic` in commands. Run the following from the `app/` directory in
the command line:

```console
$ export FLASK_APP=app.py
$ export FLASK_RUN_PORT=5555
$ flask db init
# => /python-p4-flask-sqlalchemy/.venv/lib/python3.10/site-packages/flask_sqlalchemy/__init__.py:872: FSADeprecationWarning: SQLALCHEMY_TRACK_MODIFICATIONS adds significant overhead and will be disabled by default in the future.  Set it to True or False to suppress this warning.
# =>   warnings.warn(FSADeprecationWarning(
# => /python-p4-flask-sqlalchemy/.venv/lib/python3.10/site-packages/flask_sqlalchemy/__init__.py:872: FSADeprecationWarning: SQLALCHEMY_TRACK_MODIFICATIONS adds significant overhead and will be disabled by default in the future.  Set it to True or False to suppress this warning.
# =>   warnings.warn(FSADeprecationWarning(
# =>   Creating directory /python-p4-flask-sqlalchemy/app/migrations ...  done
# =>   Creating directory /python-p4-flask-sqlalchemy/app/migrations/versions ...  done
# =>   Generating /python-p4-flask-sqlalchemy/app/migrations/script.py.mako ...  done
# =>   Generating /python-p4-flask-sqlalchemy/app/migrations/env.py ...  done
# =>   Generating /python-p4-flask-sqlalchemy/app/migrations/README ...  done
# =>   Generating /python-p4-flask-sqlalchemy/app/migrations/alembic.ini ...  done
# =>   Please edit configuration/connection/logging settings in '/python-p4-flask-sqlalchemy/app/migrations/alembic.ini' before proceeding.
```

Let's heed that warning and set `SQLALCHEMY_TRACK_MODIFICATIONS` to `False`.

```py
# app/app.py

...
app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///app.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
...

```

We're getting a warning to change `app/migrations/alembic.ini` before we
continue, but our `app/app.py` configuration already manages all of our
unique environment variables. We can jump straight into migrating:

```console
$ flask db revision --autogenerate -m'Create tables owners, pets'
# => INFO  [alembic.runtime.migration] Context impl SQLiteImpl.
# => INFO  [alembic.runtime.migration] Will assume non-transactional DDL.
# =>   Generating /python-p4-flask-sqlalchemy/app/migrations/versions/a48f1fc37e07_create_tables_owners_pets.py ...  done
```

...and pushing those migrations to our database:

```console
$ flask db upgrade head
# => INFO  [alembic.runtime.migration] Context impl SQLiteImpl.
# => INFO  [alembic.runtime.migration] Will assume non-transactional DDL.
# => INFO  [alembic.runtime.migration] Running upgrade  -> 1c84830b3fc2, Create tables owners, pets
```

We've created a database with Flask-SQLAlchemy and Flask-Migrate! Open
`app/app.db` and you should see the fruits of your labor:

![Screenshot of SQLite database with three tables: alembic_version, owners, and
pets](
https://curriculum-content.s3.amazonaws.com/python/flask-sqlalchemy-owners-pets-db.png)

***

## Conclusion

Flask extensions give us many opportunities to add functionality to our
applications while writing minimal code for configurations. We saw how to
create a database with Flask-SQLAlchemy and Flask-Migrate, and some among us
may have even noticed that we wrote slightly _less_ code than we did when using
SQLAlchemy by itself!

In the next lesson, we will look into working with data in Flask applications
using Flask-SQLAlchemy.

***

## Solution Code

```py
# app/app.py

from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

from models import db

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///app.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

migrate = Migrate(app, db)

db.init_app(app)

if __name__ == '__main__':
    app.run(port=5555)

```

```py
# app/models.py

from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

class Owner(db.Model):
    __tablename__ = 'owners'

    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String, unique=True)

    pets = db.relationship('Pet', backref='owner')

    def __repr__(self):
        return f'<Pet Owner {self.name}>'

class Pet(db.Model):
    __tablename__ = 'pets'
    
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String)
    species = db.Column(db.String)

    owner_id = db.Column(db.Integer, db.ForeignKey('owners.id'))

    def __repr__(self):
        return f'<Pet {self.name}, {self.species}>'

```

***

## Resources

- [Quickstart - Flask-SQLAlchemy](https://flask-sqlalchemy.palletsprojects.com/en/2.x/quickstart/#)
- [Flask-Migrate](https://flask-migrate.readthedocs.io/en/latest/)
- [Flask Extensions, Plug-ins, and Related Libraries - Full Stack Python](https://www.fullstackpython.com/flask-extensions-plug-ins-related-libraries.html)
