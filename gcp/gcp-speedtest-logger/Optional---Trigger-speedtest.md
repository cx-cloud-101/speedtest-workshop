As an optional extension to the application it could be possible to trigger the speedtest logger to perform a speedtest and publish the results through Pub/Sub. See [Trigger speedtest logger](https://github.com/cx-cloud-101/gcp-speedtest-api/wiki/Optional---Trigger-speedtest-logger(s)) for the message format.

The speedtest should only be triggered if it receives a message where `user` and `device` in the message matches the `user` and `device` that the speedtest logger was started with.

The Speedtest Logger should subscribe to the `gcp-speedtest-logger` topic. Each running instance of the Speedtest Logger must have it's own subscription to the topic (so that the different instances don't "steal" messages from each other). You can achieve this in two ways.
1. Name subscriptions by user-device combinations and keep the subscriptions even after the logger is terminated - though then the Speedtest Logger should "swallow" all messages on startup (since the subscription will continue to receive messages even when it is not running).
1. Create a subscription with a random name when the Speedtest Logger is started, and delete the subscription when the logger is terminated. In Node.js you can achieve this with the following:
   ```
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