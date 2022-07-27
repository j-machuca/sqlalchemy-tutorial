## [Working with Database Metadata](https://docs.sqlalchemy.org/en/14/core/metadata.html)

---

### Components

1. The 3 main components of the Database Metadata in SQLAlchemy are:

   - MetaData
   - Table
   - Column

#### MetaData Object

1. The `MetaData` object.

   - The `MetaData` object is the collection that will hold all the `Table` objects we create.
   - Creating a `MetaData` object is pretty simple.

     ```python
     from sqlalchemy import MetaData

     metadata_obj = MetaData()

     ```

   - Having a single `MetaData` object is the most common case.
   - Having multiple `MetaData` objects is also an option.
     _Most useful if a series of `Table` objects that are related to each other belong to the same `MetaData` collection._

#### Table Object

1. Setting up MetaData with Table Objects.

   - The `Table` objects can be declared **explicitly** from the source code.
   - The `Table` objects can also be **reflected** from what is already present in the database.

2. Working with the `MetaData` object

   - Declaring `Table` objects.

     ```python
         from sqlalchemy import Table,Column,String,Integer

         user_table = Table(
             "user_account",
             metadata_obj,
             Column('id',Integer,primary_key=True),
             Column('name',String(30)),
             Column('fullname',String),
         )
     ```

   - The collection of `Column` objects can be typically accessed via an associative array located at `Table.c`.

     ```python
     user_talbe.c.name
     # Column('name', String(length=30), table=<user_account>)

     user_table.c.keys
     # ['id', 'name', 'fullname']

     ```

#### Column Object
