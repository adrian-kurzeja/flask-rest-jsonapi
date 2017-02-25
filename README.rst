.. image:: https://badge.fury.io/py/Flask-Rest-JSONAPI.svg
    :target: https://badge.fury.io/py/Flask-Rest-JSONAPI
.. image:: https://travis-ci.org/miLibris/flask-rest-jsonapi.svg
    :target: https://travis-ci.org/miLibris/flask-rest-jsonapi
.. image:: https://coveralls.io/repos/github/miLibris/flask-rest-jsonapi/badge.svg
    :target: https://coveralls.io/github/miLibris/flask-rest-jsonapi
.. image:: https://readthedocs.org/projects/flask-rest-jsonapi/badge/?version=latest
    :target: http://flask-rest-jsonapi.readthedocs.io/en/latest/?badge=latest
    :alt: Documentation Status

Flask-Rest-JSONAPI
##################
Flask-Rest-JSONAPI is a flask extension to build REST api. It combines the power of `Flask-Restless <https://flask-restless.readthedocs.io/en/stable/>`_ and the flexibility of `Flask-RESTful <http://flask-restful-cn.readthedocs.io/en/0.3.5/a>`_ around a strong specification `JSONAPI 1.0 <http://jsonapi.org/>`_. This framework is design to quickly build REST api and to fit the complexity of real life projects with legacy data and multiple data storages.

Install
=======

    pip install Flask-Rest-JSONAPI

Quickstart
==========

.. code-block:: python

    # -*- coding: utf-8 -*-

    from flask import Flask
    from flask_rest_jsonapi import Api, ResourceDetail, ResourceList
    from flask_sqlalchemy import SQLAlchemy
    from marshmallow_jsonapi.flask import Schema
    from marshmallow_jsonapi import fields

    # Create the Flask application and the Flask-SQLAlchemy object.
    app = Flask(__name__)
    app.config['DEBUG'] = True
    app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:////tmp/test.db'
    db = SQLAlchemy(app)

    # Create the api
    api = Api(app)


    # Create model
    class Person(db.Model):
        id = db.Column(db.Integer, primary_key=True)
        name = db.Column(db.Unicode, unique=True)
        birth_date = db.Column(db.Date)

    # Create the database tables.
    db.create_all()


    # Create schema
    class PersonSchema(Schema):
        class Meta:
            type_ = 'person'
            self_view = 'person_detail'
            self_view_kwargs = {'id': '<id>'}

        id = fields.Str(dump_only=True)
        name = fields.Str()
        created = fields.Date()


    # Create resource
    class PersonList(ResourceList):
        schema = PersonSchema
        data_layer_kwargs = {'session': db.session,
                             'model': Person}


    class PersonDetail(ResourceDetail):
        schema = PersonSchema
        data_layer_kwargs = {'session': db.session,
                             'model': Person}

    # Create endpoints
    api.route(PersonList, 'person_list', '/persons')
    api.route(PersonDetail, 'person_detail', '/persons/<int:id>')

    # start the flask loop
    app.run()




This example provides this api:

========================  ======  =============  ===========================
url                       method  endpoint       Usage
========================  ======  =============  ===========================
/persons                  GET     person_list    Get a collection of persons
/persons                  POST    person_list    Create a person
/persons/<int:person_id>  GET     person_detail  Get person details
/persons/<int:person_id>  PATCH   person_detail  Update a person
/persons/<int:person_id>  DELETE  person_detail  Delete a person
========================  ======  =============  ===========================

Flask-Rest-JSONAPI vs `Flask-RESTful <http://flask-restful-cn.readthedocs.io/en/0.3.5/a>`_
==========================================================================================

* Instead of Flask-RESTful, Flask-Rest-JSONAPI provides a default implementation of get, post, patch and delete methods around a strong specification JSONAPI 1.0. Thanks to this you can build REST api very quickly.
* Flask-Rest-JSONAPI is as flexible as Flask-RESTful. You can rewrite every default methods implementation to make custom work like distributing object creation.

Flask-Rest-JSONAPI vs `Flask-Restless <https://flask-restless.readthedocs.io/en/stable/>`_
==========================================================================================

* Flask-Rest-JSONAPI is a real implementation of JSONAPI 1.0 specification. So instead of Flask-Restless, Flask-Rest-JSONAPI force you to create a real logical abstration over your data models with `Marshmallow <https://marshmallow.readthedocs.io/en/latest/>`_. So you can create complex resource over your data.
* Instead of Flask-Restless, Flask-Rest-JSONAPI can use any ORM or data storage through the data layer concept, not only `SQLAlchemy <http://www.sqlalchemy.org/>`_. A data layer is a CRUD interface between your resource and one or more data storage so you can fetch data from any data storage of your choice or create resource that use multiple data storage.
* Like i said previously, Flask-Rest-JSONAPI is a real implementation of JSONAPI 1.0 specification. So instead of Flask-Restless you can manage relationships via REST. You can create dedicated url to create a CRUD api to manage relationships. 
* Plus Flask-Rest-JSONAPI helps you to design your application with strong separation between resource definition (schemas), resource management (resource class) and route definition to get a great organization of your source code.
* Instead of Flask-Restless, Flask-Rest-JSONAPI is highly customizable. For example you can entirely customize you urls, define multiple urls for the same resource manager,control serialization parameters of each methods and lot of very useful parameters.
* Finally instead of Flask-Restless, Flask-Rest-JSONAPI provides a great error handling system according to JSONAPI 1.0. Plus the exception handling system really helps the api developer to quickly find missing resources requirements.

Documentation
=============

Documentation available here: http://flask-rest-jsonapi.readthedocs.io/en/latest/



