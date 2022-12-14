# MongoDb_With_Nodejs
repo contains examples for connecting and using mongodb with node

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
