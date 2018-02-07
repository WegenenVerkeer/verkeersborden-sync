# verkeersborden-sync
Documentatie en schema file om een sync vanuit een externe applicatie (EXT) met de verkeersborden applicatie (VKB) op te zetten.

## EXT is master
De sync van EXT naar VKB, waarbij EXT de eigenaar van de data is, is op dit moment een PUSH vanuit de externe applicatie naar VKB.
Hierbij worden de opstellingen die in VKB bestaan overschreven met deze in de import. 

### Setup
Voor er gestart kan worden met het testen, met er langs de kant van VKB wel wat setup gebeuren.

Stuur daarvoor de naam van de organisatie (gemeente/provincie/...) door via email. Deze zal dan in VKB gemarkeerd worden als een extern beheerde organisatie, waardoor er in VKB geen wijzigingen op de opstellingen van deze organisatie kunnen gebeuren, wat ons toelaat om een enkel richting sync te gebruiken.

Als antwoord sturen we u een client-certificaat dat u moet gebruiken bij de communicatie tussen EXT en VKB.

Per organisatie krijgt u 1 certificaat, dus indien uw applicatie meerdere organisaties beheert, moet u de sync zelf opsplitsen en het juiste certificaat gebruiken.

Let op dat uw applicatie overweg kan met de Let's encrypt certificaten (zie https://letsencrypt.org/docs/certificate-compatibility/).

Al de communicatie gebruikt REST calls met JSON data wanneer toepasselijk.

Via deze URL kan u checken of de connectie en permissies werken:

https://services.apps-dev.mow.vlaanderen.be/verkeersborden/rest/gebruikerbeperkingen?laag=VerkeersbordOpstelling

Dit moet een toelating voor uw organisatie teruggeven.

### Opladen data:

Al de volgende operaties gebeuren t.o.v. de volgende url https://services.apps-dev.mow.vlaanderen.be/verkeersborden

:warning: **Let op, de zip-file mag niet te groot zijn, maximaal 100MB. Indien u meer opstellingen wilt opladen, moet u zelf meerdere oplaad calls doen met kleinere zip-files**

Importeren is een asynchrone operatie, daarom is er een transactie sleutel nodig om de operatie te kunnen opvolgen.

#### Transactie-sleutel opvragen:

POST /rest/verkeersborden/opstelling/transactie 
Accept: application/vnd.awv.wdb-v3.0+json

Dit geeft een sleutel (string) terug die in de volgende call gebruikt kan worden. Deze sleutel kan maar 1 keer gebruikt worden om een file op te laden. U moet iedere keer een nieuwe sleutel opvragen.

#### Opladen zip-file:

POST /rest/verkeersborden/opstelling/upload/{sleutel}
Content: multipart/form-data

zip-file zit in field met naam zipFile

Formaat van zip-file: export.xml en svg files. Zie [Export-awv.xsd](Export-awv.xsd) voor schema van export.xml (met uitleg). 

[Dit](verkeersborden.zip) is een voorbeeld van een zip-file.

Dit doet een upload van de data en start een import in de achtergrond.

#### Opvragen status van import:

```
GET /rest/audit/transactions/{sleutel}
Accept: application/vnd.awv.wdb-v3.0+json
```

Geeft JSON terug
{
    transactionResult: ''
    errorTrace: ''
    creationDate: ''
    ldapId: ''
    transactionId: ''
    transactionType: ''
    transactionName: ''
    transactionStatus: 'SUCCESS' | 'EXECUTING' | 'FAILED'
}

Omdat het opladen een asynchrone operatie is, kan het zijn dat deze call onmiddelijk na het starten van het opladen van de file een 404 teruggeeft. Probeer dan later opnieuw.



