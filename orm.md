## Using the SQLAclhemy ORM

---

### Working with Session

1. Executing with an ORM Session

   - Most patterns and examples used by the SQLAlchemy Core also apply to the ORM Session.

   - The transactional/database object when using the ORM is called `Session`

     _The `Session` internally refers to the `Connection` object to emit SQL_
