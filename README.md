Python ORM for MongoDB
=========

Simple but powerful ORM-like implementation of tool to wotk with MongoDB from Python.
In other words - it's Django ORM but designed only for MongoDB.

**Current state**: Alpha, not production ready, under development


Describe models
--------

We need to describe models to store data and validate it. We can define required and optional fields.

**Note:** In Google App Engine we have a problem with validate data that stored in datastore and we try to retrieve
this object from datastore and validate in model. We need to fix this problem.

Sometimes we need to add new required field, or add "required" attribute to already exisitng field.
In the same time, we have a lot of objects stored in datastore, that haven't this required attribute.
We need to create operation that synchronize changes between model and appropriate collections in datastore.
This operation should be completed as deferred task.

```python
from mongo_orm import Model, fields


class User(Model):
    fields = {
        'nick': fields.TextField(min_length=2, max_length=20),
        'password': fields.TextField(length=32),
        'name': fields.TextField(),
    }
    required_fields = ('nick', 'password')
    key_field = 'nick'


class Blog(Model):
    fields = {
        'title': fields.TextField(min_length=20, max_length=500),
        'text': fields.TextField(min_length=50, max_length=10000),
        'author': fields.Reference(User),
    }
    required_fields = ('title', 'text', 'author')
    unique_fields = ('title')
```

Find
--------

```python
# basic operations
db.colletion_name.find()
db.colletion_name.find_one()
db.colletion_name.count()

db.colletion_name.find(field_name="value", field2_name="value2")
# -> db.colletion_name.find({field_name: "value", field2_name: "value2"})

db.colletion_name.find(age__in=[18, 19, 20])
# -> db.colletion_name.find({'age': {'$in': [18, 19, 20]}})
```

