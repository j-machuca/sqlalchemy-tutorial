## Selecting Rows with Core or ORM

- For both Core and ORM the `select()` function to generate a `Select` construct which is used for all _SELECT_ queries.
- The construct is passed to `Connectio.execute()` and `Session.execute()` the _SELECT_ statement is emmitted in
  the current transaction and the result rows are returned in a `Result` object

  **For ORM specific features check [ORM Querying Guide](https://docs.sqlalchemy.org/en/14/orm/queryguide.html)**

### The Select() SQL Expression Construct
