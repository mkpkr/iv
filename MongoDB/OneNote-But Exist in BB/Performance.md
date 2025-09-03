# Index Order: Equality, Sort, Range

We want to order the index from most selective to least.

If you see your query is examining many more index keys than documents returned, it is a clue that your index is ordered wrong.

When we perform an equality query, it is very selective (we're only selecting these exact matching documents), when we perform a range query, it is not very selective (we may select half of our documents for example).

So the index should first be elements that you will be using for equality queries, and lastly elements you'll do range queries on.

Finally, an index can only be used for filtering AND sorting if the keys in our predicate are equality conditions.

For this reason, we should put the field we will use for sorting AFTER the equality field but BEFORE the range field in our index.

So the order of the index should be fields used for equality, sort, range.

```bash
{ "first_name": 1, "address.state": -1, "address.city": -1 }

  
db.people.find({ "first_name": "Jessica" })
         .sort({ "address.state": 1, "address.city": 1 })
```

*Fill this with more including range when get more examples*

# Single Key Index

```
db.people.find({"ssn":"720-38-5636"}).explain("executionStats");  
```

```
"queryPlanner" : {
               ...
                "winningPlan" : {
                        "stage" : "COLLSCAN",

"executionStats" : {
                "nReturned" : 1,
                "executionTimeMillis" : 41,
                "totalKeysExamined" : 0,
                "totalDocsExamined" : 50474,
```

- COLLSCAN - collection scan (search one by one)
- nReturned - number of documents returned by the query
- totalKeysExamined - index keys used
- totalDocsExamined - number of documents query had to look at

```
db.people.createIndex({"ssn":1}); 
``` 

Re-run query:

```
"queryPlanner" : {  
               ...  
                "winningPlan" : {  
                        "stage" : "FETCH",  
                        "inputStage" : {  
                                "stage" : "IXSCAN",  
"executionStats" : {  
                "executionSuccess" : true,  
                "nReturned" : 1,  
                "executionTimeMillis" : 0,  
                "totalKeysExamined" : 1,  
                "totalDocsExamined" : 1,  
```

- IXSCAN - index scan
- now only 1 document examined (and 1 index key)

## Subdocument Index

```
db.example.createIndex({"subDocument.indexedField":1});  
```

Now the index will be used when we find on subDocument.indexedField

Remember, don't index on the sub document field itself, because then we'd have to query by entire subdocument. Better to index using dot notation on the fields we care about. If we need to index on more than one field we use a compound index (info later).

## Range Queries

```
db.people.find({"ssn" : {"$gte" : "555-00-0000", "$lt" : "556-00-0000"}}).explain("executionStats");  
```

This uses the index we created earlier:

- IXSCAN
- nReturned: 49
- totalKeysExamined: 49
- totalDocsExamined: 49

## Querying Multiple Fields (With Only One Index)

What if we query ssn (indexed) and last_name (not indexed):

```
db.people.find({"ssn" : {"$gte" : "555-00-0000", "$lt" : "556-00-0000"}, "last_name" : {"$gte" : "H"}}).explain("executionStats");  
```

This is still able to use our ssn index:

```
"winningPlan" : {
                "stage" : "FETCH",
                "filter" : {
                        "last_name" : {
                                "$gte" : "H"
                        }
                },
                "inputStage" : {
                        "stage" : "IXSCAN",
                        "keyPattern" : {
                                "ssn" : 1
                        },



 "executionStats" : {
                "executionSuccess" : true,
                "nReturned" : 37,
                "executionTimeMillis" : 0,
                "totalKeysExamined" : 49,
                "totalDocsExamined" : 49,

```

The index was used to get the 49 documents in the ssn range, then it filtered those using last_name to return 37 documents.

Note the field order does not matter in this case, if last_name was first in the query it will still use the ssn index.

# explain()

Note that stages will go backwards so the latest will be towards the top, then it will show the input stage for that stage. So first stage will be furthest down/most inner

We can either append .explain() to our query, but it is recommended to create an explainable object:

```
exp = db.people.explain()  
  
exp.find({"address.city":"Lake Meaganton"})  
```

This will explain what would happen, without actually running the query (helpful if you have a server under heavy load).

We can pass parameters:

```
#default setting - does not execute the query  
exp = db.people.explain("queryPlanner")  
  
#actually executes the query  
exp = db.people.explain("executionStats")  
  
#actually executes the query  
#verbose: show alternative plans that were considered  
exp = db.people.explain("allPlansExecution")
```

## Compass

Compass gives us a great visual view into explain:

![[Pasted image 20250903173210.png]]

# Sorting

Sorting can be done either in memory or using an index.

## In Memory

Documents are stored in an unknown order on disk; when a query returns them, it does so in the order it finds them.

When we add a sort to our query, the documents must be read into RAM and a sorting algorithm performed on them.

The server will abort sorts if 32MB memory or more is used.

## Using an Index

Fields are sorted in an index in the order specified in index creation.

The server can take advantage of this: if the query uses an index scan, the order of documents is guaranteed to be sorted by index keys.

This means there is no need to perform an in memory sort as the documents will be fetched from the server in sorted order.

Example: Sort on Non-Indexed Field

```
db.people.find({}, {_id : 0, last_name: 1, first_name: 1, ssn: 1}).sort({last_name: 1}).explain("executionStats")  
```

The following shows the query does **collection scan(COLLSCAN) then in memory sort (SORT)**, examining 0 index keys.

```
"queryPlanner" : { 
	"winningPlan" : { 
		"stage" : "SORT", 
		"sortPattern" : { "last_name" : 1 }, 
		"memLimit" : [33554432](https://bitbucket.org/mkpkr/iv/commits/33554432), 
		"type" : "simple", 
		"inputStage" : { "stage" : "PROJECTION_SIMPLE", ... 
				"inputStage" : { "stage" : "COLLSCAN", "direction" : "forward" } } }, "rejectedPlans" : [ ] }, 

"executionStats" : { 
	"executionSuccess" : true, 
	"nReturned" : 50474, 
	"executionTimeMillis" : 103, 
	"totalKeysExamined" : 0, 
	"totalDocsExamined" : 50474,
```

Example: **Sort on Indexed Field**  

```
db.people.find({}, {_id : 0, last_name: 1, first_name: 1, ssn: 1}).sort({ssn: 1}).explain("executionStats")
```

The following shows the query does **index scan (IXSCAN)**, examining all 50474 index keys.

```
"queryPlanner" : {
            "plannerVersion" : 1,
            "namespace" : "test.people",
            "indexFilterSet" : false,
            "parsedQuery" : {

            },
            "winningPlan" : {
                    "stage" : "PROJECTION_SIMPLE",
                    "transformBy" : {
                            "_id" : 0,
                            "last_name" : 1,
                            "first_name" : 1,
                            "ssn" : 1
                    },
                    "inputStage" : {
                            "stage" : "FETCH",
                            "inputStage" : {
                                    "stage" : "IXSCAN",
                                    "keyPattern" : {
                                            "ssn" : 1
                                    },
                                    ...
    },
    "executionStats" : {
            "executionSuccess" : true,
            "nReturned" : 50474,
            "executionTimeMillis" : 123,
            "totalKeysExamined" : 50474,
            "totalDocsExamined" : 50474,

```

If we repeat the query, sorting backward then we will still use the index. With a single index field it is always possible to traverse the index backwards.

?????????/why is the index scan slower than collection scan & in memory sort

# Compound Indexes

Compound indexes are not 2 dimensional, they contain multiple elements but are still flat:

![[Pasted image 20250903173918.png]]

This is just like a phone book.

With an index {last_name : 1, first_name : 1} in order to find everyone with a certain last name, we can use the index. But in order to find everyone with a certain first name, we can't.

Example (using Compass as it provides easier to read output):

```
{"last_name":"Frazier", "first_name": "Jasmine"}  
```

As expected, all records are examined, no index keys are used.
![[Pasted image 20250903173951.png]]

Here is the same explain when we add an index on {last_name : 1}

![[Pasted image 20250903174007.png]]
And when we add an index on {last_name : 1, first_name : 1}

![[Pasted image 20250903174026.png]]

Because the indexes are ordered; this index also works for queries such as:

```
{"last_name":"Frazier", "first_name": {$gte : "L"}}  
```

## Index Prefixes

Index prefixes are a continuos subset of indexes from our compound index:

Given the index:

```
{item : 1, location : 1, stock : 1}  
```

There are 2 index prefixes:

```
{item : 1}  
{item : 1, location : 1}  
```

Both of these prefixes can be used as indexes in our queries.

If your application has queries that use subsets of indexes, do not build multiple indexes to fit both.

If we ran a query that used item, location, and color; we could still use the index prefix {item : 1, location : 1} from our index, it just wouldn't be as efficient as having a query on the 3 exact fields we used (we would see more keys examined than necessary).


# Sorting with Compound Indexes

Note that what we've talked about with compound indexes and index prefixes also applies to sorting.

But there's more, we can combine a find and sort to use an index or index prefix.

Given the index:

```
{job : 1, employer : 1, last_name : 1}  
```

When we query:

```
db.people.find({job: "Graphic Designer", employer: "Facebook"}).sort({last_name : 1})  
```

The index will be used.

However:

```
db.people.find({job: "Graphic Designer",}).sort({last_name : 1})  
```

The index cannot be used since job, last_name is not an index or index prefix.

## Sort Direction with Compound Indexes

We saw with a single field index, we can sort using the index in either direction.

This is also true for compound indexes.

Given the index `{a: 1, b: -1, c: 1}`

We can walk the index forward:

```
.sort({a: 1, b: -1, c: 1})  
```

or backwards:

```
.sort({a: -1, b: 1, c: -1})  
```

Or use index prefixes of either:

```
//forward.sort({a: 1})  
.sort({a: 1, b: -1})  
  
//backward.sort({a: -1})  
.sort({a: -1, b: 1})
```

# Multikey Indexes (Arrays)

A multikey index is an index on an array field.

Its called this because for each entry in the array, the server will create a separate index key.

For example there will be an index key for T-Shirts, Clothing, and Apparel:

```
{  
    ...  
    categories: ["T-Shirts", "Clothing", "Apparel"],  
    ...  
}  
```
  
db.products.createIndex({categories: 1, })  

We can also index on fields in arrays of subdocuments:

```
{  
    ...  
    stock: [  
        {size: "S", color: "red", quantity: 25},  
        {size: "S", color: "blue", quantity: 10},  
        {size: "M", color: "blue", quantity: 50}  
    ],  
    ...  
}  

db.products.createIndex({"stock.quantity": 1, })  
```
  


The server would create an index key for each of the subdocuments.

We can have at most one field in an index whose value is an array. (If you could create multiple array fields in a compound index you'd have a cartesian product of the arrays as indexes.) If you try to create an index with multiple fields that are arrays in at least one existing document, or if you try to insert a document with multiple array fields that match fields in a compound index, the operation will fail.

We must be careful with multikey indexes that our arrays don't grow too large.

Multikey indexes don't support covered queries.

# Partial Indexes

To improve write performance and save space in memory, we may want to only index a portion of the total documents in a collection.

Imagine a restaurant collection that rarely gets queries for restaurants over 3.5 stars, we may only want to index those that are gte 3.5 stars:

```
db.restaurants.createIndex(  
    {"address.city": 1, cuisine: 1},  
    {partialFilterExpression: {"stars": {$gte: 3.5}}}  
)  
```

This is especially helpful when we have multikey indexes with large arrays.

Note that in order to use a partial index, the query must include a filter expression that matches our partial index predicate, so in the above example, filtering on address,city would use a collection scan; we must also filter on {stars: {$gte:3.5}} or {stars : 4} etc. Remember that the partial filter expression MUST BE GUARANTEED TO MATCH your query predicate for the index to be used (otherwise some documents could be missed so MongoDB will just do a collection scan).

Note that _id and shard key index cannot be partial.

## Sparse Indexes

Special csase of partial indexes.

Only index when the field exists that we're indexing on, rather than write index key with null value:

```
db.restaurants.createIndex(  
    {stars: 1},  
    {sparse: true}  
)  
```

Same as:

```
db.restaurants.createIndex(  
    {stars: 1},  
    {partialFilterExpression: {"stars": {$exists: true}}}  
)
```

But partial indexes are more expressive (and therefore recommended).

With partial indexes we can check for existence of fields other than the ones we're indexing on:

```
db.restaurants.createIndex(  
    {stars: 1},  
    {partialFilterExpression: {"cuisine": {$exists: true}}}  
)  
```

You cannot specify sparse and partialFilterExpression in the same index.

# Text Index

```
db.products.createIndex({productName: "text"})  
  
db.products.find({$text: {$search: "t-shirt"}})  
```

The server will process the field and create an index key for every unique word in the string (similar to multikey index).

Note that "t-shirt" would become "t" and "shirt".

You must watch text indexes closely and make sure they don't grow larger than RAM capacity.

Also be aware it takes longer to build this index than a regular index.

We will see a decreased write performance.

We can create compound indexes that reduce the amount of keys needed to be looked at in a text query:

```
db.products.createIndex({category: 1, productName: "text"})  
  
db.products.find({    category: "Clothing",    $text: {$search: "t-shirt"}  
})  
```

## Projecting Score

Text queries logically OR each word in the query. So searching "mongo best" will show documents with "mongo is the best" and "mongo is the worst".

To show the most relevant results, we can project the score of the result and sort by that:

```
db.products.find({$text: {$search: "mongo best"}}, {score: {$meta: "textScore"}}).sort({score: {$meta: "textScore"}}) 
``` 

# Covered Query

A covered query is a query that can be satisfied entirely using an index and does not have to examine any documents.

An index covers a query when all of the following apply:

- all the fields in the query are part of an index
- all the fields returned in the results are in the same index (typically this means _id: 0 and all index fields : 1)
- no fields in the query are equal to null (i.e. {"field" : null} or {"field" : {$eq : null}} )

You can't cover a query if

- any indexed fields are arrays
- any indexed fields are embedded documents
- when run against a mongos if the index does not contain the shard key

# Aggregation Performance

Once the pipeline reaches a stage that cannot use an index, none of the following stages will use an index, the querry optimizer can try to move stages around to use an index but we should aim to order them properly ourselves.

We can pass , {explain: true} to the aggregate method to show the query plan.

$match (at beginning) and $sort (not preceded by $group, $unwind or $project) can use indexes, and if we are using a $limit it should be before sort.

The $geoNear pipeline operator takes advantage of a geospatial index. When using $geoNear, the $geoNear pipeline operation must appear as the first stage in an aggregation pipeline.

# Aggregation Memory Constraints

- Result document must be < 16MB
- 100MB of RAM per stage
	- use indexes
	- if you still hit the limit you can add , {allowDiskUse: true} as an absolute last resort (it will be much slower and therfore typically only used in batch queries)