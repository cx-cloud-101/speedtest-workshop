The GCP Speedtest API application is an HTTP API that receives speedtest events and publishes them as Pub/Sub messages to be further processed by other applications.

**Optional:** as an extension to the application it could also trigger speedtest loggers through Pub/Sub.

## Suggested implementation
We suggest that you implement the Speedtest API as a GCP Appengine Standard Java app using Spring Boot and Spring Cloud. The [reference application](https://github.com/cx-cloud-101/gcp-speedtest-api) is implemented using Kotlin and Gradle as the build tool, you may chose Java 8 and/or maven if you prefer.

## API

### POST /speedtest
**Request body**
```json
{
   "user": <string>,
   "device" <number>,
   "timestamp": <number>, // epoch time in ms
   "data": {
        "speeds": {
            "download": <number>,
            "upload": <number>
        },

        "client": {
            "ip": <string>,
            "lat": <number>,
            "lon": <number>,
            "isp": <string>,
            "country": <string> // (ISO 3166-1_alpha2)
        },

        "server": {
            "host": <string>,
            "lat": <number>,
            "lon": <number>,
            "country": <string>, // (ISO 3166-1_alpha2)
            "distance": <number>,
            "ping": <number>,
            "id": <string>
        }
    }
}
```
