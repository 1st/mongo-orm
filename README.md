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
# models.py
from mongo_orm import Model, fields, validators


class User(Model):
    fields = {
        'nick': fields.TextField(check_min_length=2, check_max_length=20),
        'email': fields.TextField(check_email=True),
        'password': fields.TextField(check_length=32),
        'name': fields.TextField(),
        'birthday': fields.DateTime(),
    }
    required_fields = ('email', 'nick', 'password')
    key_field = 'nick'
    unique_fields = ('email')


class Blog(Model):
    fields = {
        'title': fields.TextField(check_min_length=20, check_max_length=500),
        'text': fields.TextField(check_min_length=50, check_max_length=10000),
        'author': fields.Reference(User),
    }
    required_fields = ('title', 'text', 'author')
    unique_fields = ('title')
```


Forms
--------

With forms we can filter data, that should be passed to Model.

```python
# forms.py
from mongo_orm import Form, widgets
from models import User, Blog


validate_email_user_form = Form(User, fields=['email'])
create_user_form = Form(User, fields=['nick', 'password'])
edit_user_form = Form(User, fields=['email', 'password', 'name'])
edit_birthday_user_form = Form(User, fields=['birthday'], widgets={'birthday': widgets.SplitDateTimeWidget})
```


Find
--------

```python
from mongo_orm import OR, Connection

# create connection to MongoDB and select database
db = Connection('localhost', 'user', '*****')
db = db.database_name

# basic operations
db.colletion_name.find()
db.colletion_name.find_one()
db.colletion_name.count()

db.colletion_name.find(field_name="value", field2_name="value2")
# -> db.colletion_name.find({field_name: "value", field2_name: "value2"})

db.colletion_name.find(age__in=[18, 19, 20])
# -> db.colletion_name.find({'age': {'$in': [18, 19, 20]}})

db.colletion_name.find(date__lt=now)
# -> db.colletion_name.find({"date": {"$lt": d}})

db.colletion_name.find(OR(name_first__startwith='G', birth__lt=now))
# -> db.colletion_name.find({
#      $or: [
#        {'name.first' : /^G/},
#        {birth: {$lt: new Date('01/01/1945')}}
#      ]
#    })

#
# Operarions with queryset
#

query_set = db.colletion_name.find()

query_set.sort("author")
# -> query_set.sort({author: 1})

query_set.sort("author", "-date")
# -> query_set.sort({author: 1, date: -1})

query_set[:5]
# -> query_set.limit(5)

query_set[3:5]
# -> query_set.skip(3).limit(2)

```

