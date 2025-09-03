A single document should contain only data your application will use. You should use other structures for historical data if it will not be used (e.g don't try and store a user's profile and its entire history in one document).

Your working set should fit in RAM (your data that is frequently accessed is called your working set - the size of this should fit in RAM to prevent disk access).

# Modelling Steps

1. List read operations
2. List write operations
3. Identify the durability of each write operation
4. Quantify each operation in terms of latency and frequency
5. Identify the relationships between the data
6. Apply schema design patterns

# Write Durability

- w = majority

	- money
	- account information
	- anything you can't lose

- w = 1

	- log data
	- sensor data

- w = 0 (fire and forget)

	- stock quotes (few per second)

# Throughput

- A high spec host with many cores can sustain around 100k operations per second.
- So to handle 1,000,000 writes per second you'd need at least 10 shards.

# Optimising for Writes

- distribute evenly while sharding
- reduce/group writes
- analytic queries can be run on dedicated analytics nodes to keep the primary free to accept writes

# Relationships

Each type of relationship can be represented in several ways.

We must choose representations of our relationships based on how we query them, and how it fits with our writes i.e. we don't want to have to update hundreds of documents that contain duplicate data if the referenced data is updated a lot.

# One-to-One

- Prefer embedding over referencing.
- Use subdocuments to organise related fields (e.g. address).

# One-to-Many

A mother may have one or more children.

A twitter user may have one or more followers.

Should these be modelled similarly? No.

A mother would have on average 2 children, and there is a limit not much higher than that. Embedding would work fine for the relationship.

A twitter user could have millions of followers - see "One-to-Zillions" (we must reference on the zillions side).

Note that twitter user and their followers is a many to many, but as we will see, we usually treat many to many as one to many.

Prefer embedding over referencing for simplicity, or when there are a small number of referenced documents.

Prefer referencing when the associated documents are not always needed with the most often queried documents.

## Embedding

Embedding is typically used when the number of embedded documents is small.

We can embed either on the "one" side, or on the "many" side.

Embed in the entity that is most queried

### Embed on "One" Side

We can use a multikey index to index the array of nested documents.

### Embed on "Many" Side

Less common, embedded object is duplicated.

Used if many side is queried more often, and if one side will not change (would mean updating many documents).

e.g. Address -< Order

We would probably embed address into order document because the access pattern is focussed on orders, and there could be a lot of orders (unlimited) for an address.

## Referencing

We can reference either on the "one" side, or on the "many" side.

### "Many" Side (most common – similar to relational)

Zip Code -< Store

{

  "_id" : ObjectId("590a530e3e37d79acac26a41"),  
  "store_name" : "Best Buy",  
  "zip_code": "02571",  
}

### "One" Side (less common)

User -< Address

{

  "_id" : ObjectId("590a530e3e37d79acac26a41"),  
  "name" : "alex",  
  addresses:  
    [  
        ObjectID('590a56743e37d79acac26a44'),  
        ObjectID('590a56743e37d79acac26a46'),  
        ObjectID('590a56743e37d79acac26a54')  
    ]  
}  
  
> person = db.Person.findOne({"name":"mary"})  
> addresses = db.Addresses.find({_id: {$in: person.addresses} })  

Cascade deletes are not supported by MongoDB and must be managed by the application.

# One-to-Zillions

A special case of one-to-many where the many could be 10s of thousands or millions.

We must reference the one in the many side for a zillions relationship.

- Embedding on the "one" side doesn't work as we would embed zillions.
- Embedding on the "zillions" side means a LOT of duplication.
- Referencing on the one side means an unbounded array of potentially many millions of references.

# Many-to-Many

In relational DBs this would be solved with two one-to-many relationships and a join table. We do not have to do this in a document db.

Most of the time we only care about one direction of the relationship, in which case we can trick the schema to be one-to-many. For example a shopping cart has many items, but each item can belong to many carts. We do not need both directions of this relationship in our query patterns. We only care about cart -< item.

Prefer embedding for data that is primarily static over time or that we only care about the state at the time of adding. Prefer referencing to avoid managing duplication.

## Embed in Main Side

We can simply embed the the item into the carts - with the caveat that if an item description changes, it will have to be updated in many cart documents, or we accept that the data represents the item at the time it was added, and may not be up to date.

We still need to maintain the "source" collection separately, e.g. items.

We can index on the array of embedded data using multikey index.

## Reference in Main Side

We could also reference the items in the cart, as we did with the one-to-many referencing, and this would keep the data up to date.

Referenced data could be retrieved with one query, joined with $lookup

## Reference in Secondary Side

Need secondary query to get more information if you query main and want secondary, need to query secondary where field in ...