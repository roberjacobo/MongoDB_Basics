# Welcome to MongoDB Basics by Roberto Jacobo

## Importing and Exporting Data
As we know MongoDB handle the data in JSON and BSON (Binary JSON) but, first we need to decide how we are going to handle the data.

JSON

    mongoimport
    mongoexport

BSON

    mongorestore
    mongodump

To connect to your Atlas cluster.
URI: Uniform Resourse Identifier
mongodb+srv://*user*:*password*@clusterURI.mongodb.net/database
srv - establishes a **secure** connection

## **Export data**

    mongodump --uri "<Atlas Cluster URI>"

> Exports data in **BSON**

    mongoexport --uri "< Atlas Cluster URI >"
			    --collection=<collection name>
				--out=<filename>.json  

> Exports data in **JSON**

## **Import data**



    mongorestore --uri "< Atlas Cluster URI >"
			     --drop dump

> Imports data in **BSON** dump

    mongoimport --uri "<Atlas Cluster URI>"
                --drop=<filename>.json

> Imports data in **JSON**

## Find()
with the mongo shell we are using this command and string to connect and find the DB information.

    mongo "mongodb+srv://sandbox.kgcwj.mongodb.net/myFirstDatabase" 
   
  you can add the username at the end like:
  

    "mongodb+srv://sandbox.kgcwj.mongodb.net/myFirstDatabase"  --username m001-student

to see the databases available

    show dbs
And you'll see something like in the command line:

    admin               0.000GB
    local               4.032GB
    sample_airbnb       0.051GB
    sample_analytics    0.009GB
    sample_geospatial   0.001GB
    sample_mflix        0.041GB
    sample_restaurants  0.006GB
    sample_supplies     0.001GB
    sample_training     0.042GB
    sample_weatherdata  0.002GB

> example from the mongo's database

|command| action |
|--|--|
| `show dbs` | Show all the db's in the project |
| `use < database name >` | Get into the db |
| `show collections` | Show all the collectionts in the database |
| `db.< db >.find({"< field >":"< value >"})` | Find all documents that match the indicated field and value  |

> If there are too much collectiont in return of .find() we can tap ***it*** to get more collections in the command prompt


## Inserting New Documents

> must know
> 
> "_id" unique identifier for a document in a collection "_id" is
> required in every MongoDB Document ObjectId() is the default value for
> the "_id" field unless otherwise specified
> if we try to insert a document that already exist in the collection with the same _id then we get a duplicate key error

    db.inspections.findOne()
   

> finds a random document in a collection

we can insert documents with the followind command example

    db.collection.insert({<doc:1>})
and we can insert many collections like this:

    db.collection.insert([{<doc:1>}.{<doc:2>}])

> using an array

The behavior of .insert() is to insert the documents following the order indicated in the array but we can insert unordered using {"ordered": false}
like this:

    db.collection.insert([{<doc:1>}.{<doc:2>}], {"ordered": false})

> if we try to insert many documents in a collection and there is an _id field that repeats mongo insert all the documents indicated before that error. If it's located in the second document indicated there only will be one document inserted.

    db.collection.insert([{ "_id": 1, "pet": "cat" }, { "_id": 1, "pet": "dog" }, { "_id": 3, "pet": "snake" }], {"ordered": true})

Here only be the first document inserted and if ordered is setted to false only one of the documents that has "_id": 1 and "_id": 3 would be inserted.

## Adding and Updating Documents
there is two ways to update documents 

    updateOne()

and 

    updateMany()
and they are used the same way as .find(), with the first {} as a filter. Like this:

    db.zips.updateMany({"city": "HUDSON"},{"$inc": {"pop": 10, "<field>": <increment value>, ...}})
increments in the field "pop" all the documents that have the field and value "city": "HUDSON"

    db.zips.updateOne({"zip": "12534"},{"$set": {"pop": 17834})
this $set command replace the value of the indicated field "pop": with the new 17834 value
if the field doesn't exist then will be created by $set

    db.zips.updateOne({"<field>": "<value>"},{"$push": {"<field2>": "<value2>"})
$push adds and element to an array field

## Deleting documents
there is two ways to delete documents 

    deleteOne()

and 

    deleteMany()

> they both work as the same way that find() and update()

and we can delete an entire collection with **.drop()**

    db.collection.drop()

> this returns a true, or false if the collection doesn't exist.

## Adavance CRUD Operations || Query Operators: Comparison
**Update operators** 
Example: *$inc, $set, $unset*

> $ has multiple uses
Precedes MQL operators
Precedes Aggregation pipeline stages
Allow access to Field Values

**Compatison operators**
|Operator| function |
|--|--|
| $eq | EQual to (===) |
| $ne | Not Equal to (!==) |
| $gt | Greater Than (>) |
| $lt | Less Than (<) |
| $gte | Greater Than or Equal to (>=) |
| $lte | Less Than or Equal to (<=) |

Example:

    db.collection.find({"tripduration": {"$lte": 70}})
to use multiple

   

    db.collection.find({ "tripduration": { "$lte" : 70 }, "usertype": { "$ne": "Subscriber" } })

**Logic Operators**
|Operator| function |
|--|--|
| $and | Match all the specified query clauses |
| $or | At least one of the query clauses is matched |
| $nor | Fail to match both given matches |
| $not | Negates the query requirement |

$and is used as the default operator when an operator is not specified.

    {sector: "Mobile food vendor - 881",  result: "warning" }

is the same as:

    { "$and": [{ sector:  "Mobile food vendor - 881"}, { result: "warning" }]}

**Implicit $and**

**$and** is used as the **default** operator when an operator is not specified.

Find which student ids are > 25 and < 100 in the sample_training.grades collection

    {"$and": [{"student_id": {"$gt": 25}}, {"student_id": {"$lt": 100}}]}
better:

    {"student_id": {"$gt": 25, "$lt": 100}}
**Explicit $and**
The best practices is when you need to include **the same operator more than once** in a query

    {"$or" :[{dst_airport : "KZN"},{src_airport : "KZN"}]}

and

    {"$or" :[{airplane : "CR2"}, {airplane : "A81"}]}
both:

    db.routes.find({ "$and": [ { "$or" :[ { "dst_airport": "KZN" }, { "src_airport": "KZN" } ] }, { "$or" :[ { "airplane": "CR2" }, { "airplane": "A81" } ] } ]}).pretty()

From the sample_training.inspections collection where the inspection date is either "Feb 20 2015", or "Feb 21 2015" **and** the company is **not** part of the "Cigarette Retail Dealer - 127" sector

    db.inspections.find(
      { "$or": [ { "date": "Feb 20 2015" },
                 { "date": "Feb 21 2015" } ],
        "sector": { "$ne": "Cigarette Retail Dealer - 127" }}).pretty()

How many zips in the  sample_training.zips  dataset are neither over-populated nor under-populated?

In this case, we consider population of more than  1,000,000  to be over- populated and less than  5,000  to be under-populated.

    db.zips.find({ "pop": {"$gte": 5000, "$lte": 1000000 } }).count()

How many companies in the sample_training.companies dataset were either founded in 2004 and either have the social category_code or web category_code, or were founded in the month of October and also either have the social category_code or web category_code?

    db.companies.find({ "$or" : [ {"founded_year" : 2004 , "$or" : [ {"category_code" : "social"} , {category_code : "web"} ] } , {"founded_month" : 10 , "$or" : [ {"category_code" : "social"} , {category_code : "web"} ] } ] }).count()

**Expresive Query Operator**
**$expr** allows the use of aggregation expretions within the query language

    { $expr: { <Expression> } }
**$** denotes the use of an operator
with $expr we can compare two values in the same document:

    {  "$expr":  {  "$eq":  [  "$end station id",  "$start station id"]  }
    
**$** signifies that you are looking at the value of that field rather than the field name.

The following statements will find all the companies that have more employees than the year in which they were founded:

    db.companies.find(
        { "$expr": { "$gt": [ "$number_of_employees", "$founded_year" ]} }
      ).count()

and

    db.companies.find(
        { "$expr": { "$lt": [ "$founded_year", "$number_of_employees" ] } }
      ).count()

in the sample_training.companies collection have the same *permalink* as their *twitter_username*?

    db.companies.find({ "$expr": { "$eq" : ["$permalink", "$twitter_username"] } }).count()

**Array Operators**
$push
|Operator| How it works|
|--|--|
| $push | Allows us to add an element to an array and turns a field into an array if it was previously a different type |
| $size | Returns a cursor with all documents where the specified array field is exactly the given length |
| $all | Returns a cursor with all documents in which the specified array field contains all the given elements regardless of their order in the array |



<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU1NjA3MjEzMl19
-->