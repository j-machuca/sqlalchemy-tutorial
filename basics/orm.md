## Using the SQLAclhemy ORM

### Working with Session

1. Executing with an ORM Session ([Working with Session](https://docs.sqlalchemy.org/en/14/orm/session_basics.html#id1))

   - Most patterns and examples used by the SQLAlchemy Core also apply to the ORM Session.

   - The transactional/database object when using the ORM is called `Session`

     _The `Session` internally refers to the `Connection` object to emit SQL_

   - When the `Session` is used with non-ORM constructs it passes throught the SQL statement and works very similarly to the `Connection` does.

   - The `Session` doesn't hold onto the connection object instead it gets a new connection from the `Engine` when executing SQL is next needed.

2. Creating a session

   - There are different patterns to create sessions:

   1. Context Manager

   - Basic Usage

     ```python
     from sqlalchemy.orm import Session

     stmt = text("SELECT x,y FROM some_table WHERE Y > :y ORDER BY x,y").bindparams(y=6)
     with Session(engine) as session:
         result = session.execute(stmt)
         for row in result:
             print (f"x: {row.x} y: {row.y}")

     ```

   - Commit as you go approach

   ```python
   with Session(engine) as session:
        result = session.execute(text("UPDATE some_table SET y=:y WHERE x=:x"),
        [
            {"x":1,"y":1},
            {"x":2,"y":2}
            ]
        )
        session.commit()

   ```
