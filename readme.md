# SQLAlchemy Tutorial

## Using SQLAlchemy

### Using the SQLAlchemy Engine

---

1. import the create_engine function from sqlalchemy

```python

from sqlalchemy import create_engine

```

2. Create the engine by creating an instance
   1. Depending on the DB provider the string passed to the `create_engine()` function will change

```python
engine = create_engine()
```

### Using the SQLAclhemy ORM
