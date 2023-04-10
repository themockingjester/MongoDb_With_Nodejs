# MongoDb_With_Nodejs
repo contains examples for connecting and using mongodb with node

## basic commands
<br>
<br>

### Shutting down mongodb server
![Shutting mongodb server](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/shutting%20down%20mongo%20db%20server.png)

<br>
<br>

### Running mongo db server in background
![Running mongodb server in background](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/running%20mongodb%20in%20background.png)

<br>
<br>

### Setting dbpath and logpath in mongodb server
![setting dbpath and logpath](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/Setting%20dbpath%20and%20logpath%20for%20mongodb%20server%20while%20running.png)
<br>
<br>



### running mongodb with config file
![running mongodb with config file](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/running%20mondb%20server%20with%20onfig%20file.png)
<br>
<br>


## Writes in mongodb

<br>
<br>
<br>

### writing data even if data writing for record fails in between
![data writing even after failing of a record in between](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/allowing%20mongodb%20to%20continue%20adding%20data%20if%20error%20occurs.png)

<br>
<br>

### write concern in mongodb
![write concern](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/writeconcern.png)
write concern comes into picture whenever we want to either write or update data in mongodb.
from above diagram we can see that client want to write in mongodb using insertOne command and mongodb takes helps from storage engine for doing writing task. An important thing to note here is journal (it is a file in which those write operations are written which havn't been performed yet in mongodb so in case if mongodb server fails in between , mongodb can recover data from this file).

Let's have a look at three other options denoted by orange color.
1) where w:1 and j:undefined: it specifies mongodb should return acknowledge message only when at least one instnce of mongodb has written the data.
2) where w:1 and j:true : it means mongodb should return acknoledge message for our operation (either write or update) only when at least one instnce of mongodb has written the data also our operation has to be added in journal file (this will be good when mongodb went down bcz of some issue also didn't added data into journal but return us with acknoledge message hence the command can give us assurity that our operation has been receorded successfully)
3) third one says mongodb have to perfrom action (denoted by second point) but have to give response in given time specified.
<br>
<br>



### write concern in practice
![write concern in practice](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/write%20concern%20in%20practice.png)

<br>
<br>


## Read in mongodb

### logical and operator
![logical and](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/and%20ooperator.png)
<br>
<br>


### logical or operator
![logical or](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/logical%20or.png)

<br>
<br>

### nor operator
![nor](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/nor%20operator%20(does%20not%20satisfies%20given%20conditions).png)
it filter out those records only which don't satisfies the given conditions.
<br>
<br>



### logical not operator
![logical not](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/not%20operator.png)
this operator funcgtionality can be achieved by not equals operator also but if we are not able to use not equals operator for any cases then we use not operator.
<br>
<br>
<br>


### checking a field exists in a document or not 
![checking a field exists](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/checking%20wheather%20a%20field%20exists%20on%20a%20document%20using%20exists%20command.png)

<br>
<br>

### Fetching those records in which given fields have given data types 
![records in which given fields have given data types ](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/getting%20documents%20where%20given%20fields%20have%20given%20data%20types.png)
in this diagram we are checking where phone must be of either double or string if you want to check only for one datatype then you can omit the array and specify one element
<br>
<br>
### applying filter on nested documents
![filter on nested documents](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/applying%20filter%20on%20nested%20documents.png)

<br>
<br>
### applying multiple filters
![applying multiple filters](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/applying%20multiple%20filters.png)
in above screenshot we have two queries which seems same at first glance but they are totally different
first one returns all those records where either hobbies.title = Sports and hobbies.frequency equals to 3 (it don't mean both condition should apply to one nested document that is for one record if we have a array of nested documents and in one of those nested document title is Sports and in other nested one frequency is 3 then the above query still that document to us. while second query mongo db checks both given condition should apply to the at least one of the nested documents.

<br>
<br>
### getting records where one field is greater than other
![getting records where one field is greater than other](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/getting%20records%20where%20one%20field%20is%20greater%20than%20other.png)
above query will fetch all documents where volume field is greater than target field.
<br>
<br>
### finding documents where field (as array) has size equals to given number
![finding documents where field (as array) has size equals to given number](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/finding%20records%20having%20field%20(as%20array)%20of%20given%20size%20.png)

<br>
<br>
### searching for a array
![array exact match (even order should be same)](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/searching%20for%20a%20%20array%20part%201.png)
in above query mongo db returns those records where genre must be equals to given array `["action","thriller"] (even order must be eaxct same)`
![array match (order can be different)](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/searching%20for%20a%20%20array%20part%202.png)
in above query mongo db will return those documents where genre must have "action" and "thriller" (order can be differnt i.e action can come first or thriller can come first.)

<br>
<br>
### Using regex in searching
![using regex in search)](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/using%20regex%20for%20text%20snippet%20searching.png)
above query will return all those document where musical word will present inside summary field (note these regex queries are not efficient)

## Crud Operations With Transactional queries (or acid property)
```

const { MongoClient, ServerApiVersion } = require('mongodb');
const config = require("./config/default.json")
const uri = config.Application.database.development.mongodb.uri;
const client = new MongoClient(uri, { useNewUrlParser: true, useUnifiedTopology: true, serverApi: ServerApiVersion.v1 });
client.connect(async (err) => {
    //gettingMultipleDocsFromDbUsingTransactions(client)
});

const gettingMultipleDocsFromDbUsingTransactions = (client) => {
    // note : while getting data transactions are not needed

    const collection = client.db("test").collection("devices");
    var session = client.startSession()
    const transactionOptions = {
        "readConcern": { "level": "snapshot" },
        "writeConcern": { "w": "majority" }
    }

    session.withTransaction(async () => {

        const data = await collection.find({
            name: 'Mickeynew',

        }, { session }).toArray()
        console.log(data) // [
        //     {
        //       _id: new ObjectId("6313bd9cb0b77a5913297c62"),
        //       name: 'Mickeynew',
        //       class: 32,
        //       address: 'Cany'
        //     },
        //     {
        //       _id: new ObjectId("6313c617803a54152b784499"),
        //       name: 'Mickeynew',
        //       class: 11,
        //       address: 'Cany'
        //     }
        //   ]

        await session.commitTransaction()
        await session.endSession()
    }, transactionOptions)
    // perform actions on the collection object
}
const gettingOneDocFromDbUsingTransactions = (client) => {
    // note : while getting data transactions are not needed
    const collection = client.db("test").collection("devices");
    var session = client.startSession()
    const transactionOptions = {
        "readConcern": { "level": "snapshot" },
        "writeConcern": { "w": "majority" }
    }

    session.withTransaction(async () => {

        await collection.findOne({
            name: 'Mickeynew',

        }, { session }).then(async (data) => {

            console.log(data) //{
            //     _id: new ObjectId("6313bd9cb0b77a5913297c62"),
            //     name: 'Mickeynew',
            //     class: 32,
            //     address: 'Cany'
            //   }
            await session.commitTransaction()
            await session.endSession()
            client.close();
        }).catch((err) => {
            console.log(err)
        }).finally(async () => {

        })
    }, transactionOptions)
    // perform actions on the collection object

}

const updateMultipleDocsInDbUsingTransactions = (client) => {
    const collection = client.db("test").collection("devices");
    var session = client.startSession()
    const transactionOptions = {
        "readConcern": { "level": "snapshot" },
        "writeConcern": { "w": "majority" }
    }

    session.withTransaction(async () => {

        await collection.updateMany({
            name: 'Mickey',

        }, { $set: { name: "Mickeynew", address: "Cany" } }, { session }).then(async (data) => {

            console.log(data) //{
            //     acknowledged: true,
            //     modifiedCount: 2,
            //     upsertedId: null,
            //     upsertedCount: 0,
            //     matchedCount: 2
            //   }  
            await session.commitTransaction()
            await session.endSession()
            client.close();
        }).catch((err) => {
            console.log(err)
        }).finally(async () => {

        })
    }, transactionOptions)
    // perform actions on the collection object

}
const updateSingleDocFromDbUsingTransactions = (client) => {
    const collection = client.db("test").collection("devices");
    var session = client.startSession()
    const transactionOptions = {
        "readConcern": { "level": "snapshot" },
        "writeConcern": { "w": "majority" }
    }

    session.withTransaction(async () => {

        await collection.updateOne({
            name: 'yash',
            class: 11
        }, { $set: { name: "Mickey", address: "Canyon 123" } }, { session }).then(async (data) => {

            console.log(data)   //{
            //     acknowledged: true,
            //     modifiedCount: 1,
            //     upsertedId: null,
            //     upsertedCount: 0,
            //     matchedCount: 1
            //   }
            await session.commitTransaction()
            await session.endSession()
            client.close();
        }).catch((err) => {
            console.log(err)
        }).finally(async () => {

        })
    }, transactionOptions)
    // perform actions on the collection object

}
const deletingMultipleDocsFromDbUsingTransactions = (client) => {
    const collection = client.db("test").collection("devices");
    var session = client.startSession()
    const transactionOptions = {
        "readConcern": { "level": "snapshot" },
        "writeConcern": { "w": "majority" }
    }

    session.withTransaction(async () => {
        var myquery = { address: 'ya' };

        await collection.deleteMany({
            "name": 'yashj',
        }, { session }).then(async (data) => {

            console.log(data)       //{ acknowledged: true, deletedCount: 4 }
            await session.commitTransaction()
            await session.endSession()
            client.close();
        }).catch((err) => {
            console.log(err)
        }).finally(async () => {

        })
    }, transactionOptions)
    // perform actions on the collection object


}
const deletingSingleDocFromDbUsingTransaction = (client) => {
    const collection = client.db("test").collection("devices");
    var session = client.startSession()
    const transactionOptions = {
        "readConcern": { "level": "snapshot" },
        "writeConcern": { "w": "majority" }
    }

    session.withTransaction(async () => {
        var myquery = { address: 'ya' };

        await collection.deleteOne({
            "name": 'ya',
        }, { session }).then(async (data) => {

            console.log(data)          //{ acknowledged: true, deletedCount: 1 }
            await session.commitTransaction()
            await session.endSession()
            client.close();
        }).catch((err) => {
            console.log(err)
        }).finally(async () => {

        })
    }, transactionOptions)
    // perform actions on the collection object

}
const insertingSingleDocIntoDbUsingTransaction = (client) => {
    const collection = client.db("test").collection("devices");
    var session = client.startSession()
    const transactionOptions = {
        "readConcern": { "level": "snapshot" },
        "writeConcern": { "w": "majority" }
    }

    session.withTransaction(async () => {

        await collection.insertOne({
            "name": 'ya',
            "class": 32
        }, { session }).then(async (data) => {
            console.log(data.acknowledged)
            console.log(data.insertedId.toString())
            console.log(data) //{ acknowledged: true,insertedId: new ObjectId("6313bd9cb0b77a5913297c62") }
            await session.commitTransaction()
            await session.endSession()
            client.close();
        }).catch((err) => {
            console.log(err)
        }).finally(async () => {

        })
    }, transactionOptions)
}

const insertingBulkDocsIntoDbUsingTransaction = (client) => {
    const collection = client.db("test").collection("devices");
    var session = client.startSession()
    const transactionOptions = {
        "readConcern": { "level": "snapshot" },
        "writeConcern": { "w": "majority" }
    }

    session.withTransaction(async () => {

        await collection.insertMany([{
            "name": 'yash',
            class: 11
        }, {
            name: 'ram',
            class: 3
        }], { session }).then(async (data) => {

            console.log(data)       //{acknowledged: true,insertedCount: 2,insertedIds: {'0': new ObjectId("6313c5a355e966cc7ef17e29"),'1': new ObjectId("6313c5a355e966cc7ef17e2a") } }
            await session.commitTransaction()
            await session.endSession()
            client.close();
        }).catch((err) => {
            console.log(err)
        }).finally(async () => {

        })
    }, transactionOptions)
    // perform actions on the collection object

}

```
