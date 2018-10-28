As an optional step you can add a feature to trigger running Speedtest loggers. This step should not be done before you've created both a [Speedtest Logger](https://github.com/cx-cloud-101/gcp-speedtest-logger/wiki) that is triggerable through Pub/Sub and an [Event Writer](https://github.com/cx-cloud-101/gcp-event-writer/wiki).

## Suggested solution
* Allow users to register and unregister devices through an HTTP requests
* Store registrations in GCP Datastore
* Simple endpoint to trigger Speedtest Loggers
* Create a GCP Appengine Cron Job

## Setup
1. Create a trigger Pub/Sub topic: `gcloud pubsub topics create speedtest-trigger`
1. Create Datastore
   1. Goto Datastore in your project
   1. Click on create datastore and select Cloud Datastore

## Suggested API

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

## Implementation
If you followed the [[Getting started|Getting started]] guide you should already have added the `org.springframework.cloud:spring-cloud-gcp-starter-data-datastore` dependency.

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

## Cron job
**Example**
```yaml
cron:
  - description: Trigger registered speedtest loggers
    url: /trigger
    schedule: every 10 minutes
```
Deploy it: `gcloud app deploy cron.yaml`