# SQLAlchemy Tutorial

## Using SQLAlchemy

### Using the SQLAlchemy Engine

---

**1.** Import the `create_engine` function from `sqlalchemy`

```python

from sqlalchemy import create_engine

```

**2.** Creating an `engine` instance. Reference to the [Engine](https://docs.sqlalchemy.org/en/14/core/engines.html) object documentation.

1. Depending on the **DB provider** (i.e. PostgreSQL, SQLite, MySQL, etc.) the URL string passed to the `create_engine()` function will change.
2. The **_echo_** parameter passed to `create_engine()` instructs the engine to log all the SQL it emits to a Python logger that will write to standard out.
3. The **_future_** parameter will instruct the engine allow us to use the **2.0 style**

**For PostreSQL**

```python
# Url String Syntax
# dialect+driver://username:password@host:port/database

# PostgreSQL
# engine = create_engine("postgresql://usernamme:password@localhost:5432/database")

from sqlalchemy import create_engine

engine = create_engine("sqlite+pysqlite:///:memory:", echo=True, future=True)
```

**3.** Working with transactions

When working with the engine there are 2 primary endpoints.

1.  The `Connection`
2.  The `Result`

### Using the SQLAclhemy ORM

---
