The GCP Event Writer is a cloud function that subscribes to Speedtest Event messages on a Pub/Sub topic and writes them as rows in a BigQuery table. 

Cloud Functions can be written either as Node.js 8 or Python 3.7.0, we suggest that you use Node.js. 

The Event Writer receives messages in the following format.
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

And should write them to BigQuery.