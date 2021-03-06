#MongoDB is document oriented & Schema-less
(http://proserge.kh.ua/coding/index.php/post/88/Mysql+To+MongoDB+statements+table)
(http://docs.mongodb.org/manual/)


```
$ mkdir data && echo 'mongod --bind_ip=$IP --dbpath=data --nojournal --rest "$@"' > mongod && chmod a+x mongod && ./mongod

```

We also sometimes use the term 'dynamic schema.' The point is that, unlike in a relational database, you are not constrained to follow any particular schema. If you wish to change your schema, you are free to do any of the following:

*Begin inserting documents with the new schema.
*Perform a bulk update on the existing documents.
*Begin updating old documents to the new schema one by one at an appropriate event (such as getting read from or written to), as coded in the application.
*Contrast this with what happens in a relational database, where the table must typically be taken offline in order to add columns.

*As for the other two answers, MongoDB does not support joins as a design decision because they do not scale horizontally, and it does not support SQL because that query language was built around joins and transactions, and tends to assume table structure rather than the flexible document orientation that MongoDB provides.*


```
//html
<h1>Hello, {{name}}!</h1>

//script
var express = require('express'),
    app = express(),
    cons = require('consolidate'),
    MongoClient = require('mongodb').MongoClient,
    Server = require('mongodb').Server;

app.engine('html', cons.swig);
app.set('view engine', 'html');
app.set('views', __dirname + '/views');

var mongoclient = new MongoClient(new Server("localhost", 27017));
var db = mongoclient.db('course');

app.get('/', function(req, res){

    // Find one document in our collection
    db.collection('hello_mongo_express').findOne({}, function(err, doc) {

        if(err) throw err;

        res.render('hello', doc);
    });
});

app.get('*', function(req, res){
    res.send('Page Not Found', 404);
});

mongoclient.open(function(err, mongoclient) {

    if(err) throw err;

    app.listen(8080);
    console.log('Express server started on port 8080');
});
```

##JSON:JavaScript Object Notation

There are two types of data structures:

*Arrays: List of things, []
*Dictionaries: Associated Maps, {}
(http://json.org/)
```
Example: 
{"fruit": ["apple", "pear", "peach"] }
		   {"address":  {"street_address":"23 Elm Drive",
              "city": "Palo Alto",
              "state": "California",
              "zipcode": "94305"}
              }
```             

```
show dbs
use <dbs>
show collections
db.<collection>.find();
mongorestore <data dump>
```

##CRUD and mongo shell

- Operations: MongoDB: SQLDB
- Create: Insert: Insert
- Read: Find: Select
- Update: Update: Update
- Delete: Remove: Delete

* MongoDB's CRUD operations exits as method/functions in programming language APIs, not as a separate language.
* It as a wired protocol

####BSON: Binary JSON, superset of JSON used by JavaScript

**findOne**
>>Use findOne on the collection users to find one document where the key username is "dwight", and retrieve only the key named email.

```
db.users.findOne({"username": "dwight"},{"email": true, "_id": false})
```

**find**
>>how would you find all documents with type: essay and score: 50 and only retrieve the student field?

```
db.scores.find({type:"essay", "score":50}, {student: true, _id: false})
```

**query operators**

```
db.scores.find({ score : { $gte : 50 , $lte : 60 } } ); // >=50 && <=60
```

- $gt, $gte, $lt, $lte
- $exits: true - db.scores.find({ score : {$exits: true } } ); //or false
- $type : 2 // 2 = string, look at the BSON documentation 
- $regex : '<>' // ^ - start, $ - end

```
db.users.find({name : {$regex: "q"}, email : {$exists: true}});
```
- $or: How would you find all documents in the scores collection where the score is less than 50 or greater than 90?
```
db.scores.find({
$or: [{score :  {$lt : 50 }}, {score: {$gt : 90 }}] 
})
```
- $and
- $all, $in
```
db.users.find( { friends : { $all : [ "Joe" , "Bob" ] }, favorites : { $in : [ "running" , "pickles" ] } } )
//output
{ name : "Cliff" , friends : [ "Pete" , "Joe" , "Tom" , "Bob" ] , favorites : [ "pickles", "cycling" ] }
```

- dot notation
Suppose a simple e-commerce product catalog called catalog with documents that look like this:
```
{ product : "Super Duper-o-phonic", 
  price : 100000000000,
  reviews : [ { user : "fred", comment : "Great!" , rating : 5 },
              { user : "tom" , comment : "I agree with Fred, somewhat!" , rating : 4 } ],
  ... }
```
Write a query that finds all products that cost more than 10,000 and that have a rating of 5 or better.
```
db.catalog.find({
'price': {'$gt' : 10000},
'reviews.rating': {'$gte': 5} //dot for embedded document
})
```

**Cursors:**

- cursor.next - next element
- cursor.hasNext - returns boolean if has next

```
   cursor = grades.find({}); null; //null to avoid the print
    cursor.skip(1);  
    cursor.limit(4); 
    cursor.sort('grade', 1);
```
Cursor can be changed at any point before the first document is called and before you've checked to see if it is empty.

- count :How would you count the documents in the scores collection where the type was "essay" and the score was greater than 90?
```
db.scores.count({type: 'essay' ,score:{$gt: 90}})
```

- update
```
db.foo.update({_id:"Texas"},{population:30000000})
```
- $set, $inc: increment

```
db.users.update({"_id" : "myrnarackham"}, {$set: {"country" : "RU"}})
```
- $unset
```
db.users.update({"_id":"jimmy"},{$unset:{"interests":1}})
```

- $push, $pop, $pull, $pushAll, $pullAll, $addToSet
```
db.friends.update( { _id : "Mike" }, { $push : { interests : "skydiving" } } );
db.friends.update( { _id : "Mike" }, { $pop : { interests : -1 } } );
db.friends.update( { _id : "Mike" }, { $addToSet : { interests : "skydiving" } } );
db.friends.update( { _id : "Mike" }, { $pushAll: { interests : [ "skydiving" , "skiing" ] } } );
```
-  upsert: true
```
db.foo.update( { username : 'bar' }, { '$set' : { 'interests': [ 'cat' , 'dog' ] } } , { upsert : true } );
```

- multipe update: {multi: true}
```
db.scores.update({'score':{$lt:70}}, {$inc:{'score':20}},{multi:true})
```
- remove
```
db.scores.remove({'score':{$lt:60}})
```

###NodeJS Driver and CRUD

- find

```
var MongoClient = require('mongodb').MongoClient;

MongoClient.connect('mongodb://localhost:27017/course', function(err, db) {
    if(err) throw err;

    var query = { 'grade' : 100 };

    db.collection('grades').find(query).toArray(function(err, docs) {
        if(err) throw err;

        console.dir(docs);

        db.close();
    });
});
```

- findOne

```
var MongoClient = require('mongodb').MongoClient;

MongoClient.connect('mongodb://localhost:27017/course', function(err, db) {
    if(err) throw err;

    var query = { 'grade' : 100 };

    db.collection('grades').findOne(query, function(err, doc) {
        if(err) throw err;

        console.dir(doc);

        db.close();
    });
});
```

- cursor: its a object to describe the data
```
var MongoClient = require('mongodb').MongoClient;

MongoClient.connect('mongodb://localhost:27017/course', function(err, db) {
    if(err) throw err;

    var query = { 'grade' : 100 };

    var cursor = db.collection('grades').find(query);

    cursor.each(function(err, doc) {
        if(err) throw err;

        if(doc == null) {
            return db.close();
        }

        console.dir(doc.student + " got a good grade!");
    });
});

```
- projection
```
var MongoClient = require('mongodb').MongoClient;

MongoClient.connect('mongodb://localhost:27017/course', function(err, db) {
    if(err) throw err;

    var query = { 'grade' : 100 };

    var projection = { 'student' : 1, '_id' : 0 };

    db.collection('grades').find(query, projection).toArray(function(err, docs) {
        if(err) throw err;

        docs.forEach(function (doc) {
            console.dir(doc);
            console.dir(doc.student + " got a good grade!");
        });

        db.close();
    });
});
```
- gt lt

```
var MongoClient = require('mongodb').MongoClient;
MongoClient.connect('mongodb://localhost:27017/course', function(err, db) {
    if(err) throw err;

    var query = { 'grade' : { '$gt' : 69, '$lt' : 80 } };
    db.collection('grades').find(query).each(function(err, doc){
        if(err) throw err;
        if(doc == null) {
            return db.close();
        }
        console.dir(doc);
    });
}); 
```

**Importing the Reddit JSON**
```
var MongoClient = require('mongodb').MongoClient
  , request = require('request');

MongoClient.connect('mongodb://localhost:27017/course', function(err, db) {
    if(err) throw err;

    request('http://www.reddit.com/r/technology/.json', function (error, response, body) {
        if (!error && response.statusCode == 200) {
            var obj = JSON.parse(body);

            var stories = obj.data.children.map(function (story) { return story.data; });

            db.collection('reddit').insert(stories, function (err, data) {
                    if(err) throw err;

                    console.dir(data);

                    db.close();
            });
        }
    });
});
```
- regex
```
var MongoClient = require('mongodb').MongoClient;

MongoClient.connect('mongodb://localhost:27017/course', function(err, db) {
    if(err) throw err;

    var query = { 'title' : { '$regex' : 'Microsoft' } };

    var projection = { 'title' : 1, '_id' : 0 };

    db.collection('reddit').find(query, projection).each(function(err, doc) {
        if(err) throw err;

        if(doc == null) {
            return db.close();
        }

        console.dir(doc.title);
    });
});
```
- sort, skip, limit
```
var MongoClient = require('mongodb').MongoClient;

MongoClient.connect('mongodb://localhost:27017/course', function(err, db) {
    if(err) throw err;

    var grades = db.collection('grades');

    var cursor = grades.find({});
    cursor.skip(1);
    cursor.limit(4);
    cursor.sort('grade', 1);
    //cursor.sort([['grade', 1], ['student', -1]]);

    //var options = { 'skip' : 1,
    //                'limit' : 4,
    //                'sort' : [['grade', 1], ['student', -1]] };
    //var cursor = grades.find({}, {}, options);

    cursor.each(function(err, doc) {
        if(err) throw err;
        if(doc == null) {
            return db.close();
        }
        console.dir(doc);
    });
});
```
- insert
```
var MongoClient = require('mongodb').MongoClient;

MongoClient.connect('mongodb://localhost:27017/course', function(err, db) {
    if(err) throw err;

    var doc = { '_id' : 'calvin', 'age' : 6 };

    db.collection('students').insert(doc, function(err, inserted) {
        if(err) throw err;

        console.dir("Successfully inserted: " + JSON.stringify(inserted));

        return db.close();
    });
});
```
- findAndModify: atomically finds modify's and returns the document
```
var MongoClient = require('mongodb').MongoClient;

MongoClient.connect('mongodb://localhost:27017/course', function(err, db) {
    if(err) throw err;

    var query = { 'name' : 'comments' };
    var sort = [];
    var operator = { '$inc' : { 'counter' : 1 } };
    var options = { 'new' : true };

    db.collection('counters').findAndModify(query, sort, operator, options, function(err, doc) {
        if(err) throw err;

        if (!doc) {
            console.log("No counter found for comments.");
        }
        else {
            console.log("Number of comments: " + doc.counter);
        }

        return db.close();
    });
});
```

- Mongo csv import

```
$ mongoimport --type csv --headerline weather_data.csv -d weather -c data
$ mongo
> use weather
> db.data.find().count()
> 2963
> db.data.find({"Wind Direction":{"$gte":180, "$lte": 360}}).sort({"Temperature"
: -1}).limit(1).pretty()
```
db.data.find({}).sort({"State": 1},{"Temperature": 1})

##Schema Design

- Rich Documents
- Pre Join/ Embedded Data
- MongoDB doesn't have 'join'
- no constraints
- atomic operation, no transactions 
- no declared schema
- always try to embed/pre-join the documents not like SQL DBs

**Normalization involves decomposing a table into less redundant (and smaller) tables but without losing information; defining foreign keys in the old table referencing the primary keys of the new ones. The objective is to isolate data so that additions, deletions, and modifications of an attribute can be made in just one table and then propagated through the rest of the database using the defined foreign keys.**

- LIVING WITHOUT CONSTRAINTS: Keeping your data consistent even though MongoDB lacks foreign key constraints
- Lack of transactions can be overcomed by:
* restructuring the code to work in one document and take advantage of atomic operations
* implement in SW
* tolerate (adjust)

- Benefits of embedding 
* Improved read performance
* one round trip to the DB

**Assignment3:**
```
$ mongoimport -d school -c students < students.json
$ mongo
> use school
> db.students.count() 
200
> db.students.find( { _id : 137 } ).pretty( )
{
	"_id" : 137,
	"name" : "Tamika Schildgen",
	"scores" : [
		{
			"type" : "exam",
			"score" : 4.433956226109692
		},
		{
			"type" : "quiz",
			"score" : 65.50313785402548
		},
		{
			"type" : "homework",
			"score" : 89.5950384993947
		}
	]
}
```

## Indexing

### Storage Engine: Its a interface between persistent database like the disk and the mongoDB server. Its plugable storage engine in mongodb
- MMAP:  its by default
- Wired Tiger
 It determines:
 - format of indexes
 - the data file format
 
- MMAPv1 automatically allocates power-of-two-sized documents when new documents are inserted
 * This is handled by the storage engine.
- MMAPv1 is built on top of the mmap system call that maps files into memory
 * This is the basic idea behind why we call it MMAPv1.
- MMAPv1 offers collection level locking.
- The operating system manages the memory used by each mapped file, deciding which parts to swap to disk.

- Wired Tiger:
 * Document level concurrency
 * Compression: of data and of index
(http://www.slideshare.net/wiredtiger/)
```
mongod --storageEngine wiredTiger
db.name.stats()
```

** Index is a ordered list of things. Indexing slows down your writes and increases your reads **
```
db.student.createIndex({student_id:1});
db.students.createIndex( { "class" : 1, "student_name" : 1 } )
db.students.getIndexes();
```

** Creating an index takes time **

#### Multi Index:
```
db.foo.createIndex( { a:1, b:1 } )
db.foo.insert( { a : "grapes", b : [ 8, 9, 10 ] } )
db.foo.insert( { a : "grapes", b : "oranges" } )
db.foo.insert( { a : ["apples", "oranges" ], b : "grapes" } )
```
* A can be an array and B a scalar or vice versa but both can be an array for a Multi Index *
```
{
	"_id" : ObjectId("551458821b87e1799edbebc4"),
	"name" : "Eliot Horowitz",
	"work_history" : [
		{
			"company" : "DoubleClick",
			"position" : "Software Engineer"
		},
		{
			"company" : "ShopWiki",
			"position" : "Founder & CTO"
		},
		{
			"company" : "MongoDB",
			"position" : "Founder & CTO"
		}
	]
}

> db.people.createIndex({'work_history.company':-1})
```
- Unique
```
db.students.createIndex({'student_id':1,'class_id':1},{'unique': true})
```
- Sparse: For duplicate/null unique
```
db.students.createIndex({'student_id':1,'class_id':1},{'unique': true, 'sparse':true})
```
 * You can gain greater flexibility with creating Unique indexes.
 * The index will be smaller than it would if it were not sparse.
 * Sorting not possible  by using indexes
 
 - Index creation
    * Foreground: Faster, blocks writes and reads in the database
	* Background: slower, don't block
	```
	db.students.createIndex({'student_id':1},{'background': true})
	```
- Explain
* query planner mode: dy default	
* execution stats mode: db.example.explain("executionStats")
* all plans execution: "allPlansExecution"

- Covered Queries: 
```
db.example.find( { name : { $in : [ "Bart", "Homer" ] } }, {_id : 0, dob : 1, name : 1} )
```
Shows name and dob but supresses _id

-  The index size can be considerably smaller (at the cost of some CPU space) in WiredTiger with --wiredTigerIndexPrefixCompression enabled.
- Index is more important to fit into memory
- The cost of moving documents, in terms of updating index entries only exists in the MMAPv1 storage engine. In the WiredTiger storage engine, index entries don't contain pointers to actual disk locations. Instead, in WiredTiger, the indexes contain _id values. As _id is immutable, the indexes don't change for document moves, although document moves do require updating the index that translates between _id values an disk locations.

- Geospatial Index
```
location:[x,y]
db.stores.ensureIndex({location:'2d', type:1})
db.stores.find({location:{$near:[50,50]}})
```
- Geospatial Spherical
 * GeoJson
```
db.stores.ensureIndex({'location':'2dsphere'})
db.stores.find({ loc:{ $near:{ $geometry: { type: "Point", coordinates: [-130, 39]}, $maxDistance:1000000 } } })
```
- Text Index
```
db.sentences.ensureIndex({'words':'text'})
db.sentences.find('{$text:{$search:'dog cat'}}) //this is a logical or
db.sentences.find('{$text:{$search:'dog'}}.{score:{$meta:'textScore'}}).sort({score:{$meta:'textScore'}}) //for sorting according to the score
```
Efficiency of Index Use
```
db.students.find({student_id:{$gt:500000}, class_id:54}).sort({student_id:1}).hint({class_id:1}).explain("executionstats")
```
Selectivity is the primary factor that determines how efficiently an index can be used. Ideally, the index enables us to select only those records required to complete the result set, without the need to scan a substantially larger number of index keys (or documents) in order to complete the query. Selectivity determines how many records any subsequent operations must work with. Fewer records means less execution time.
- Logging and Profiling
 * Default logging: In the mongod shell
(http://docs.mongodb.org/manual/reference/database-profiler/?_ga=1.206914768.2128372322.1439362268)
```
#collection for all queries that took longer than one second, ordered by timestamp descending
db.system.profile.find({millis:{$gt:1000}}).sort({ts:-1})
```
- Mongotop: Similar to unix top
- mongostat: Similar to unix iostat 
- Sharding: Splitting the data in different servers
```
Servers(mongoD, replica set) ---> Mongos ---> Application
```

# Aggregation Framework
```
use agg
db.products.aggregate([
    {$group:
     {
	 _id:"$manufacturer", 
	 num_products:{$sum:1}
     }
    }
])
```
### stages in the aggregation pipeline:
- Match
- Group
- Skip
- Limit
- Sort
- Project
- Unwind
### Compound Grouping
```
use agg
db.products.aggregate([
    {$group:
     {
	 _id: {
	     "manufacturer":"$manufacturer", 
	     "category" : "$category"},
	 num_products:{$sum:1}
     }
    }
])
```
- $group
 * $sum: count, sum
 * $avg
 * $min
 * $max
 * $push: Arrays
 * $addtoSet: Adds to the array uniquely 
 * $first $last: doesn't make sense unless you sort them
```
use agg
db.products.aggregate([
    {$group:
     {
	 _id: {
	     "maker":"$manufacturer"
	 },
	 sum_prices:{$sum:"$price"}
     }
    }
])
```
```
{
	"city" : "CLANTON",
	"loc" : [
		-86.642472,
		32.835532
	],
	"pop" : 13990,
	"state" : "AL",
	"_id" : "35045"
}
```
Write an aggregation query to sum up the population (pop) by state and put the result in a field called population.
```
db.zips.aggregate([{"$group":{"_id":"$state", "population":{$sum:"$pop"}}}])
```
- $avg
```
db.products.aggregate([
    {$group:
     {
	 _id: {
	     "category":"$category"
	 },
	 avg_price:{$avg:"$price"}
     }
    }
])
```
- $addtoSet : No direct parallel to SQL
```
db.zips.aggregate([{"$group":{"_id":"$city", "postal_codes":{"$addToSet":"$_id"}}}])
```
- $push : can have duplication
```
db.zips.aggregate([{"$group":{"_id":"$city", "postal_codes":{"$addToSet":"$_id"}}}])
db.zips.aggregate([{"$group":{"_id":"$city", "postal_codes":{"$push":"$_id"}}}])
```
- $max $min
```
db.zips.aggregate([{"$group":{"_id":"$state", "pop":{"$max":"$pop"}}}])
```
- double group
```
db.grades.aggregate([
    {'$group':{_id:{class_id:"$class_id", student_id:"$student_id"}, 'average':{"$avg":"$score"}}},
    {'$group':{_id:"$_id.class_id", 'average':{"$avg":"$average"}}}])
```
- $project
	* $toUpper
	* $toLower
	* $add
	* $multiply
```
db.zips.aggregate([{$project:{_id:0, city:{$toLower:"$city"}, pop:1, state:1, zip:"$_id"}}])
```
- $match
```
db.zips.aggregate([
    {$match:
     {
	 state:"NY"
     }
    },
    {$group:
     {
	 _id: "$city",
	 population: {$sum:"$pop"},
	 zip_codes: {$addToSet: "$_id"}
     }
    },
    {$project:
     {
	 _id: 0,
	 city: "$_id",
	 population: 1,
	 zip_codes:1
     }
    }
     
])
```
**Again, thinking about the zipcode collection, write an aggregation query with a single match phase that filters for zipcodes with greater than 100,000 people.**
```
db.zips.aggregate([{"$match":{"pop":{$gt:100000}}}])
```
- $sort: memory and disk based
```
db.zips.aggregate([
    {$match:
     {
	 state:"NY"
     }
    },
    {$group:
     {
	 _id: "$city",
	 population: {$sum:"$pop"},
     }
    },
    {$project:
     {
	 _id: 0,
	 city: "$_id",
	 population: 1,
     }
    },
    {$sort:
     {
	 population:-1
     }
    }
      
    
     
])
```
**Write an aggregation query with just a sort stage to sort by (state, city), both ascending. **
```
db.zips.aggregate([{"$sort":{"state":1,"city":1}}])
```
- $skip $limit
```
db.zips.aggregate([
    {$match:
     {
	 state:"NY"
     }
    },
    {$group:
     {
	 _id: "$city",
	 population: {$sum:"$pop"},
     }
    },
    {$project:
     {
	 _id: 0,
	 city: "$_id",
	 population: 1,
     }
    },
    {$sort:
     {
	 population:-1
     }
    },
    {$skip: 10},
    {$limit: 5}
])
```
- $first $last
```
db.zips.aggregate([
    /* get the population of every city in every state */
    {$group:
     {
	 _id: {state:"$state", city:"$city"},
	 population: {$sum:"$pop"},
     }
    },
     /* sort by state, population */
    {$sort: 
     {"_id.state":1, "population":-1}
    },

    /* group by state, get the first item in each group */
    {$group: 
     {
	 _id:"$_id.state",
	 city: {$first: "$_id.city"},
	 population: {$first:"$population"}
     }
    },

    /* now sort by state again */
    {$sort:
     {"_id":1}
    }
])
```
- $unwind
```
db.posts.aggregate([
    /* unwind by tags */
    {"$unwind":"$tags"},
    /* now group by tags, counting each tag */
    {"$group": 
     {"_id":"$tags",
      "count":{$sum:1}
     }
    },
    /* sort by popularity */
    {"$sort":{"count":-1}},
    /* show me the top 10 */
    {"$limit": 10},
    /* change the name of _id to be tag */
    {"$project":
     {_id:0,
      'tag':'$_id',
      'count' : 1
     }
    }
    ])
```
- double $unwind and wind by push
```
db.inventory.aggregate([
    {$unwind: "$sizes"},
    {$unwind: "$colors"},
    /* create the color array */
    {$group: 
     {
	'_id': {name:"$name",size:"$sizes"},
	 'colors': {$push: "$colors"},
     }
    },
    /* create the size array */
    {$group: 
     {
	'_id': {'name':"$_id.name",
		'colors' : "$colors"},
	 'sizes': {$push: "$_id.size"}
     }
    },
    /* reshape for beauty */
    {$project: 
     {
	 _id:0,
	 "name":"$_id.name",
	 "sizes":1,
	 "colors": "$_id.colors"
     }
    }
])
```
Alternative using addtoSet (has to be unique)
```
db.inventory.aggregate([
    {$unwind: "$sizes"},
    {$unwind: "$colors"},
    {$group: 
     {
	'_id': "$name",
	 'sizes': {$addToSet: "$sizes"},
	 'colors': {$addToSet: "$colors"},
     }
    }
])
```
- SQL to Aggregation Mapping Chart:(http://docs.mongodb.org/manual/reference/sql-aggregation-comparison/)
- Limitation of aggregation framework:
	* 100MB limit - allow disk to avoid
	* single document return of 16MB limit - use cursor to avoid
	* sharded: doesnt have good scalability like hadoop for map reduce

##APPLICATION ENGINEERING

*An in-memory database (IMDB; also main memory database system or MMDB or memory resident database) is a database management system that primarily relies on main memory for computer data storage. It is contrasted with database management systems that employ a disk storage mechanism. Main memory databases are faster than disk-optimized databases since the internal optimization algorithms are simpler and execute fewer CPU instructions. Accessing data in memory eliminates seek time when querying the data, which provides faster and more predictable performance than disk.

Applications where response time is critical, such as those running telecommunications network equipment and mobile advertising networks, often use main-memory databases.[3] IMDBs have gained a lot of traction, especially in the data analytics space, starting in the mid-2000s - mainly due to less expensive RAM.

With the introduction of non-volatile random access memory technology,[when?] in-memory databases will be able to run at full speed and maintain data in the event of power failure.*

*A journaling file system is a file system that keeps track of changes not yet committed to the file system's main part by recording the intentions of such changes in a data structure known as a "journal", which is usually a circular log. In the event of a system crash or power failure, such file systems can be brought back online quicker with lower likelihood of becoming corrupted.
Depending on the actual implementation, a journaling file system may only keep track of stored metadata, resulting in improved performance at the expense of increased possibility for data corruption. Alternatively, a journaling file system may track both stored data and related metadata, while some implementations allow selectable behavior in this regard.*
(http://docs.intersystems.com/ens20121/csp/docbook/DocBook.UI.Page.cls?KEY=GCDI_journal)
(http://docs.mongodb.org/manual/core/journaling/)
```
Provided you assume that the disk is persistent, what are the w and j settings required to guarantee that an insert or update has been written all the way to disk.
>w=1, j=1
```
- Network error:
 * The network fails between the time of the write and the time the client receives a response to the write.
 * The network TCP connection between the application and the server was reset after the server received a write but before a response could be sent.
 * The MongoDB server terminates between receiving the write and responding to it.

- Replication:
 * Three nodes: 1 Primary, 2 Secondary
 * Election when the primary goes down for a new primary. So min number of node = 3 as 2 secondary would be majority
- Election:
 * Regular node
 * Arbiter node -  There for voting purposes
 * Delayed/regular - DR node, can be behind other nodes, can vote (v), can't be primary, priority(p)  = 0 
 * hidden - P=0, v=1
 
 - Creating Replica set:
```
#.sh file
#!/usr/bin/env bash

mkdir -p /data/rs1 /data/rs2 /data/rs3
mongod --replSet m101 --logpath "1.log" --dbpath /data/rs1 --port 27017 --oplogSize 64 --fork --smallfiles
mongod --replSet m101 --logpath "2.log" --dbpath /data/rs2 --port 27018 --oplogSize 64 --smallfiles --fork
mongod --replSet m101 --logpath "3.log" --dbpath /data/rs3 --port 27019 --oplogSize 64 --smallfiles --fork
```
* configuration replica.js *
```
config = { _id: "m101", members:[
          { _id : 0, host : "localhost:27017"},
          { _id : 1, host : "localhost:27018"},
          { _id : 2, host : "localhost:27019"} ]
};

rs.initiate(config);
rs.status();
```
- rs.slaveOk() : this would allow to read from the secondary
- oplog
```
>use local
>show collection
oplog.rs
>db.oplog.rs.find()
```
 * Replication supports mixed-mode storage engines. For examples, a mmapv1 primary and wiredTiger secondary.
 * A copy of the oplog is kept on both the primary and secondary servers.
 * The oplog is implemented as a capped collection.
** Capped collections are fixed-size collections that support high-throughput operations that insert and retrieve documents based on insertion order. Capped collections work in a way similar to circular buffers: once a collection fills its allocated space, it makes room for new documents by overwriting the oldest documents in the collection. **

- Connecting to a Replica Set from the Node.js Driver
 * create the replica sets
 * nodejs:
```
 var MongoClient = require('mongodb').MongoClient;

MongoClient.connect("mongodb://localhost:30001,localhost:30002,localhost:30003/course", function(err, db) {
    if (err) throw err;

    db.collection("repl").insert({ 'x' : 1 }, function(err, doc) {
        if (err) throw err;

        db.collection("repl").findOne({ 'x' : 1 }, function(err, doc) {
            if (err) throw err;

            console.log(doc);
            db.close();
        });
    });
});
```
 * The missing node will be discovered as long as you list at least one valid node.
 
- NodeJs: During the failover nodejs stores the writes and reads in the its buffer and when the election is done the will send all the operations. The insert will be buffered until the election completes, then the callback will be called after the operation is sent and a response is received
```
db.admin
db.shutdownServer()
```
- Read preference (http://docs.mongodb.org/master/core/read-preference/)
- Sharding (http://docs.mongodb.org/manual/core/sharding-introduction/)
** Sharding is a method for storing data across multiple machines. MongoDB uses sharding to support deployments with very large data sets and high throughput operations. **
 * shard key, db level, For a vertical scalability we shard-> break into multiple hosts according to shard key (part of the document like _id, doc_id)
```
 ----       --------
|app| ----> |mongoS| ---->S1 S2 S3 S4....} sharding
 ----       --------      |   |  |  |
                        R11 R12....... } Replica set per shard
```
 The shard key is not included in the find operation, mongos has to send the query to all of the shards. Each shard has replica-set members, but only one member of each replica set (the primary, by default) is required to handle the find.
- Sharded Environment
 * Sharded system
 * Replica sets
 * config servers - min 3 recommended
- Implication of sharding
 * needs a shard key
 * shard isn't mutable
 * index that starts with the shard key, it cant be a multi key index
 * shard key has to be specified as multi
 * the key which I would used as most, that should be a shard key
 * cant be unique
 
- shard key 
 * sufficient cardinality 
 * avoid hot spotting, shouldn't monotonically increase
 * should be naturally parallel
 * THEY ARE NON MUTABLE SO BE CAREFUL BEFORE COMMITING 
 * cant me multi key
 
##MONGOOSE

- Object Relational Mapping (ORM) or, Object Data Mapping (ODM)
- It translates data in the database to JavaScript objects for use in your application
- Structure:
```
- app.js
 - Express
 - Routes
	- error.js		  
	- index.js
	- login.js
			- helpers
			- plugins
		- Views
			- home.html 		  
				- layout.html      
				- login.html      
				- signup.html   

- models: mongoose schema
- middle-ware: Function that are given to express to execute before the route has been executed, this helps to validate incoming data, perform queries, increment  counters all business and server logic
- helper
- plugins: http://plugins.mongoosejs.com/
```




 