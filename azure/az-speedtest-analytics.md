Azure Data Explorer
===================

Lag Azure Data Explorer cluster. (https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)
Lag en database.
Query databasen.

Lag en tabell.
```
.create table SpeedTest (TimeStamp: long, User: string, Device: int, Download: double, Upload: double)
```

Lag mapping fra json til tabell (https://docs.microsoft.com/en-us/azure/kusto/management/mappings?branch=master#json-mapping)
```
.create table SpeedTest ingestion json mapping 'SpeedTestMapping' '[
    {"column":"TimeStamp","path":"$.Timestamp","datatype":"long"},
    {"column":"User","path":"$.User","datatype":"string"},
    {"column":"Device","path":"$.Device","datatype":"int"},
    {"column":"Download","path":"$.Data.Speeds.Download","datatype":"double"},
    {"column":"Upload","path":"$.Data.Speeds.Upload","datatype":"double"}
]'
```

Må være på en linje
```
.create table SpeedTest ingestion json mapping 'SpeedTestMapping' '[{"column":"TimeStamp","path":"$.Timestamp","datatype":"long"},{"column":"User","path":"$.User","datatype":"string"},{"column":"Device","path":"$.Device","datatype":"int"},{"column":"Download","path":"$.Data.Speeds.Download","datatype":"double"},{"column":"Upload","path":"$.Data.Speeds.Upload","datatype":"double"}]'
```

Lag data ingestion fra EventHub til databasen.
Kjør noen speedtests eller noe.

Vis frem graf:
```
SpeedTest
| order by TimeStamp desc
| take 10
| project datetime_add('millisecond', TimeStamp, make_datetime(1970,1,1)), Download, Upload
| render columnchart
```

Time Series Insights
====================
_https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-overview_

Lag en ny Time Series Insights i rg.
Gå til event-hub og lag en ny consumer group "speedtest-analytics"
(Må da huske å bruke standard tier på event-hub)

Går til Event Sources og lager ny source "speedtest-events"
Velg riktig consumer group og event hub
Sett "Timestamp" som timestamp property
Åpne insigths explorer fra portalen.

API App
=======
Lag en ny API App
Gi den navn på formen "tae-speedtest-api"
Velg eksisterende rg
Når den er oppe, gå til appen og:
- Deployment options: Velg source GitHub
- Fyll inn for å få inn az-speedtests-api
- Gå til "Applications settings" og fyll inn under Applications settings

```
AuthorizedUser = teodoran
EventHub:ConnectionString = ""
EventHub:EntityPath = ""
```

Restart appen

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


Note!
-----
usual-pass + "Cloud101"