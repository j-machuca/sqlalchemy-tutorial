### Inserting Rows with Core

1.  The insert() SQL Expression Construct.

    1. We can use an instance of the insert statement provided by sqlalchemy.

    ```python
    from sqlalchemy import insert
    stmt = insert(user_table).values(name="Spongebob",fullname="Spongebob Squarepants")
    ```

    2. The statement can be printed out.

    ```python
    print(stmt)
    # INSERT INTO user_account (name, fullname) VALUES (:name, :fullname)
    ```

    3. We can access the `compile` method to obtain the compiled object and access its values.

    ```python
    compiled = stmt.compile()
    print(compiled.params)
    #{'name': 'spongebob', 'fullname': 'Spongebob Squarepants'}
    ```

2.  Executing the Statement

    1. The insert statement does not return any rows, any if only one row is inserted, it will usually include the ability to return information about the column level default values generated during the **INSERT** most commonly the primary key.

    **Can be accessed by the `CursorResult.inserted_primary_key`**

        ```python
        with engine.connect() as conn:
            result = conn.execute(stmt)
            conn.commit()
            print(result.inserted_primary_key)
            #(1,)
        ```

    **As of version 1.4.8 the tuple returned is now a named tuple with a `Row` object.**

3.  Insert Construct and the "values" clause.

    1. The `Insert` construct generates the `VALUES` clause from the parameters passed to the `Connection.execute()` method.

       ```python
       with engine.connect() as conn:
           result = conn.execute(
               insert(user_table),
               [
                   {"name":"sandy", "fullname" : "Sandy Cheeks"},
                   {"name":"patrick", "fullname" : "Patrick Star"},
               ]
           )
           conn.commit()
       ```

       - In the example above we can see that the `Insert` construct can execute multiple SQL statements. We can pass a dictionary or a list of dictionaries to the `Connection.execute` method without having to explicitly spell out the `VALUES` clause.

    _Check out [Scalar Subquery](https://github.com/j-machuca/sqlalchemy-tutorial/blob/master/scalar_subquery.md)_

4.  INSERTING FROM SELECT

    1. The `Insert` construct can compose an _INSERT_ that gets rows directly from a _SELECT_ using the `Insert.from_select()` method.

       ```python
       select_stmt = select(user_table.c.id,user_table.c.name+"@aol.com")
       insert_stmt = insert(address_table).from_select(
           ["user_id","email_address"],select_stmt
       )

       print(insert_stmt)

       # INSERT INTO address (user_id, email_address)
       # SELECT user_account.id, user_account.name || :name_1 AS anon_1
       #FROM user_account

       ```

5.  INSERT ... RETURNING

    1. The `RETURNING` clause for insert statements is used automatically for supported backends to retrieve the primary key value as well as server defaults.

    2. We can also explicitly specify the `RETURNING` clause by using the `Insert.returning` method. This will return a `Result` object with rows that can be fetched.

       ```python
       insert_stmt = insert(address_table).returning(address_table.c.id,address_table.c.email_address)
       print(insert_stmt)

       # INSERT INTO address (id, user_id, email_address) VALUES (:id, :user_id, :email_address) RETURNING address.id, address.email_address
       ```

    3. The `RETURNING` clause can also be used with the `Insert.from_select` method.

       ```python
       select_stmt = select(user_table.c.id,user_table.c.name+"@aol.com")
       insert_stmt = insert(address_table).from_select(
           ["user_id","email_address"],select_stmt
           )
       print(insert_stmt)

       # INSERT INTO address (user_id, email_address)
       # SELECT user_account.id, user_account.name || :name_1 AS anon_1
       # FROM user_account RETURNING address.id, address.email_address

       ```

    _The `RETURNING` feature is also supported by `UPDATE` and `DELETE` statements. Generally the `RETURNING` feature is only supported by statment executions with a single set of parameters. `psycopg2` supports `INSERT` of many rows at once._
