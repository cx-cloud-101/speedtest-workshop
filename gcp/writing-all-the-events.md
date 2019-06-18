[Home](../) > [GCP](index) > Writing all the events
=========================================================
_The GCP Event Writer is a cloud function that subscribes to Speedtest Event messages on a Pub/Sub topic and writes them as rows in a BigQuery table._

Suggested implementation
------------------------
Cloud Functions can be written either as Node.js 8 or Python 3.7.0, we suggest that you use Node.js.

The Event Writer receives messages in the following format.

```json
{
   "user": "STRING",
   "device": "NUMBER",
   "timestamp": "NUMBER", // epoch time in ms
   "data": {
        "speeds": {
            "download": "NUMBER",
            "upload": "NUMBER"
        },
        "client": {
            "ip": "STRING",
            "lat": "NUMBER",
            "lon": "NUMBER",
            "isp": "STRING",
            "country": "STRING" // (ISO 3166-1_alpha2)
        },
        "server": {
            "host": "STRING",
            "lat": "NUMBER",
            "lon": "NUMBER",
            "country": "STRING", // (ISO 3166-1_alpha2)
            "distance": "NUMBER",
            "ping": "NUMBER",
            "id": "STRING"
        }
    }
}
```

And should write them to BigQuery.

Versioning the code
-------------------
As with all other code, you probably want to version your code, so start off by creating a new repo named gcp-event-writer.

Development guide
-----------------
The Node.js runtime expects that you have an `index.js` at the root of the project which exports functions and a `package.json` wich declares `engine` and `dependencies`. A function that receives Pub/Sub messages and writes to BigQuery can have a `package.json` like the following.
```json
{
  "name": "gcp-event-writer",
  "version": "0.0.1",
  "description": "Serverless function that writes messages from PubSub into BigQuery",
  "engines": {
    "node": "^8"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/cx-cloud-101/gcp-event-writer.git"
  },
  "author": "Alexander Hoem Rosbach",
  "license": "MIT",
  "devDependencies": {
    "uuid": "^3.3.2"
  },
  "dependencies": {
    "@google-cloud/bigquery": "^1.3.0"
  }
}
```
And an `index.js`.
```js
const speedtest = require('./src/speedtest');

exports.processSpeedtestEvent = async (pubSubMessage) => {
  if (pubSubMessage.data) {
      const speedtestEvent = pubSubMessage.data ? Buffer.from(pubSubMessage.data, 'base64').toString() : null;
      await speedtest.handleSpeedtestEvent(JSON.parse(speedtestEvent));
  }
};
```
Notice how the received `pubSubMessage` is processed through `Buffer.from`. The `pubSubMessage.data` value is a base64 encoded string of the published message. 

An important concept to understand with how Cloud Functions are executed is that they are only allocated compute time as long as they are running. As soon as the function returns something the function will be terminated, which can be tricky since most operations that you perform in Node.js are asynchronous. The exception to this rule is that any returned promise will be allowed to resolve. Which is why, as you can note from the above example, the `processSpeedtestEvent` function is declared `async`. You must apply this to any function you implement that performs some asynchronous operation. Always return a promise, and declare your functions `async` if you want/need to use the `await` functionality. If you don't already know, the `await <promise>` statement waits for the passed promise to resolve, then continues execution of the function. It is analogous to writing `<promise>.then(() => <the rest of the function>)`. A function that is declared `async` will automatically return a promise that resolves when all the `await` statements in it has resolved. You could also define your own promise that you return.

### Code structure
We recommend that you structure your code in a way that facilitates easy manual and automatic testing. A good start is to separate code that handles the trigger event from the business logic of the function. This way you can test the business logic without worrying over how to pass arguments that are structured the way that the Cloud Function Node.js 8 runtime does.

Getting Started
---------------
### Setup
1. If you've not already done so, create a pubsub topic `gcloud pubsub topics create speedtest`
1. Create a BigQuery dataset named `speedtest`
1. Create a table in the created dataset using either the suggested schema or your own

### Suggested table schema
```json
[
  {"type":"STRING","name":"user"},
  {"type":"INTEGER","name":"device"},
  {"type":"TIMESTAMP","name":"timestamp"},
  {
    "name": "data",
    "type": "RECORD",
    "mode": "REQUIRED",
    "fields": [
      {
        "name": "speeds",
        "type": "RECORD",
        "fields": [
          {
            "name": "download",
            "type": "NUMERIC",
            "mode": "NULLABLE"
          },
          {
            "name": "upload",
            "type": "NUMERIC",
            "mode": "NULLABLE"
          }
        ],
        "mode": "NULLABLE"
      },
      {
        "name": "client",
        "type": "RECORD",
        "fields": [
          {
            "name": "ip",
            "type": "STRING",
            "mode": "NULLABLE"
          },
          {
            "name": "lat",
            "type": "NUMERIC",
            "mode": "NULLABLE"
          },
          {
            "name": "lon",
            "type": "NUMERIC",
            "mode": "NULLABLE"
          },
          {
            "name": "isp",
            "type": "STRING",
            "mode": "NULLABLE"
          },
          {
            "name": "country",
            "type": "STRING",
            "mode": "NULLABLE"
          }
        ],
        "mode": "NULLABLE"
      },
      {
        "name": "server",
        "type": "RECORD",
        "fields": [
          {
            "name": "host",
            "type": "STRING",
            "mode": "NULLABLE"
          },
          {
            "name": "lat",
            "type": "NUMERIC",
            "mode": "NULLABLE"
          },
          {
            "name": "lon",
            "type": "NUMERIC",
            "mode": "NULLABLE"
          },
          {
            "name": "country",
            "type": "STRING",
            "mode": "NULLABLE"
          },
          {
            "name": "distance",
            "type": "NUMERIC",
            "mode": "NULLABLE"
          },
          {
            "name": "ping",
            "type": "NUMERIC",
            "mode": "NULLABLE"
          },
          {
            "name": "id",
            "type": "STRING",
            "mode": "NULLABLE"
          }
        ],
        "mode": "NULLABLE"
      }
    ]
  }
]
```
Note that the `timestamp` field from the received messages should be transformed using `new Date(receivedEvent.timestamp)`.

Deploying the event writer
--------------------------
To deploy a cloud function to Google Cloud Platform you run: 

```shell
gcloud functions deploy processSpeedtestEvent --trigger-topic speedtest --runtime nodejs8 --region europe-west1
```

Where
* `processSpeedtestEvent` is the name the function will have in your GCP project, and when `--entry-point` is not specified it must also be the name of a function exported in `index.js`
* `--trigger-topic <topic>` specifies that this is a cloud function triggered by messages to the given Pub/Sub topic
* `--runtime nodejs8` specifies that the function shall be executed using nodejs-8
* `--entry-point <name>` specifies that the function exported from `index.js` with name `<name>` shall be deployed

Declared dependencies in `package.json` will be installed automatically when you deploy the function.

When you're done
----------------
You now have a system with an API that can receive speedtests, and a system for storing the speedtests in Big Query. Next we'll [create a logger](return-of-the-logger) so we can get some real speedtests.
