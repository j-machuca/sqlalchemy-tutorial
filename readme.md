# SQLAlchemy Tutorial

## Using SQLAlchemy

### Using the SQLAlchemy Engine

---

1. Import the create_engine function from sqlalchemy

   ```python

   from sqlalchemy import create_engine

   ```

2. Create the engine by creating an instance

   1. Depending on the **DB provider** (i.e. PostgreSQL, SQLite, MySQL, etc.) the URL string passed to the `create_engine()` function will change.
      The string is composed by the {DBtype}+{Adapter}+{url required by vendor}
   2. The echo parameter passed to `create_engine()` instructs the engine to log all the SQL it emits to a Python logger that will write to standard out.
   3. The future parameter will instruct the engine allow us to use the **2.0 style**

   \*\* For PostreSQL

   ```python
   # dialect+driver://username:password@host:port/database
   engine = create_engine("postgresql://usernamme:password@localhost:5432/database")
   ```

### Using the SQLAclhemy ORM
