Overview
========

Measuring speed
---------------
0. Create git repo
1. Create console app
2. Install Speedtest.net package
3. Print some stuff to console
3. Create basic logger in Program.cs
4. Add configuration
5. Refactor into SpeedTestRunner.cs (This introduces TestResult.cs)
6. Switch to using fake test-results, so you don't overload the network

An API for all things
---------------------
0. Create git repo
1. Create new api-app
2. Do the stuff with hsts and https-enforcing in Startup.cs
3. Create Ping() And test it in the browser.
4. Commit everything.

It's alive!
-----------
1. Create new resource group
2. Create new API app
3. Deploy API to API app
4. Test Ping.

Receiving speeds
----------------
0. Switch to using fake test-results, so you don't overload the network
1. Create `UploadSpeedTest()` and TestResult.cs with restrictions. Print results to console and return something fake.
2. Push to master and test on Azure.

Events, events, events
----------------------
1. Create new event hub on Azure (use standard tier)
2. Add Microsoft.Azure.EventHubs and create SpeedTestEvents
3. Test locally, and note event added to hub.
4. Add fake authentication. This introduces appsettings.Development.json
5. Push and test again.

Analytics!
----------
1. Create Time Series Insights
2. Create consumer-group on event hub
3. Create new event source (Timestamp has to be timestamp property!)
4. Open Insigths Explorer and run some speedtests.

Scheduling speedtests
---------------------
1. Create Service Bus (standard pricing tier)
2. New Shared Access Policy SpeedTestLogger (listen) and SpeedTestScheduler (listen, read, manage)
3. New topic run-speedtest (30 min message time to live)
4. New subscription (30 min message time to live, 5 min lock duration)
5. New Logic App
6. Open designer and choose Recurrence (5 min)
7. Connect to Service Bus (dynamic content as session id `guid()`)
8. Add Microsoft.Azure.ServiceBus to logger
9. Write code to get logger to listen to bus
10. Test it all out

Optional A: Web-api and website
-------------------------------
1. Table Storage for simplified data
2. Logic app to put event into table storage
3. New endpoint in api to get speedtests from table storage
4. Quick and dirty webpage to view speedtests

Optional B: Application Insights logging
----------------------------------------
1. Replace Console.WriteLine with Application Insights logging