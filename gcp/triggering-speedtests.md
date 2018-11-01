[Home](../) > [GCP](index) > Triggering Speedtests
========================================================
_As an optional step you can add a feature to trigger running Speedtest loggers. This step should not be done before you've created both a [Speedtest Logger](https://github.com/cx-cloud-101/gcp-speedtest-logger/wiki) that is triggerable through Pub/Sub and an [Event Writer](https://github.com/cx-cloud-101/gcp-event-writer/wiki)._

Suggested solution
------------------
* Allow users to register and unregister devices through an HTTP requests
* Store registrations in GCP Datastore
* Simple endpoint to trigger Speedtest Loggers
* Create a GCP Appengine Cron Job

Setup
-----
1. Create a trigger Pub/Sub topic: `gcloud pubsub topics create speedtest-trigger`
2. Create Datastore
   1. Goto Datastore in your project
   2. Click on create datastore and select Cloud Datastore

Suggested API
-------------

### GET /trigger
Look up registered devices and publish trigger messages to a Pub/Sub topic.

PubSub message payload format
```
{
    "user": "<string>",
    "device" <number>
}
```

### POST /trigger/register
Registers a new device for the specified user.

**Request payload**
```
{
    "user": <string>,
    "device" <number>
}
```

**Response payload**
```
{
    "user": <string>,
    "devices" [<number]
}
```

### DELETE /trigger/register
Removes registration for a device on the specified user.

**Request payload**
```
{
    "user": <string>,
    "device" <number>
}
```

**Response payload**
```
{
    "user": <string>,
    "devices" [<number]
}
```

Implementation
--------------
If you followed the "Getting started"-guide you should already have added the `org.springframework.cloud:spring-cloud-gcp-starter-data-datastore` dependency.

This library builds on the concepts from Spring Data and makes it very simple to add repositories that access Datastore.

### Create an entity class
```kotlin
import com.fasterxml.jackson.annotation.JsonCreator
import com.google.common.collect.ImmutableSet
import org.springframework.cloud.gcp.data.datastore.core.mapping.Entity
import org.springframework.cloud.gcp.data.datastore.core.mapping.Field
import org.springframework.data.annotation.Id
import java.io.Serializable

@Entity(name = "someName") // The entity kind in Datastore (analogous to SQL tables)
data class SomeName @JsonCreator constructor(
        @Id
        @Field(name = "name")
        val someField: String,
) : Serializable
```

### Create a repository
Add the following attribute to `application.properties`: `spring.cloud.gcp.datastore.namespace=<name>` (name can be anything, e.g. speedtest).

```kotlin
import org.springframework.cloud.gcp.data.datastore.repository.DatastoreRepository

interface UserRepository: DatastoreRepository<SomeName, String>
```

Cron job
--------
**Example**
```yaml
cron:
  - description: Trigger registered speedtest loggers
    url: /trigger
    schedule: every 10 minutes
```
Deploy it: `gcloud app deploy cron.yaml`

Making the logger use the trigger
=================================
_In order to use our new API trigger-fuctionality, we'll have to make some changes to the logger._

Triggering the logger
---------------------
It's possible to trigger the speedtest logger to perform a speedtest and publish the results through Pub/Sub. The speedtest should only be triggered if it receives a message where `user` and `device` in the message matches the `user` and `device` that the speedtest logger was started with.

The Speedtest Logger should subscribe to the `gcp-speedtest-logger` topic. Each running instance of the Speedtest Logger must have it's own subscription to the topic (so that the different instances don't "steal" messages from each other).

You can achieve this in two ways:
1. Name subscriptions by user-device combinations and keep the subscriptions even after the logger is terminated - though then the Speedtest Logger should "swallow" all messages on startup (since the subscription will continue to receive messages even when it is not running).
2. Create a subscription with a random name when the Speedtest Logger is started, and delete the subscription when the logger is terminated. In Node.js you can achieve this with the following:

```js
constructor(projectId: string, private topic: string) {
    this.pubSubClient = new PubSub({projectId});
    this.subscriptionName = `gcp-speedtest-logger-${RandomString.generate(7)}`;
    this.createSubscription()
        .then(() => this.listenToSubscription());
    this.registerProcessCleanUp();
}
private registerProcessCleanUp() {
    const subscriptionCleanUp = () => {
        this.removeSubscription()
            .then(() => {
                process.exit(0);
            });
    };

    process.on('SIGTERM', subscriptionCleanUp);
    process.on('SIGINT', subscriptionCleanUp);
}   
```
