[Home](../) > [GCP](index) > Love the smell of HTTP in the morning
========================================================================
_This time we'll start with the API. The GCP Speedtest API application is an HTTP API that receives speedtest events and publishes them as Pub/Sub messages to be further processed by other applications._

Suggested implementation
------------------------
We suggest that you implement the Speedtest API as a GCP Appengine Standard Java app using Spring Boot and Spring Cloud. 
The [reference application](https://github.com/cx-cloud-101/gcp-speedtest-api) is implemented using Kotlin and Maven as the build tool, you may choose Java and/or Gradle if you prefer.

### API
Implement the following API.

#### GET /ping

Should respond with PONG or something similar.

#### POST /speedtest

_Request body:_
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
            "ip": "STRING?",
            "lat": "NUMBER?",
            "lon": "NUMBER?",
            "isp": "STRING?",
            "country": "STRING?" // (ISO 3166-1_alpha2)
        },
        "server": {
            "host": "STRING?",
            "lat": "NUMBER?",
            "lon": "NUMBER?",
            "country": "STRING?", // (ISO 3166-1_alpha2)
            "distance": "NUMBER?",
            "ping": "NUMBER?",
            "id": "STRING?"
        }
    }
}
```

Versioning the code
-------------------
You'll probably want to version your code. You can either create a new repo named gcp-speedtest-api on GitHub and clone it locally, or you can create a local repo named gcp-speedtest-api as shown below.
```bash
git init gcp-speedtest-api
```

Getting Started
---------------
Use [Spring Initializr](https://start.spring.io/) to generate your project.

1. Select setup of build tool and language. Our examples use Kotlin and Maven, but you can choose between Java or Kotlin and Gradle or Maven if you prefer something else. Notice the selections in **bold**, especially **jar** packaging.![](images/create-project-1b.png)

1. Add dependencies: `Spring Web` and `GCP Messaging` ![](images/create-project-2b.png)

1. Generate project and unzip the downloaded archive. Copy the unzipped ´api´ folder (whole folder) into your git repo folder.

1. Open IntelliJ, click "Open", and select your git repo folder, containing the unzipped files.
    ![](images/open-project.png)

1. Modify `dependencies` in `pom.xml` so that Maven can find the appengine plugin.
   ```xml
    <dependency>
	      <groupId>com.google.cloud.tools</groupId>
	      <artifactId>appengine-maven-plugin</artifactId>
	      <version>2.4.1</version>
    </dependency>
   ```

1. Modify `pom.xml` to invoke the appengine plugin (Add the last line to the `plugins`-section). Remember to replace PROJECT_ID with your GCP project id.
    ```xml
    			<plugin>
				<groupId>com.google.cloud.tools</groupId>
				<artifactId>appengine-maven-plugin</artifactId>
				<version>2.4.1</version>
				<configuration>
					<projectId>PROJECT_ID</projectId>
					<version>GCLOUD_CONFIG</version>
				</configuration>
			</plugin>
    ```
_You can find the project ID for your project by looking at the "Project info" tile shown on [console.cloud.google.com/home](https://console.cloud.google.com/home)._

![](images/project-info-gcp.png)

1. Create the folder `/src/main/appengine/`. Then add the following file in that folder:
`app.yaml`
```yaml
runtime: java11
```

Testing the API
---------------

Let's test if all that setup worked as expected.

### Add test endpoint
Create a new class named `HelloResource` under `/src/main/kotlin/com.speedtest.api/`. Implement a simple controller handling a simple GET request.

**Example**

```kotlin
package com.speedtest.api

import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.PathVariable
import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.web.bind.annotation.RestController

@RestController
@RequestMapping("/hello")
class HelloResource {

    @GetMapping(path = ["{name}"])
    fun getHello(@PathVariable("name") name: String): String = "Hello $name"
}
```

### Locally
Before we can start out API, we need to generate [Application Default Credentials](https://cloud.google.com/docs/authentication/production) for connecting to GCP. This is needed since the GCP Messaging plugin to Spring Boot will lock for the dependencies, and fail to start if they're not present. To generate the credentials, open a terminal and run the following command.

```shell
$> gcloud auth application-default login
Your browser has been opened to visit:
...
Credentials saved to file:
...
These credentials will be used by any library that requests
Application Default Credentials.
...
```

Start the application either by running the `ApiApplication` class in your IDE, or run the following maven command:

* `mvn spring-boot:run`

_Both ways of starting the application should work, so pick the one that works for you._

If the API started successfully, you should be able to open [http://localhost:8080/hello/speedtest](http://localhost:8080/hello/speedtest) in your browser and get a response.

![](images/hello-speedtest.png)

### Deploy to GCP

If everything went well, you're ready to deploy the API to GCP.

Start by creating an App Engine Application in your GCP Project by running the following command in a terminal, and selecting `europe-west` as the region.

![](images/create-app-1.png)

Navigate to your gcp-speedtest-api/api repo folder, and use maven to deploy your app to appengine.

```
$ gcp-speedtest-api/api> mvn clean install appengine:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] -------------------------< com.speedtest:api >--------------------------
[INFO] Building api 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------

// A lot more output omitted here

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  03:42 min
[INFO] Finished at: 2021-11-09T16:40:20+01:00
[INFO] ------------------------------------------------------------------------
```

_If you get an error saying that `JAVA_HOME` is not set, and that java is missing from `PATH`, you'll need to do both. `JAVA_HOME` should be the folder where you installed the Java SE 11 JDK. To the `PATH` variable, you'll need to add the `\bin` folder under the `JAVA_HOME` folder._

If everything worked, you should be able to open [https://your-project-id.ew.r.appspot.com/hello/speedtest](https://your-project-id.ew.r.appspot.com/hello/speedtest), replacing "your-project-id" with the ID of your GCP project.

![](images/gcp-hello-speedtest.png)

You can also take a look at [console.cloud.google.com/appengine](https://console.cloud.google.com/appengine) to view metrics and trace logs about your newly created App Engine Application.

Implementing gcp-speedtest-api
------------------------------

The Speedtest API should have an endpoint as described above under "Suggested implementation" and publish the received speedtest events to a Pub/Sub topic.

### Start with /ping

It should be quite easy to modify `HelloResource` to be a `PingResource` instead. Can you figure out how it should be done?

_Tip: Remember to update the `@RequestMapping(...)` as well as the class name_

### Creating /speedtest

As a start, we're going to create a simplified version of the /speedtest endpoint that only accepts a simple user model. Create another class named `SpeedTestResource` under `/src/main/kotlin/com.speedtest.api/` and add the following code:
```kotlin
package com.speedtest.api

import org.springframework.web.bind.annotation.PostMapping
import org.springframework.web.bind.annotation.RequestBody
import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.web.bind.annotation.RestController

@RestController
@RequestMapping("/speedtest")
class SpeedTestResource {

    data class User(val name: String, val email: String, val telephone: String)

    @PostMapping
    fun createUser(@RequestBody user: User) {
        println(user.name)
    }
}
```

_Note that you can use the kotlin construct `data class` to represent the request body_

Test the partially implemented /speedtest endpoint by using Postman, or a similar tool. It should be able to accept a POST request to  [http://localhost:8080/speedtest](http://localhost:8080/speedtest) with a request body containing JSON along the lines of:

```json
{
    "name": "Alex",
    "email": "email@email.com",
    "telephone": "12345678"
}
```

Now it's up to you to extend the /speedtest endpoint so it will accept a full speedtest result. When you're done, it should be able to receive a JSON request body as shown below:

```json
{
   "user": "gcp-101-testuser",
   "device": 1,
   "timestamp": 1581558456119,
   "data": {
        "speeds": {
            "download": 60.4,
            "upload": 20.7
        },
        "client": {
            "ip": "127.0.0.1",
            "lat": 1.2,
            "lon": 92.7,
            "isp": "Get",
            "country": "no-NB"
        },
        "server": {
            "host": "get.speedtest.net",
            "lat": 1.3,
            "lon": 93.4,
            "country": "no-NB",
            "distance": 3001,
            "ping": 12,
            "id": "42"
        }
    }
}
```

![](images/postman-response.png)

Publish messages to Pub/Sub
---------------------------

Our Spring Boot project already includes the required packages to publish messages to a Pub/Sub topic, but before we can do that, we'll need to create the topic in GCP using `gcloud pubsub`:

```shell
$> gcloud pubsub topics create speedtest
Created topic [projects/you-project-id/topics/speedtest].
```

Spring will automatically inject a bean into the `pubSub` field in the class `SpeedTestResource` below. You can achieve the same in Java by creating a class with the field `private final PubSubTemplate pubSub;` and a constructor that has a `PubSubTemplate` parameter that is assigned to the field. With this in mind, we can extend or API so it sends publishes speedtests to our Pub/Sub topic.
```kotlin
@RestController
@RequestMapping("/speedtest")
class SpeedTestResource(val pubSub: PubSubTemplate) {

    val gson: Gson = Gson()

    // Old code omitted

    @PostMapping
    fun publishTestResult(@RequestBody testResult: TestResult) {
        this.pubSub.publish("speedtest", gson.toJson(testResult))

        // Old code omitted
    }
}
```

Note that we have to serialize the `TestResult` as JSON. This is done with an instance of the Gson library.

Now we can test if we're able to publish events to GCP. Start the API locally, and send a few requests to /speedtest. Then navigate to [console.cloud.google.com/cloudpubsub/topic](https://console.cloud.google.com/cloudpubsub/topic), and after a little while you should see your messages appear under the speedtest topic.

![](images/pub-sub-hello.png)

If everything went well, you can publish the updated API to GCP with `mvn clean install appengine:deploy` in the folder`/gcp-speedtest-api/api`, and test that it works there as well. Check in your changes and/or push them to github if you feel like doing that.

You have an API on GCP. What now?
---------------------------------

Next we'll want to store our speedtest-messages somewhere suitable. Let's continue and [create a Cloud Functions that stores our speedtests in BigQuery](writing-all-the-events)
