The GCP Speedtest Logger is an application that runs a connection speedtest to speedtest.net for a device and publishes the results to the [GCP Speedtest API](https://github.com/cx-cloud-101/gcp-speedtest-api/wiki). You should be able to run the application with `user` and `device` as arguments such that the application can be run on different devices.

See [GCP Speedtest API](https://github.com/cx-cloud-101/gcp-speedtest-api/wiki) for the API that the Logger shall publish results to.

**Optional:** as an extension to the application it could be possible to trigger the speedtest logger to perform a speedtest and publish the results through Pub/Sub. See [Trigger speedtest logger](https://github.com/cx-cloud-101/gcp-speedtest-api/wiki/Optional---Trigger-speedtest-logger(s)) for the message format.

## Suggested implementation
You can implement the application in any language you prefer, though you should concider if there are speedtest.net library implementations for that language and whether the results you get from the library comply with the GCP Speedtest API. 

We have verified that Node.js and .Net have both a speedtest.net library that comply with the GCP Speedtest API and a Pub/Sub library.

**Node.js**
* speedtest.net library: speedtest-net
* Pub/Sub library: @google-cloud/pubsub

