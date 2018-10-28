The Speedtest API should have an endpoint as described in [[Home|Home]] and publish the received speedtest events to a Pub/Sub topic.

1. Create pubsub topic `gcloud pubsub topics create speedtest`
1. Read about publishing messages to Pub/Sub topic with Spring Cloud: [Messaging with Google Cloud Pub/Sub](https://spring.io/guides/gs/messaging-gcp-pubsub/)
1. Implement the endpoint