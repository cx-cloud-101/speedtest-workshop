ServiceBus + Logic App
======================
Lag en service-bus med standard pricing tier.
Gi den navnet speedtest-commands.
Lag en ny Shared access policy SpeedTestLogger med listen rettighet.
Lag en topic med navn "run-speedtest" bruk 30 min som message time to live.
Lag en subscription "speedtest-logger-subscription" message time to love 30 min. Lock duration 5 min

Lag en ny logic app "speedtest-scheduler" i samme rg.
Åpne designeren og velg "Recurrence".
- 5 min
- Navn SpeedtestScheduler koblet til RootManageSharedAccessKey på riktig service bus
- Velg dynamisk content på session id `guid()`

i az-speedtest-logger legg til <PackageReference Include="Microsoft.Azure.ServiceBus" Version="3.1.1" />
restore

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

Optional C: Maybe some authentication?
--------------------------------------
1. Azure AD
