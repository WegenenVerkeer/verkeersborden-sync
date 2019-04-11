# verkeersborden-sync
Documentatie en schema file om een sync vanuit een externe applicatie (EXT) met de verkeersborden applicatie (VKB) op te zetten.

## EXT is master
De sync van EXT naar VKB, waarbij EXT de eigenaar van de data is, is op dit moment een PUSH vanuit de externe applicatie naar VKB.
Hierbij worden de opstellingen die in VKB bestaan overschreven met deze in de import.

### Setup
Voor er gestart kan worden met het testen, met er langs de kant van VKB wel wat setup gebeuren.

Stuur daarvoor de naam van de organisatie (gemeente/provincie/...) en applicatie door via email. Deze zal dan in VKB gemarkeerd worden als een extern beheerde organisatie, waardoor er in VKB geen wijzigingen op de opstellingen van deze organisatie kunnen gebeuren, wat ons toelaat om een enkel richting sync te gebruiken.

Dan moet er een certificaat aangemaakt en geregistreerd worden. De procedure daarvoor is als volgt:

1. AWV Team Verkeersborden.Vlaanderen definieert een CommonName (CN),
    het voorstel is: *applicatie*&lowbar;vkb&lowbar;*organisatie*&lowbar;dev&lowbar;client.mow.vlaanderen.be )
2. De organisatie genereert hiermee een CSR en een private key
3. De organisatie bezorgt ons die CSR
4. Wij genereren de public key en bezorgen die terug

Per organisatie hebben we 1 certificaat nodig, dus indien uw applicatie meerdere organisaties beheert, moet u de sync zelf opsplitsen en het juiste certificaat gebruiken.

Al de communicatie gebruikt REST calls met JSON data wanneer toepasselijk.

Via deze request kan u checken of de connectie en permissies werken:

```HTTP
GET https://services.apps-dev.mow.vlaanderen.be/verkeersborden/rest/gebruikerbeperkingen?laag=VerkeersbordOpstelling
Accept: application/vnd.awv.wdb-v3.0+json
```

Dit moet een toelating voor uw organisatie teruggeven.

### Opladen data:

Al de volgende operaties gebeuren t.o.v. de volgende url https://services.apps-dev.mow.vlaanderen.be/verkeersborden

:warning: **Let op, de zip-file mag niet te groot zijn, maximaal 100MB. Indien u meer opstellingen wilt opladen, moet u zelf meerdere oplaad calls doen met kleinere zip-files**

Importeren is een asynchrone operatie, daarom is er een transactie sleutel nodig om de operatie te kunnen opvolgen.

#### Transactie-sleutel opvragen:

```HTTP
POST /rest/verkeersborden/opstelling/transactie
Accept: application/vnd.awv.wdb-v3.0+json
```

Dit geeft een sleutel (string) terug die in de volgende call gebruikt kan worden. Deze sleutel kan maar 1 keer gebruikt worden om een file op te laden. U moet iedere keer een nieuwe sleutel opvragen.

#### Opladen zip-file:

```HTTP
POST /rest/verkeersborden/opstelling/upload/{sleutel}
Content-Type: multipart/form-data
```

zip-file zit in field met naam ``zipFile``

Formaat van zip-file: export.xml en svg files. Zie [Export-awv.xsd](Export-awv.xsd) voor schema van export.xml (met uitleg).

[Dit](verkeersborden.zip) is een voorbeeld van een zip-file.

Dit doet een upload van de data en start een import in de achtergrond.

#### Opvragen status van import:

```HTTP
GET /rest/audit/transactions/{sleutel}
Accept: application/vnd.awv.wdb-v3.0+json
```

Geeft JSON terug

```JSON
{
    "transactionResult": "",
    "errorTrace": "",
    "creationDate": "",
    "ldapId": "",
    "transactionId": "",
    "transactionType": "",
    "transactionName": "",
    "transactionStatus": "SUCCESS | EXECUTING | FAILED",
    "details": [
      {
        "identifier": "",
        "status": "WAITING | SUCCESS | INPROGRESS | FAIL | SKIPPED",
        "error": "",
        "warning": ""
      }
    ]
}
```

Details is een array van de opstellingen in de XML file. Identifier is dan de uid uit de XML file.
Error en warning zijn optioneel en geven de reden van de fout of waarschuwing weer.
Status gaat van WAITING naar SKIPPED of INPROGRESS en dan naar SUCCESS of FAIL.

Omdat het opladen een asynchrone operatie is, kan het zijn dat deze call na het starten van het opladen van de file een 404 http-status teruggeeft. Probeer dan later opnieuw. Als de service grote load heeft, kan dit potentieel lang duren.

Buiten de 404 http-status krijg je ook nog een json terug met deze vorm:

```JSON
{
    "error": "Er bestaat geen transactie met transactie ID xxxxxxxxxx"
}
```
