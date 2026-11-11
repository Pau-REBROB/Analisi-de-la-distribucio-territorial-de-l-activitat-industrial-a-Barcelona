# DOCUMENTACIÓ MÒDULS 01_ADQUISICIÓ
## REQUESTS
`Requests` és una llibreria Python que permet enviar consultes HTTP i extreure informació de la web.

Importació del mòdul.
```python
import requests
```

Per interactuar amb la informació continguda a una pàgina web s’utilitzen els mètodes HTTP (*HTTP methods*): ***get***, ***head***, ***post***, ***put***, ***patch*** i ***delete***.

És el servidor qui defineix quins mètodes HTTP accepta per a cada recurs.

El mètode `get` fa una petició d’un recurs o d'unes dades al servidor on s’allotja la web.
```python
url = "https://www.meteo.cat/"

r = requests.get(url)
```
  
La resposta del servidor es guarda en un objecte tipus `Response`. Sobre aquest objecte es pot obtenir diferent tipus d’informació.

`r.content` retorna les dades directament com s’han rebut del servidor; retorna els bytes descarregats.

`r.text` permet visualitzar el contingut com a text HTML; interpreta els bytes de `content` com a text llegible per humans segons la codificació indicada.
```python
print(type(r.content))
print(type(r.text))
```

`r.encoding` retorna la codificació del text.
```python
r.encoding
'UTF-8'
```

`r.headers` retorna les capçaleres del contingut. Les capçaleres proporcionen informació sobre el contingut, incloent la mida, el tipus de contingut i la codificació.
```python
r.headers
{
  'Content-Length': '481671',
  'Content-Type': 'text/html;charset=UTF-8'
}
```

Al retornar-se en forma de diccionari de Python, es pot accedir al seu contingut.
```python
r.headers['Content-Type']
'text/html;charset=UTF-8'
```

`r.url` permet visualitzar la direcció URL del contingut.

L’estat de la resposta es troba codificat en diferents valors. El mètode `r.status_code` retorna el valor de l’estat com a:
- 200
- 400
- 404 Client Error

`r.request.method` retorna el mètode HTTP utilitzat per generar la resposta.

Totes les consultes poden tenir límits en quant al temps d’espera d’obtenir una resposta. El paràmetre `timeout` indica el temps en segons que el request esperarà a obtenir una resposta.
```python
url = "https://www.meteo.cat/"

r = requests.get(url, timeout=60)
```

## xml.etree.ElementTree (ET)
`xml.etree.ElementTree` - o `ET` - és una llibreria Python que implementa una manera simple i eficient de tractar i crear dades tipus XML.

**XML** és un format de dades inherentment jeràrquic, de manera que la forma més natural de representar-lo és com un arbre. La classe `ElementTree` representa el document XML sencer com a arbre, mentre que la classe `Element` representa un node individual de d'aquest arbre.

ccc


```python
import xml.etree.ElementTree as ET
from pathlib import Path

carpeta_script = Path(__file__).parent

ruta_xml = carpeta_script / "exemple_xml_docu.xml"

tree = ET.parse(ruta_xml)
root = tree.getroot()
```
