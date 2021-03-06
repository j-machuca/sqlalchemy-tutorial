## [Working with Database Metadata](https://docs.sqlalchemy.org/en/14/core/metadata.html)

### Emitting to the DB using CORE

#### Components

1. The 3 main components of the Database Metadata in SQLAlchemy are:

   - [MetaData](https://docs.sqlalchemy.org/en/14/core/metadata.html#sqlalchemy.schema.MetaData)
   - [Table](https://docs.sqlalchemy.org/en/14/core/metadata.html#sqlalchemy.schema.Table)
   - [Column](https://docs.sqlalchemy.org/en/14/core/metadata.html#sqlalchemy.schema.Column)

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

#### [Columns and Data Types](https://docs.sqlalchemy.org/en/14/core/types.html)

1. Declaring Simple Constraints

   1. Primary Key

      - We can declare what `Column` would be the _Primary Key_ for our table by setting the `primary_key` parameter on the `Column`.

        ```python
        Column('id',Integer,primary_key=True)

        ```

      - We can access the Table's primary key using the `Table.primary_key` attribute on the `Table` object.

        ```python

        user_table.primary_key
        # PrimaryKeyContraint(Column('id',Integer(),table=<user_account>,primary_key=True,nullable=False))

        ```

   2. Foreign Key

      - Foreign keys are commonly declared by explicitly declarring them using the `ForeignKey` object.

      - Datatype for the column is inferred form that of the related column.

        ```python
        from sqlalchemy import ForeignKey
        address_table = Table(
            "address",
            metadata_obj,
            Column('id',Integer,primary_key=True),
            Column('user_id',ForeignKey('user_account.id'),nullable=False),
            Column('email_address',String(50), nullable=False),
        )
        ```

#### Emiting using CORE

1. Creating the Database

   - To create the database we invoke the `MetaData.create_all()` method and providing it our `Engine` object to the `MetaData` object that contains the collection of `Table`s, `Column`s, and `Contraint`s.

     ```python
     metadata_obj.create_all(engine)
     ```

   - The `create_all` method contains some SQL to check the existence of a table before emmiting a _CREATE_ statement.
   - The `drop_all` method will emit a _DROP_ statement as well. _works in reverse order_

### Working with the ORM

#### Defining the tables

1. Setting up the Registry

   - When using the ORM, the `MetaData` collection is still present but it is contained within an ORM-only object called the `registry`.

     ```python
     from sqlalchemy.orm import registry
     mapper_registry = registry()

     mapper_registry.metadata
     #MetaData()
     ```

   - Instead of declaring `Table` objects we now declare them indirectly by extending the `Base` class.

     ```python
         Base = mapper_registry.generate_base()
     ```

     **TIP**

     - There is a shortcut to create the registry and the base in one step

     ```python

     from sqlalchemy.orm import declarative_base
     Base = declarative_base()

     ```

2. Declaring Mapped Classes

   - To declare Classes for the ORM we will use the `Base` as the base class for the objects.

     ```python
     from sqlalchemy.orm import relationship

     class User(Base):
      __tablename__ = 'user_account'

      id = Column(Integer,primary_key=True)
      name = Column(String(30))
      fullname = Column(String)

      addresses = relationship("Address",back_populates="user")

      def __repr__(self):
        return f"User(id={self.id!r}, name={self.name!r}, fullname={self.fullname!r})"


      class Address(Base):
        __tablename__ = 'address'

        id = Column(Integer,primary_key=True)
        email_address = Column(String,nullable=False)
        user_id = Column(Integer,ForeignKey('user_account.id'))

        user = relationship("User",back_populates='addresses')

        def __repr__(self):
          return f"Address(id={self.id!r},email_address={self.email_address!r})"

     ```

   - The classes above are now mapped and available to the ORM and include the `Table` objects generated in the mapping process.

     ```python
     User.__table__
     ###
       Table('user_account', MetaData(),
       Column('id', Integer(), table=<user_account>, primary_key=True, nullable=False),
       Column('name', String(length=30), table=<user_account>),
       Column('fullname', String(), table=<user_account>), schema=None)
     ###
     ```

3. Mapped Class Details

   - An autogenerated **\_\_init\_\_** method.
   - An optional **\_\_repr\_\_** method
   - The `relationship` construct on both classes indicate to the ORM refer to each other in a [one-to-many](https://docs.sqlalchemy.org/en/14/glossary.html#term-one-to-many) / [many-to-one](https://docs.sqlalchemy.org/en/14/glossary.html#term-many-to-one) relationsip.

4. Emitting DDL to the Database - ORM

   - Emitting to the database is not any different from the process using the `Core`.
   - We can still use the `MetaData.create_all` as before.

   ```python
   # emit CREATE statement given the ORM registry
   mapper_registry.metadata.create_all(engine)

   # the identical MetaData object is also present on the declarative base

   Base.metadata.create_all(engine)

   ```

5. Combine Core Tables with ORM Declarative

   - We can combine the Tables in the Core with the ORM by passing the `Table` object to the `\_\_table\_\_` attribute directly.

   ```python
   mapper_registry = registry()
   Base = mapper_registry.generate_base()

   class User(Base):
      __table__ = user_table

      addresses = relationship("Address", back_populates="user")

      def __repr__(self):
          return f"User({self.name!r}, {self.fullname!r})"

   class Address(Base):
      __table__ = address_table

      user = relationship("User", back_populates="addresses")

      def __repr__(self):
          return f"Address({self.email_address!r})"

   ```
