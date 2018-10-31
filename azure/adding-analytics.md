Adding Analytics
================

Analytics!
----------
1. Create Time Series Insights
2. Create consumer-group on event hub
3. Create new event source (Timestamp has to be timestamp property!)
4. Open Insigths Explorer and run some speedtests.

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