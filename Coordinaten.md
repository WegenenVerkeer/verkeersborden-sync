# Omschreven rechthoek
De omschreven rechthoek van een bord is de kleinst mogelijke rechthoek waarbinnen het bord past. 
In het geval van een rechthoekig bord is dit heel simpel: 
```xml
    <bord>
      <type>F17</type>
      <vorm>rh</vorm>
      <afmeting1>1300.0</afmeting1>
      <afmeting2>900.0</afmeting2>
      <omschrevenRechthoek>
        <breedte>1300.0</breedte>
        <hoogte>900.0</hoogte>
      </omschrevenRechthoek>
    </bord>
```
Voor een rechthoekig bord wordt het al ingewikkelder:
```xml
    <bord>
      <type>A1b</type>
      <vorm>dh</vorm>
      <afmeting1>700.0</afmeting1>
      <afmeting2>0.0</afmeting2>
      <omschrevenRechthoek>
        <breedte>634.0</breedte>
        <hoogte>561.0</hoogte>
      </omschrevenRechthoek>
    </bord>
```
De omschreven rechthoek van een opstelling/aanzicht is dan analoog de kleinst mogelijke rechthoek waarbinnen alle borden van die opstelling/aanzicht passen.
