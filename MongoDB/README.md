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

    //show databases
    show dbs;
    
    //switch to students database
    use students;
    
    //drop an existing database
    db.dropDatabase();
    
    //create collection
    db.createCollection(name,options);
    
    //show collections
    show collections;
    
    //drop collection
    db.students.drop();
    
    //empty collection
    db.students.remove();
    
    //insert
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


    //createIndex by ascending as 1
    db.students.createIndex(
        {
            name : 1
        }
    );
    
    //drop index
    db.students.dropIndex(
        {
            name : 1
        }
    );  

    // Text Indexe/Text Search:
    db.posts.createIndex(
        {
            'content': 'text'
        }
    );

    // search all the posts content as world word as:
    db.posts.find(
        {
            $text: {
                $search: 'world'
            }
        }
    );

    // regrex:
    search all the posts content containing as world
    db.posts.find(
        {
            content: {
                $regrex: 'world'
            }
        }
    )
    
    
## Operations:
    
    //insert:
    db.students.insert({
        name: 'Asadur Rahman',
        age: 28,
        roll: 10,
        class: 7
    })
    
    //update:
    db.students.update({
        name: 'Asadur Rahman',
    },
    {
        $set: {
        name: 'Asif Miah'
        }
    }
    )
    db.students.update(
        { 
            name: 'Asif Miah', 
        }, 
        { 
            $set: { 
                classes: ['seven','amiray','std'] 
            } 
        } 
    )
    
    db.students.find(
        {
            name: 'Asif Miah'
        }
    )
    db.students.find(
        {
            age: {
                $gt: 20
            }
        }
    )
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
    )
    
    //1 -> descending, -1 -> ascending    
    //for json format output: pretty()    


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
    
    db.students.deleteOne(
        {
            age: 18
        }
    );
    
    deleteMany() -> multiple document delte.    
    
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
