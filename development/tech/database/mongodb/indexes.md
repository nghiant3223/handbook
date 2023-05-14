# MongoDB indexes
## Definition
- `Indexes` support the `efficient execution` of queries in `MongoDB`.
- `Without indexes`, `MongoDB` must perform a `collection scan`, i.e. `scan every document` in a `collection`, to select those `documents` that `match` the `query statement`.
- If an `appropriate index` exists for a query, MongoDB can use the `index` to `limit` the `number of documents` it must `inspect`.
- `Indexes` are `special data structures` that store a `small portion` of the collection's `data set` in an `easy` to `traverse` form.
- The `index` `stores` the `value` of a `specific field` or `set of fields`, `ordered by` the `value` of the field.
- The `ordering` of the `index` entries `supports` efficient `equality matches` and `range-based query` operations.
- In addition, MongoDB can `return sorted results` by `using the ordering` in the `index`.
- Fundamentally, `indexes` in MongoDB are `similar to` `indexes` in other `database systems`. 
- MongoDB defines `indexes` at the `collection level` and supports `indexes` on `any field` or `sub-field` of the documents in a `MongoDB` collection.
## Default _id Index
- `MongoDB` creates a `unique index` on the `_id` field during the `creation` of a `collection`. 
- The `_id` index `prevents clients` from `inserting two documents` with the `same value` for the `_id` field.
- You `cannot` `drop` this index on the `_id` field.
- In `sharded clusters`, `if` you do `not use` the `_id` field as the `shard key`, then `your application` must `ensure` the `uniqueness` of the `values` in the `_id` field to `prevent errors`. This is `most-often done` by using a `standard auto-generated ObjectId`.

## Create an Index
To create an index in the Mongo Shell, use db.collection.createIndex().
```
db.collection.createIndex( <key and index type specification>, <options> )
```

- creates a single key descending index on the name field:
```
db.collection.createIndex( { name: -1 } )
```
- `MongoDB` indexes use a `B-tree` data structure.
- You can create `indexes` with a `custom name`
```
db.products.createIndex(
  { item: 1, quantity: -1 } ,
  { name: "query for inventory" }
)
```
## Index Types
- `MongoDB` provides a number of `different index types`.
### Single Field
- MongoDB supports the creation of `user-defined ascending/descending indexes` on `a single field` of a document.
- For `a single-field index` and `sort` operations, the `sort order` (i.e. `ascending` or `descending`) of the `index` key does `not matter` because `MongoDB` can `traverse` the index in `either direction`.
### Compound Index
- `MongoDB` also supports `user-defined indexes` on `multiple` fields.
- The `order` of `fields` `listed` in a compound `index` has `significance`. For instance, if a compound index consists of `{ userid: 1, score: -1 }`, the index `sorts` first by `userid` and then, `within` each `userid` value, sorts by `score`.
- For `compound indexes` and `sort` operations, the `sort order` (i.e. ascending or descending) of the index keys can determine `whether` the index can `support` a `sort` operation.
### Multikey Index
- MongoDB uses `multikey indexes` to index the `content` stored in `arrays`. 
- If you `index` a `field` that holds an `array` value, MongoDB creates `separate index entries` for every `element` of the array.
- These `multikey indexes` allow queries to `select` documents that `contain arrays` by matching on `element` or `elements` of the `arrays`.
- `MongoDB` `automatically` determines `whether` to `create` a `multikey index` if the `indexed field` contains `an array` value; you `do not need` to explicitly `specify` the `multikey type`.
### Geospatial Index
- To support `efficient queries` of `geospatial coordinate` data, MongoDB provides `two` special indexes: `2d indexes` that uses `planar geometry` and `2dsphere indexes` that use `spherical geometry` to return results.
- This index is a `sparse index`.
### Text Search Indexes
- For `data` hosted on `MongoDB Atlas`, you can support `full-text search` with `Atlas Search indexes`
- For `self-managed` deployments, `MongoDB` provides a `text index type` that supports `searching` for `string content` in a `collection`.
- This index is a `sparse index`
### Hashed Indexes
- To support `hash based sharding`, MongoDB provides a `hashed index` type, which `indexes` the `hash of the value` of a `field`.
- These indexes have a `more random distribution` of `values` along their range, but `only support equality matches` and `cannot support range-based` queries.
### Clustered Indexes
- Starting in `MongoDB 5.3`, you can create `a collection` with a `clustered index`. 
- Collections created with a `clustered index` are called `clustered collections`.
## Index Properties
### Unique Indexes
- The `unique` property for an index causes MongoDB to `reject duplicate values` for the `indexed field`. 
### Partial Indexes
- `Partial indexes` only index the `documents` in a collection that `meet` a specified `filter expression`.
- By indexing a `subset` of the `documents` in a `collection`, partial indexes have `lower storage requirements` and `reduced performance costs` for index `creation` and `maintenance`.
- Example
```
CREATE INDEX messages_todo
          ON messages (receiver)
       WHERE processed = 'N'
```

### Sparse Indexes
- The `sparse` property of an index ensures that the `index only contain entries` for `documents` that `have the indexed field`.
- The index `skips documents` that do `not have` the `indexed field`.
- Can `combine` the `sparse` index option with the `unique` index option to `prevent` inserting `documents` that have `duplicate values` for the `indexed field`(s) and `skip indexing documents` that `lack` the `indexed field(s)`.

### TTL Indexes
- `special indexes` that `MongoDB` can use to `automatically remove documents` from a `collection` after `a certain amount of time`.
- This is `ideal` for `certain types` of information like `machine generated event data`, `logs`, and `session information` that only need to `persist in a database` for `a finite amount of time`.

### Hidden Indexes
- `Not visible` to the `query planner` and `cannot be used` to `support a query`.
- By `hiding an index` from the `planner`, users can `evaluate` the `potential impact` of `dropping an index without actually dropping the index`.
- `If` the `impact` is `negative`, the `user` can `unhide` the `index` `instead of` having to `recreate a dropped index`.
- And because `indexes` are `fully maintained while hidden`, the `indexes` are `immediately available` for use `once unhidden`.
- `Except` for the `_id` index, you can `hide` any `indexes`.
## Index Use
- Indexes can `improve` the `efficiency` of `read` operations.

## Indexes and Collation
- `Collation` allows `users` to specify `language-specific rules` for `string comparison`, such as `rules` for `lettercase` and `accent marks`.
- To `use` an `index` for `string comparisons`, an `operation` must also specify the `same collation`.
- That is, an `index` with a `collation` `cannot support` an `operation` that performs `string comparisons` on the `indexed fields` if the `operation specifies a different collation`.
- For example, the collection `myColl` has an `index` on a `string` field category with the collation locale `"fr"`.
```
db.myColl.createIndex( { category: 1 }, { collation: { locale: "fr" } } )
```
- The following query operation, which specifies the `same collation` as the index, `can use the index`:
```
db.myColl.find( { category: "cafe" } ).collation( { locale: "fr" } )
```

- However, the following query operation, which `by default` uses the `"simple" binary collator`, `cannot use` the `index`:
```
db.myColl.find( { category: "cafe" } )
```

For a compound index where the index prefix keys are not strings, arrays, and embedded documents, an operation that specifies a different collation can still use the index to support comparisons on the index prefix keys.

For example, the collection myColl has a compound index on the numeric fields score and price and the string field category; the index is created with the collation locale "fr" for string comparisons:

db.myColl.createIndex(
   { score: 1, price: 1, category: 1 },
   { collation: { locale: "fr" } } )

The following operations, which use "simple" binary collation for string comparisons, can use the index:

db.myColl.find( { score: 5 } ).sort( { price: 1 } )
db.myColl.find( { score: 5, price: { $gt: NumberDecimal( "10" ) } } ).sort( { price: 1 } )

The following operation, which uses "simple" binary collation for string comparisons on the indexed category field, can use the index to fulfill only the score: 5 portion of the query:

db.myColl.find( { score: 5, category: "cafe" } )

For more information on collation, see the collation reference page.

The following indexes only support simple binary comparison and do not support collation:

text indexes,

2d indexes, and

geoHaystack indexes.

Covered Queries
When the query criteria and the projection of a query include only the indexed fields, MongoDB returns results directly from the index without scanning any documents or bringing documents into memory. These covered queries can be very efficient.

Diagram of a query that uses only the index to match the query criteria and return the results. MongoDB does not need to inspect data outside of the index to fulfill the query.
click to enlarge
For more information on covered queries, see Covered Query.

Index Intersection
MongoDB can use the intersection of indexes to fulfill queries. For queries that specify compound query conditions, if one index can fulfill a part of a query condition, and another index can fulfill another part of the query condition, then MongoDB can use the intersection of the two indexes to fulfill the query. Whether the use of a compound index or the use of an index intersection is more efficient depends on the particular query and the system.

For details on index intersection, see Index Intersection.

Restrictions
Certain restrictions apply to indexes, such as the length of the index keys or the number of indexes per collection. See Index Limitations for details.

Additional Considerations
Although indexes can improve query performances, indexes also present some operational considerations. See Operational Considerations for Indexes for more information.

During index builds, applications may encounter reduced performance or limited read/write access to the collection being indexed.

For more information on the index build process, see Index Builds on Populated Collections, especially the Index Builds in Replicated Environments section.

Some drivers use NumberLong(1) instead of 1 to specify the index order. The resulting indexes are the same.