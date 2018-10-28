# Setup
1. Create pubsub topic gcloud pubsub topics create speedtest
1. Create a BigQuery dataset named `speedtest`
1. Create a table in the created dataset using either the suggested schema or your own

## Suggested table schema
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