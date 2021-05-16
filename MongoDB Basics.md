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

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIzMTIzMzAwNF19
-->