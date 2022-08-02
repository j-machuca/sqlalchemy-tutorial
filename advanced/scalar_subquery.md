## Scalar Subquery

### Performing a Scalar Subquery

1. The main use for _Scalar Subqueries_ is to generate additional **VALUES** from the parameters from the main **QUERY**.

   _i.e getting the primary key of a user and use that to insert on a related table_

2. Basic Usage

   - We construct a _Scalar Subquery_ using the `select` construct and the parameters used in the _subquery_ are set up using and explicit bound parameter name using the `bindparams` construct.

     ```python
     from sqlalchemy import select,bindparams

     scalar_subq = (
         select(user_table.c.id).
         where(user_table.c.name==bindparams('username')).
         scalar_subquery()
     )

     with engine.connect() as conn:
         result = conn.execute(
             insert(address_table).
             values(user_id=scalar_subq),
             [
                 {"username": 'spongebob', "email_address": "spongebob@sqlalchemy.org"},
                 {"username": 'sandy', "email_address": "sandy@sqlalchemy.org"},
                 {"username": 'sandy', "email_address": "sandy@squirrelpower.org"},
             ]
         )
         conn.commit()

     ```
