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

***

## Resources

- [API - Pallets Projects](https://flask.palletsprojects.com/en/2.2.x/api/)
- [HTTP request methods - Mozilla][moz_http]
- [HTTP response status codes - Mozilla][moz_http_status]
- [Response Objects - Pallets Projects][response]

[moz_http]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods
[moz_http_status]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status
[response]: https://flask.palletsprojects.com/en/2.2.x/api/#response-objects
