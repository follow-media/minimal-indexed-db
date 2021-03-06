# minimalIndexedDB

Library to handle the simplest possible application of native IndexedDB (a basic single-table CRUD), with none of indexedDB complexities, in a super lightweight fashion.

## installation

`npm i minimal-indexed-db` with `--save-dev` if desired


## usage

The DB is meant to be instantiated and then it will be ready to be used. Note that it will create a DB if it doesn't exist. As the IndexedDB lib is asynchronous, so this is as well.

To use the DB initially, do it as in the example below. The first parameter is the base/table name, the second is the primary key (which is optional and defaults to _"id"_).

```javascript
const dbPromise = DB('sample', 'id');
```

This code above returns a promise that resolves with the DB handling methods (see below) when the base/table is initiated or created and rejects in case of any error.

```javascript
DB('sample', 'id').then((db) => {
    db.put({ id: 1, name: 'John' });
});
```

After that, you can access the base/table anytime through `DB(dbName)` to have access to the handling methods. Bear in mind that once the key is configured it is not chengeable. With an existing database you can do:

```javascript
DB.use('sample').then((db) => {
    db.put({ id: 1, name: 'John' });
    db.getEntry(1); // returns { id: 1, name: 'John' }
});
```

### Handling methods

- `<instance>.getEntry(key)`: gets the entry by the primary key provided (or undefined if nothing was found)
- `<instance>.getAll()`: gets an array with all entries
- `<instance>.put()` (alias: `<instance>.add()`): inserts an entry or updates it, if the key provided in the entry object already exists. An array can be provided to insert many entries at once.
- `<instance>.deleteEntry(key)`: deletes the entry with the primary key provided
- `<instance>.deleteAll()` (alias: `<instance>.flush()`): deletes all entries on the base/table
- **new** `<instance>.count()`: returns the total number of entries

All those methods above return a promise that resolves when the process is done (with the result of the query, when it is a query) and rejects in case of any error. Follow the examples above:

```javascript
const db = await DB('sample');
await db.put([
    { id: 1, name: 'John' },
    { id: 3, name: 'Brian' }
]);
const oldEntry = await db.getEntry(3); // "oldEntry" returns { id: 3, name: 'Brian' }
await db.deleteEntry(3); // delete the record for the key provided
const newEntry = await db.getEntry(3); // "newEntry" returns undefined
const totalEntriesNumber = await db.count(); // "totalEntriesNumber" is 1
```

Note that a compatibility lib like `babel` is needed to use `await` as in the examples above.

## testing and linting

```
npm test
```
Tests include linting, but when only the linting is desired the command `npm run lint` can be run.
