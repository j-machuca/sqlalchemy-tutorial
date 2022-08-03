## Selecting Rows with Core or ORM

- For both Core and ORM the `select()` function to generate a `Select` construct which is used for all _SELECT_ queries.
- The construct is passed to `Connectio.execute()` and `Session.execute()` the _SELECT_ statement is emmitted in
  the current transaction and the result rows are returned in a `Result` object

  **For ORM specific features check [ORM Querying Guide](https://docs.sqlalchemy.org/en/14/orm/queryguide.html)**

### The Select() SQL Expression Construct

1. The `select` construct builds the statement int he same way the `insert` statement does, where each method builds more onto the object.

   ```python
   from sqlalchemy import select
   stmt = select(user_table).where(user_table.c.name == "spongebob")
   print(stmt)
   # SELECT user_account.id, user_account.name, user_account.fullname
   # FROM user_account
   # WHERE user_account.name = :name_1
   ```

2. To run the statement we pass it to an excecution method which returns `Row` objects.

   ```python
   with engine.connect() as conn:
     for row in conn.execute(stmt):
       print(row)

   # BEGIN (implicit)
   # SELECT user_account.id, user_account.name, user_account.fullname
   # FROM user_account
   # WHERE user_account.name = ?
   # [...] ('spongebob',)

   # Result

   # (1, 'spongebob', 'Spongebob Squarepants')

   ```
