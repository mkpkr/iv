# Attribute Pattern

Reduce number of indexes on a polymorphic schema.

Imagine some documents have the same field keys, but some vary between documents - think of products, all have name, category, brand etc; then some will have power/voltage, while some will have flavour.

Attribute pattern allows us to index on the varied fields.

Transpose keys/values as array of sub-documents of the form:

{"k":"<key>","v":"<value>"}

We can even add qualifiers such as unit:

"specs": [  
    {"k":"watts", "v":"60"},  
    {"k":"brightness", "v":"500", "u":"lumens"}  
]  

and add an index:

{"specs.k": 1, "specs.v": 1, "specs.u": 1}