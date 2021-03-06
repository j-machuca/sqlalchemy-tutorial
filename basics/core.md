## Using the SQLAlchemy Core

### Creating the Engine

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

### Working with transactions.

#### There are Two primary endpoints:

1.  The `Connection` object.

    - Use the context manager to get a connection object from the engine.

      ```python
      from sqlalchemy import text

      with engine.connect() as conn:
         result = conn.execute(text("SELECT 'hello world'"))
         print(result.all())
      ```

    - In order for changes to be saved to the DB they need to be committed we need to call the `Connection.commit()` method.

      _Setting the `autocommit` parameter on the engine is possible._

    #### There are two approaches:

    1. Commit as you go.

       We need to call the `Connection.commit()` inside the context manager creating the connection.

       ```python
       with engine.connect() as conn:
          conn.execute(text("CREATE TABLE some_table(x int,y int)"))
          conn.execute(text("INSERT INTO some_table (x,y) VALUES (:x,:y)"),
          [{"x":1,"y":1},{"x":2,"y":2}])
          conn.commit()
       ```

    2. Block commit

       This is the often preffered methong for production because it indicates the intention of the block.
       This method calls the `Engine.begin` method that handles getting the connection and committing the transactions at the end if succesful
       or performing a **ROLLBACK** if an execption was raised.

       ```python
         with engine.begin() as conn:
            conn.execute(
               text("INSERT INTO some_table (x,y) VALUES (:x,:y)"),
               [{"x":1,"y":1},{"x":2,"y":2}]
            )

       ```

2.  The `Result` object.

    - When we execute a statement with the `Connection.execute` method it returns a `Result` object.

      _When executing the `Session.execute` method when using the ORM we get the same `Result` interface used by **Core**._

      #### Fetching rows

      1.  When fetching rows from the database the `Result` object is returned and represents an iterable object of result rows.

      ```python
      with engine.connect() as conn:
         result = conn.execute(text("SELECT x,y FROM some_table"))
         for row in result:
            print(f"x:{row.x} y:{row.y}")
      ```

      2. The `Result` Object has several methods for fetching and transforming rows.

         1. Fetching methods

            1. `Results.all` method.

         2. Access rows.

            1. Tuple Assignment (named tuple)

               ```python
               result = conn.exexute("SELECT x,y FROM some_table")

               for x,y in result:
                  print(x,y)

               ```

            2. Integer Index

               ```python
               result = conn.exexute("SELECT x,y FROM some_table")

               for row in result:
                  x= row[0]
                  y = row[1]
               ```

            3. Attribute name. (Matches the name of the column)

               ```python
               result = conn.exexute("SELECT x,y FROM some_table")

               for row in result:
                  x = row.x
                  print(f"Row: {x} {row.y}")
               ```

            4. Mapping Access

               - This is a read only version of the common dict object.

               ```python
               result = conn.exexute("SELECT x,y FROM some_table")

               for dict_row in results.mappings():
                  x = dict_row['x']
                  y = dict_row['y']

               ```

      3. Queries and Parameters.

         1. Sending Parameters

            - The `Connection.execute` method accepts parameters that are refered as bound parameters.

              _The construct accepts the parameters using a colon format :param_name, the actual value is passed as the second argument to the `Connection.execute` method in form of a dictionary._

              ```python
               with engine.connect() as conn:
                  result = conn.execute(
                     text("SELECT x,y FROM some_table WHERE y> :y"),
                     {"y":2}
                  )
                  for row in result:
                     print(f"x: {row.x}  y:{row.y}")

              ```

              **Never Stringify values directly to a textual SQL - can lead to SQLInjection attacks.**

         2. Sending Multiple Parameters

            - For statements that operate upon data, but do not return result sets we can send multiple parameters to the `Connection.execute` method by passing a list of dictionaries instead of a single disctionary. This allows a single SQL statement to be invoked against each parameter set individually.

              ```python
              with engine.connect() as conn:
                 conn.execute(text("INSERT INTO some_table (x,y) VALUES (:x,:y)"),
                    [{"x":1,"y":1},{"x":2,"y":2}]
                 )
                 conn.commit()
              ```

            - Behind the scenes the `Connection` uses a `cursor.executemany` method.
            - This method performs the equivalent operation of invoking the given SQL statement against each parameter set individually.

              - The DBAPI may optimize this operation in different ways.

            - When using the ORM the technique is different.

            - Behind the scenes SQLAlchemy's `text` construct uses the `TextClause.bindparams` method to generate the SQL construct.

              ```python
              stmt = text("SELECT x,y FROM some_table WHERE y>:y ORDER BY x,y").bindparams(y=6)

              with engine.connect() as conn:
                 for row in result:
                    print(f"x:{row.x} y:{row.y}")
              ```
