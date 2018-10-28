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

## Code structure
We recommend that you structure your code in a way that facilitates easy manual and automatic testing. A good start is to separate code that handles the trigger event from the business logic of the function. This way you can test the business logic without worrying over how to pass arguments that are structured the way that the Cloud Function Node.js 8 runtime does.
