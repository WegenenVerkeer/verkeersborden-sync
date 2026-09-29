# Opladen van signalisatie naar VKS

## Principes

### Het register

In het verleden was er geen duidelijke defenitie van de opschriften van borden, in het beste geval hadden we een bordcode en een lijst van opschriften.

Dit was een probleem later in de ketting wanneer er interpretaties moesten gebeuren over de aanwezige borden op het terrein, denk bv aan de automatische doorstroming van bv. tonnage beperkingen.

Daarom is er beslist om een centraal register van signalisatie op te zetten, daarin wordt bepaald welke signalisatie er kan zijn, met welke code, welke afmetingen en welke opschriften.

Wanneer er signalisatie wordt aangeleverd, zal er actief worden afgecheckt t.o.v. dit register of aan alle voorwaarden voldaan is.

Een concreet voorbeeld: een C21 bord (tonnagebeperking) moet 1 parameter _"Tonnage"_ hebben wat een decimaal getal moet zijn. Iets anders wordt niet aanvaard.

Dit geldt ook voor wegmarkeringen: bv. een onderbroken lijn (WM72.3), heeft 2 parameters: _"Breedte"_ met bv. waarde _"0,15 m"_ en _"Type_onderbroken_streep"_ met waarde _"Standaard"_.

#### Linken naar het register:

_**TODO**_

### Het model

Voor de effectieve data uitwisseling, maken we gebruik van binnen de Vlaamse Overheid geldende standaarden, nl. OSLO en OTL modellen.

_**TODO**: link naar het model_ 

Op deze moment zijn we de laatste aanpassingen aan het doen aan dit model.

Een model bestaat uit objecten met attributen en relaties tussen de objecten. De objecten en hun relaties worden uitgedrukt in een JSON-LD formaat.

_**TODO**: voorbeelden_

### API

Als eindpunt van een aanlevering, gebruiken we de DAVIE applicatie van AWV. Om toegang te krijgen tot de REST API van DAVIE, heb je een OAuth token nodig.

_**TODO**: aanvragen token beschrijven?_

#### Interacties met DAVIE

_**TODO**: overnemen of linken naar DAVIE documentatie en Swagger?_

