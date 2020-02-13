[Home](../) > [GCP](index) > Return of the Logger
=======================================================
_The GCP Speedtest Logger is an application that runs a connection speedtest to speedtest.net for a device and publishes the results to [gcp-speedtest-api](love-the-smell-of-http)._ You should be able to run the application with `user` and `device` as arguments such that the application can be run on different devices.
See the reference implementation [here](https://github.com/cx-cloud-101/gcp-speedtest-logger).
See "[Love the smell of HTTP in the morning](love-the-smell-of-http)" for a description of the API that the Logger will publish results to.

Suggested implementation
------------------------
You can implement the application in any language you prefer, though you should consider if there are speedtest.net library implementations for that language and whether the results you get from the library comply with the GCP Speedtest API. 

We have verified that Node.js and .Net have both a speedtest.net library that comply with the GCP Speedtest API and a Pub/Sub library.

**Node.js**
* speedtest.net library: [speedtest-net](https://github.com/ddsol/speedtest.net)
* Pub/Sub library: @google-cloud/pubsub

Another option is to re-purpose the SpeedTestLogger made as part of the Azure workshop.

Versioning the code
-------------------
You'll probably want to create a new repo named gcp-speedtest-logger so you can version your logger.

What's next?
------------
When you have a logger capable of sending speedtests to the API, we can move forward and start [analyzing it all](analyzing-it-all).
