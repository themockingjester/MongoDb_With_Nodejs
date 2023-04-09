# MongoDb_With_Nodejs
repo contains examples for connecting and using mongodb with node

## basic commands
### Shutting down mongodb server
![Shutting mongodb server](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/shutting%20down%20mongo%20db%20server.png)
### Running mongo db server in background
![Running mongodb server in background](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/running%20mongodb%20in%20background.png)
### Setting dbpath and logpath in mongodb server
![setting dbpath and logpath](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/Setting%20dbpath%20and%20logpath%20for%20mongodb%20server%20while%20running.png)

### running mongodb with config file
![running mongodb with config file](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/running%20mondb%20server%20with%20onfig%20file.png)


## Writes in mongodb
### writing data even if data writing for record fails in between
![data writing even after failing of a record in between](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/allowing%20mongodb%20to%20continue%20adding%20data%20if%20error%20occurs.png)

### write concern in mongodb
![write concern](https://github.com/themockingjester/MongoDb_With_Nodejs/blob/main/writeconcern.png)
write concern comes into picture whenever we want to either write or update data in mongodb.
from above diagram we can see that client want to write in mongodb using insertOne command and mongodb takes helps from storage engine for doing writing task. An important thing to note here is journal (it is a file in which those write operations are written which havn't been performed yet in mongodb so in case if mongodb server fails in between , mongodb can recover data from this file).

Let's have a look at three other options denoted by orange color.
1) where w:1 and j:undefined: it specifies mongodb should return acknowledge message only when at least one instnce of mongodb has written the data.
2) where w:1 and j:true : it means mongodb should return acknoledge message for our operation (either write or update) only when at least one instnce of mongodb has written the data also our operation has to be added in journal file (this will be good when mongodb went down bcz of some issue also didn't added data into journal but return us with acknoledge message hence the command can give us assurity that our operation has been receorded successfully)
3) third one says mongodb have to perfrom action (denoted by second point) but have to give response in given time specified.

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
