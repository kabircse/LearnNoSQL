# MongoDB

MongoDB is an open source document database and nosql database.
Written in C++;

MongoDB structure comparatively RDBMS:

    RDBMS	        MongoDB
    ------------------------
    Database	Database
    Table	        Collection
    Tuple/Row	Document
    column	        Field
    Table Join	Embedded Documents
    Primary Key	Primary Key (Default key _id provided by MongoDB itself)

**Type of NoSQL databases:**

    Mainly categorized in 4 types as:
    1. Key-value pair based: Redis, Memcached, etc.
    2. Column-based: BigTable, Cassandra,Hbase,etc.
    3. Graph based: Neo4j,etc.
    4.Document based: MongoDB, CouchDB,DocumentDB,etc. 

**DataType:**
    
    6 data types as: string, number, date, array, boolean, ObjectId
    
    Another types as:        
        buffer:video, image, audio
        mixed: combine different type


#### Data Model Design:

1. **Embedded Data Model**: Embedded all the related data in a single document.
2. **Normalized Data Model** :refer the sub documents in the original document, using reference.


1. Embedded Data Model:
Personal_details and address embedded as:

        {
            _id: ,
            Emp_ID: "10025AE336"
            Personal_details:{
                First_Name: "Radhika",
                Last_Name: "Sharma",
                Date_Of_Birth: "1995-09-26"
            },
            Address: {
                city: "Hyderabad",
                Area: "Madapur",
                State: "Telangana"
            }
        }

2. Normalized Data Model:
Personal_details and address normalized as:

        Employee:
        {
            _id: <ObjectId101>,
            Emp_ID: "10025AE336",
            Code: 1245
        }
        
        Personal_details:
        {
            _id: <ObjectId102>,
            empDocID: " ObjectId101",
            First_Name: "Radhika",
            Last_Name: "Sharma",
            Date_Of_Birth: "1995-09-26"
        }
        
        Address:
        {
            _id: <ObjectId104>,
            empDocID: " ObjectId101",
            city: "Hyderabad",
            Area: "Madapur",
            State: "Telangana"
        }


query for normalized/reference data as:

    var result = db.employee.findOne({"code":"1245"},{"_id":1})
    var addresses = db.address.find({"_id":{"$in":result["_id"]}}) 

**DBRefs:**

	$ref − This field specifies the collection of the referenced document
	$id − This field specifies the _id field of the referenced document
	$db − This is an optional field and contains the name of the database in which the referenced document lies.

    Example:
    {
       "_id":ObjectId("53402597d852426020000002"),
       "address": {
       "$ref": "address_home",
       "$id": ObjectId("534009e4d852427820000002"),
       "$db": "tutorialspoint"},
       "name": "Tom Benzamin"
    }	

Query from user collection and ref address collection on ref db address_home

    var user = db.users.findOne({"name":"Tom Benzamin"})
    var dbRef = user.address
    db[dbRef.$ref].findOne({"_id":(dbRef.$id)})       
    
    
To run mongo in cmd go mongdb/bin and run:    

    mongod
    mongo 

**mongod**: (Mongo Daemon) is a background process used to manage all the mongodb server tasks.
(Instance, request accepting, memory management)

**mongo**: mongo is a command line shell used to interact with client.

To connect with mongo using shell: 
   
    mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
    

## Commands
#### show databases
  
    show dbs;
    
#### switch to students database
  
    use students;
    
#### drop an existing database
  
    db.dropDatabase();
    
#### create collection
  
    db.createCollection(name,options);
    
#### show collections
    show collections;
    
#### drop collection
    db.students.drop();
    
#### empty collection
    db.students.remove();
    
#### insert
    db.students.insert(
        {
            'name': 'Sharif'
        }
    )

## Indexes:

Indexes stored in Ram.

Indexing can't used in queries which use −
1. Regular expressions or negation operators like $nin, $not, etc.
2. Arithmetic operators like $mod, etc.
3. $where clause


#### createIndex by ascending as 1
    db.students.createIndex(
        {
            name : 1
        }
    );
    
#### drop index
    db.students.dropIndex(
        {
            name : 1
        }
    );
      
####  Text Indexe/Text Search:
    db.posts.createIndex(
        {
            'content': 'text'
        }
    );

####  search all the posts content as world word as:
    db.posts.find(
        {
            $text: {
                $search: 'world'
            }
        }
    );

####  regrex:
    search all the posts content containing as world
    db.posts.find(
        {
            content: {
                $regrex: 'world'
            }
        }
    )
    
    
## Operations:
    
#### insert:
    db.students.insert({
        name: 'Asadur Rahman',
        age: 28,
        roll: 10,
        class: 7
    })
    
#### update:
    db.students.update({
        name: 'Asadur Rahman',
    },
    {
        $set: {
        name: 'Asif Miah'
        }
    }
    );
    
    db.students.update(
        { 
            name: 'Asif Miah', 
        }, 
        { 
            $set: { 
                classes: ['seven','amiray','std'] 
            } 
        } 
    );
    
#### Find    
    db.students.find(
        {
            name: 'Asif Miah'
        }
    );
    
    db.students.find(
        {
            age: {
                $gt: 20
            }
        }
    );
    
    db.students.find(
        {
            classes:{
                $in: ['amiray']
            }
        }
    ).sort(
        {
            name: 1
        }
    );
    
#### foreach():
       db.posts.find().forEach(function(doc) {
         print("Blog Post: " + doc.title)
       });
       
#### Increment Field ($inc):
       db.posts.update({ title: 'Post Two' },
       {
         $inc: {
           likes: 5
         }
       });
       
#### Rename Field:
       db.posts.update({ title: 'Post Two' },
       {
         $rename: {
           likes: 'views'
         }
       });
       
#### Find By Element in Array ($elemMatch)
       db.posts.find({
         comments: {
            $elemMatch: {
              user: 'Mary Williams'
              }
           }
         }
       );
    
  // 1 -> descending, -1 -> ascending    
 // for json format output: pretty()    


### Projection:

Projection means selecting the necessary field.

projection/show columns as:

    db.students.find(
        {},
        {
            name: 1,
            age: 1
        }
    );
    //show only name and age column
    //if name: 0, hide name column
    
#### Delete one    
    db.students.deleteOne(
        {
            age: 18
        }
    );
#### DeleteMany: 
    deleteMany() -> multiple document delete.    
    
#### Operators:    
**Comparison:**
1. $eq: Matches values that are equal to a specified value.
2. $gt: Matches values that are greater than a specified value.
3. $gte: Matches values that are greater than or equal to a specified value.
4. $in: Matches any of the values specified in an array.
5. $lt: Matches values that are less than a specified value.
6. $lte: Matches values that are less than or equal to a specified value.
7. $ne: Matches all values that are not equal to a specified value.
8. $nin: Matches none of the values specified in an array.

**Logical:**
1. $and: Joins query clauses with a logical AND returns all documents that match the conditions of both clauses.
2. $not: Inverts the effect of a query expression and returns documents that do not match the query expression.
3. $nor: Joins query clauses with a logical NOR returns all documents that fail to match both clauses.
4. $or: Joins query clauses with a logical OR returns all documents that match the conditions of either clause.

**Element:**
1. $exists: Matches documents that have the specified field.
2. $type: Selects documents if a field is of the specified type.

**Evaluation:**
1. $expr: Allows use of aggregation expressions within the query language.
2. $jsonSchema: Validate documents against the given JSON Schema.
3. $mod: Performs a modulo operation on the value of a field and selects documents with a specified result.
4. $regex: Selects documents where values match a specified regular expression.
5. $text: Performs text search.
6. $where: Matches documents that satisfy a JavaScript expression.

**Array:**
1. $all: Matches arrays that contain all elements specified in the query.
2. $elemMatch: Selects documents if element in the array field matches all the specified $elemMatch conditions.
3. $size: Selects documents if the array field is a specified size.


    //getter then operator
    db.students.find(
        {
            age: {
                $gt: 17
            }
        }
    );
    
    // age>=17 or class=7
    db.students.find(
        {
            $or: [
                {
                    age: {
                        $gte: 17
                    }
                },
                {
                    class: 7
                }
            ]
        }
    );
    
    
    array:
    [
        {
            name: 'Asif',
            books:[
                {
                    name: 'English for today',
                    writter: 'Samsul Arefin'
                },
                {
                    name: 'Amar Desh',
                    writter: 'A man'
                }
            ]
        },
        {
            name: 'Sharif',
            books:[
                {
                    name: 'English for today',
                    writter: 'Samsul Arefin'
                },
                {
                    name: 'Amar Desh',
                    writter: 'A man'
                }
            ]
        },
    ]
    
    // query from inner object
    db.students.find(
        {
            'books.name': 'Amar Desh'
        }
    );
    
    //add element using set/push
    db.students.updateMany(
        {
            name: 'Asif'
        },
        {
            $push: {
                books:
                {
                    name: 'Social Science',
                    writter: 'Mujibur Rahman'
                }
            }
        }
    )
    
    //remove element using pull
    Array as in store collection:
    {
       _id: 1,
       fruits: [ "apples", "pears", "oranges", "grapes", "bananas" ],
       vegetables: [ "carrots", "celery", "squash", "carrots" ]
    }
    
    db.stores.update(
        { },
        { $pull: { fruits: { $in: [ "apples", "oranges" ] }, vegetables: "carrots" } },
        { multi: true }
    )
    result:
    {
      "_id" : 1,
      "fruits" : [ "pears", "grapes", "bananas" ],
      "vegetables" : [ "celery", "squash" ]
    }
    
    // get count group by gender as
    db.students.aggregate([
        {
            $group: {
                _id: '$gender',
                , result: {
                    $sum: 1
                }
            }
        }
    ]);

**Upsert**:
To add/update the document on filter condition. 
If filter document is exists it will update the respective values else MongoDB will insert document if not exists.

**Multi:**
updates multiple documents that meet the query criteria. 