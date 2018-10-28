---
---
Notes on Java setup
===================

Development tools
-----------------

### Installasjon av JDK
Følger denne: https://thishosting.rocks/install-java-ubuntu/
- Velger default JDK
- Husk å sett JAVA_HOME

### Installasjon av gradle
- sudo apt-get install gradle

### IDE
Trying out IntelliJ IDEA (https://www.jetbrains.com/idea/)
Ultimate version has 30 day trial, so should work for workshop.

### Utsjekk av kode og åpning
- Hent ut koden og importer eksisterende prosjekter i idea (valgte /home/teodoran/cloud-101/gcp-speedtest-api)
- Velger Create Project from existing sources 
- Trykker next på resten
- Etter prosjektet er åpnet og sjekker File -> Project Structure -> SDK Location Velger installert JDK hvis ingenting er valgt
- Neste gang jeg åpnet det fikk jeg opp import module from gradle. Velg det.
- Husk og velge SpeedtestApiApplication fra nedtrekksvelgeren

### GCloud hemmeligheter
Ser ut som gradle leser 'GCLOUD_CONFIG' fra miljøet eller noe?
Tar dette med Alexander