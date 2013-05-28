Python ORM for MongoDB
=========

Simple but powerful ORM-like implementation of tool to wotk with MongoDB from Python.
In other words - it's Django ORM but designed only for MongoDB.

**Current state**: Alpha, not production ready


Find
----------

```python
# basic operations
db.colletion_name.find()
db.colletion_name.find_one()
db.colletion_name.count()

db.colletion_name.find(field_name="value", field2_name="value2")
# -> db.colletion_name.find({field_name: "value", field2_name: "value2"})

db.colletion_name.find(age__in=[18, 19, 20], )
# -> db.colletion_name.find({'age': {'$in': [18, 19, 20]}})
```

