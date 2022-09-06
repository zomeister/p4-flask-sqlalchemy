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
`flask_app.py`:

```py
#!/usr/bin/env python3

from flask import Flask

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///db/app.db'

if __name__ == '__main__':
    app.run()

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
    __tablename__ = 'users'
    
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

***

## Resources

- [API - Pallets Projects](https://flask.palletsprojects.com/en/2.2.x/api/)
- [HTTP request methods - Mozilla][moz_http]
- [HTTP response status codes - Mozilla][moz_http_status]
- [Response Objects - Pallets Projects][response]

[moz_http]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods
[moz_http_status]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status
[response]: https://flask.palletsprojects.com/en/2.2.x/api/#response-objects
