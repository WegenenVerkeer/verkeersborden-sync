# verkeersborden-sync
Documentatie en schema file om een sync vanuit een externe applicatie (EXT) met de verkeersborden applicatie (VKB) op te zetten.

## EXT is master
De sync van EXT naar VKB, waarbij EXT de eigenaar van de data is, is op dit moment een PUSH vanuit de externe applicatie naar VKB.
Hierbij worden de opstellingen die in VKB bestaan overschreven met deze in de import.

### Setup
Voor er gestart kan worden met het testen, met er langs de kant van VKB wel wat setup gebeuren.

Stuur daarvoor de naam van de organisatie (gemeente/provincie/...) en applicatie door via email. Deze zal dan in VKB gemarkeerd worden als een extern beheerde organisatie, waardoor er in VKB geen wijzigingen op de opstellingen van deze organisatie kunnen gebeuren, wat ons toelaat om een enkel richting sync te gebruiken.

Dan moet er een certificaat aangemaakt en geregistreerd worden.


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

Formaat van zip-file: export.xml en svg files. Zie [Export-awv.xsd](Export-awv.xsd) voor schema van export.xml (met uitleg) en [Coordinaten.md](Coordinaten.md) voor meer uitleg over het coordinaten systeem van de voorstelling en opstelling.

Opstellingen kunnen simpel verwijderd worden door ``teverwijderen`` op ``true`` te zetten.

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
# Bulk upload rechtstreeks in Verkeersbordendatabank
In tegenstelling tot de sync is dit een eenmalige actie. 
Na deze upload wordt het wijzigingen van opstellingen door de gebruiker rechtstreeks in de Verkeersbordendatabank gedaan. 
Ze zijn dus geen extern beheerder.

Van de beheerder wordt verwacht dat hij eerst enkel en alleen zijn opstellingen verwijderd vooraleer hij een bulk upload van de aangeleverde opstellingen oplaad.
Men kan dit aanzien als een eenmalige aanlevering ikv een opdracht.

### Eigenschappen van het aan te leveren zip bestand.
Het zip bestand moet voldoen aan de voorbeeldenbestand(en) die op deze Github pagina beschikbaar zijn.

In dit zip-bestand zitten buiten het export.xml bestand ook mogelijke foto's en SVG-bestanden van de opstellingen die beschreven zijn in het XML-bestand.
Het export.xml bestand moet voldoen aan het schema bestand (Export-awv.xsd).

Een zip bestand mag nooit groter zijn dan 100Mb.

Indien dit wel het geval is moet het zip bestand opgesplitst worden in meerdere aparte zip-bestanden.
Per afzonderlijk zip bestand moet het export.xml bestand ook opgesplist zijn en mogen enkel bijhorende foto's en SVG's aanwezig waarnaar het opgeplitste export.xml bestand naar verwijst.



