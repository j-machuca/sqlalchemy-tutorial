# SQLAlchemy Tutorial

### Using the SQLAlchemy Core

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
   # URL String Syntax
   # dialect+driver://username:password@host:port/database

   # PostgreSQL
   # engine = create_engine("postgresql://usernamme:password@localhost:5432/database")

   from sqlalchemy import create_engine

   engine = create_engine("sqlite+pysqlite:///:memory:", echo=True, future=True)
   ```

**3.** Working with transactions. There are Two primary endpoints

1.  The `Connection` object.

    - Use the context manager to get a connection object from the engine.

      ```python
         from sqlalchemy import text

         with engine.connect() as conn:
            result = conn.execute(text("select 'hello world'"))
            print(result.all())
      ```

    - In order for changes to be saved to the DB they need to be committed we need to call the `Connection.commit()` method.

      _Setting the `autocommit` parameter on the engine is possible_

    #### There are two approaches:

    1. Commit as you go.

2.  The `Result` object.

### Using the SQLAclhemy ORM

---
