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

Importació del mòdul.
```python
import xml.etree.ElementTree as ET
```

**XML** és un format de dades inherentment jeràrquic, de manera que la forma més natural de representar-lo és com un arbre. La classe `ElementTree` representa el document XML sencer com a arbre (*tree*), mentre que la classe `Element` representa un node individual de d'aquest arbre, inclosa l'arrel (*root*).

L’element *tree* es pot crear important les dades des d’un arxiu .xml local amb el mètode `parse()`.
```python
tree = ET.parse('document.xml')
```

Si l’arxiu xml s’ha obtingut des d’una consulta `get` HTTP i no està desat en local, sinó com a objecte tipus `response`, cal utilitzar la llibreria `io` per crear un objecte a partir dels bytes de la resposta que es comporti com un arxiu.
```python
import io

tree = ET.parse(io.BytesIO(response.content))
```

L’objecte *tree* actua com el contenidor del document XML complet. L’arrel del document, però, és l’element a través del qual es pot accedir al document i treballar amb el seu contingut.

El mètode `getroot()` és el que permet crear un objecte *root* a partir de l’objecte *tree*.
```python
tree = ET.parse("document.xml")
root = tree.getroot()
```
   
Ara bé, si no hi ha necessitat de modificar el document XML i només es desitja accedir al seu contingut, realment no cal crear l’element *tree*. Es pot crear l’arrel de manera directa amb el mètode `fromstring()`, introduint el contingut XML en format *string*. Si l’arxiu xml s’ha obtingut des d’una consulta `get` HTTP i no està desat en local, sinó com a objecte tipus `response`, es pot fer passar com a argument el contingut de la resposta, tant com a *content* com a *text*.
```python
root = ET.fromstring(response.content)

root = ET.fromstring(response.text)
```

El mètode `fromstring` està preparat per rebre el contingut d’un XML en format binari, no única i explícitament en format text. Si s’introdueix com a `response.text`, el mòdul `Requests` fa la descodificació prèvia dels bytes a text utilitzant la codificació que ha detectat o que indiqui la resposta HTTP, mentre que a partir de `response.content` és el mòdul `ET` qui codifica el document a text segons la codificació declarada al document XML.

La manera de visualitzar un document XML per pantalla és la combinació del mètode `indent()` - que afegeix espais en blanc, per defecte 2, als nivells inferiors - i el mètode `dump()` - que imprimeix per pantalla l’element XML.
```python
ET.indent(tree)
ET.dump(tree)
```

### Arrel (*root*)
L’arrel d’un XML conté una etiqueta principal, accessible a través del mètode `tag`.
```python
root.tag
```

L’arrel està estructurada en fills (*children*) que pengen directament de l’ella. Aquests fills estan estructurats en forma d’etiqueta (*tag*) i atributs (*attributes*).

Així, del següent XML d’exemple, *data* és l’etiqueta de l’element arrel, que conté 3 fills etiquetats com a *country*. Cada un d’aquests fills té un sol atribut, que és *name*.
```xml
<?xml version="1.0"?>
<data>
	<country name="Liechtenstein">
	</country>
  <country name="Singapore">
	</country>
	<country name="Panama">
	</country>
</data>
```

L'estructura jeràrquica fa que els *for loops* siguin molt eficients per a accedir a la informació que penja de l'arrel.
```python
for child in root:
  print(
    "Etiqueta:", child.tag,
	  "| Atributs:", child.attrib
  )

# Etiqueta: country | Atributs: {name="Liechtenstein"}
# Etiqueta: country | Atributs: {name="Singapore"}
# Etiqueta: country | Atributs: {name="Panama"}
```

Cada fill pot tenir associada nova informació, addicional als atributs. En aquest cas, es declara una etiqueta filla i un contingut textual amb el valor associat a l’etiqueta.
```xml
<?xml version="1.0"?>
<data>
	<country name="Liechtenstein">
    <year>2008</year>
		<gdppc>141100</gdppc>
	</country>
  <country name="Singapore">
		<year>2011</year>
		<gdppc>59900</gdppc>
	</country>
	<country name="Panama">
		<year>2011</year>
		<gdppc>13600</gdppc>
	</country>
</data>
```

Del XML d’exemple, cada entrada té associada dos elements fills (*year* i *gdppc*) amb dos continguts textuals que li donen valor. Cada un d'aquests element té associat un mètode *tag* i un mètode *text* per explorar el seu contingut.
```python
for child in root:
	for element in child:
		print(
			"Entry:",
      "| Etiqueta:", child.tag,
	 		"| Atributs:", child.attrib,
			"|| Elements:",
			element.tag,
			element.text
    )

# Entry: | Etiqueta: country | Atributs: {name="Liechtenstein"} || Elements: year 2008 gdppc 141100
# Entry: | Etiqueta: country | Atributs: {name="Singapore"} || Elements: year 2011 gdppc 59900
# Entry: | Etiqueta: country | Atributs: {name="Panama"} || Elements: year 2011 gdppc 13600
```

### Cerca d'elements
La classe `Element` presenta mètodes útils per cercar elements de manera iterativa sobre tots els nivells inferiors de l’estructura d’arbre (els fills, els fills dels fills, etc.). Aquests mètodes es basen en la cerca de nodes a partir de les etiquetes (*tag*) i la iteració sobre tots els fills que en pengen.

El mètode `find()` cerca el primer node amb una etiqueta concreta. Els seus subelements es recorren, llavors, seqüencialment.
```python
for child in root.find('country'):
  print(child.tag)

# year
# gdppc
```

El mètode `findall()` cerca tots els element que contenen l’etiqueta especificada. El resultat és una col·lecció de tots els nodes d’aquella etiqueta, de manera que si es recorre cada element només s’obté una llista d’etiquetes.
```python
for child in root.findall('country'):
  print(child.tag)

# country
# country
# country
```

Per accedir als subnivells de cada node, cal afegir una segona iteració.
```python
for country in root.findall('country'):
  print(country.attrib)
  
  for child in country:
    print(child.tag, child.text)

# {'name': 'Liechtenstein'}
# year 2008
# gdppc 141100
# {'name': 'Singapore'}
# year 2011
# gdppc 59900
# {'name': 'Panama'}
# year 2011
# gdppc 13600
```

També es poden combinar els dos mètodes per a fer cerques més concretes dins de cada node.
```python
for country in root.findall('country'):
  year = country.find('year')
  print(country.attrib['name'], year.text)

# Liechtenstein 2008
# Singapore 2011
# Panama 2011
```

Els dos mètodes cerquen elements que pengin directament del node sobre el qual s’aplica el mètode. El mètode `iter()` treballa sobre tots els nivells de l’estructura i també itera sobre el propi node.
```python
for child in root.iter('year'):
  print(child.text)

# 2008
# 2011
# 2011
```

El mètode `get()` actua igual que en diccionaris Python i dona accés al valor que es correspon amb la clau indicada.
```python
for child in root.findall('country'):
  gdppc = child.find('gdppc').text
  name = child.get('name')
 
  print(name, gdppc)

# Liechtenstein 141100
# Singapore 59900
# Panama 13600
```

### Namespaces
Els *namespaces* son un sistema d’etiquetatge de la forma `prefix:sometag` que permeten un major grau de discretització de la informació en un document XML.

Els *namespaces* es defineixen amb `xmlns` i un URI identificador.
```xml
<actors xmlns="http://people.example.com">
```

D’aquesta manera, tots els fills conservaran l’URI com a prefix de la seva etiqueta, de la forma `{namespace}tag`.

Així, en l’arxiu XML següent d’exemple totes les etiquetes dels nodes conserven l’URI com a prefix.
```xml 
<?xml version="1.0"?>
<data xmlns="http://example.com">
	<country name="Liechtenstein">
		<year>2008</year>
		<gdppc>141100</gdppc>
	</country>
  <country name="Singapore">
		<year>2011</year>
		<gdppc>59900</gdppc>
	</country>
	<country name="Panama">
		<year>2011</year>
		<gdppc>13600</gdppc>
	</country>
</data>
```

I quan es visualitzen les etiquetes (*tag*) apareixen els URI davant.
```python
for child in root:
	print(child.tag)

# {http://example.com}country
# {http://example.com}country
# {http://example.com}country
```

Això fa que quan es vulgui buscar un node concret s’hagi d’incorporar l’URI davant de la pròpia etiqueta.
```python
for country in root.findall('{http://exemple.com}country'):
  year = country.find('{http://exemple.com}year')
  gdppc = country.find('{http://exemple.com}gdppc')
  name = country.get('name')
  print(name, year.text, gdppc.text)

# Liechtenstein 2008 141100
# Singapore 2011 59900
# Panama 2011 13600
```

Els *namespaces* no estan obligats a declarar-se a l’arrel del document; Es poden declarar en qualsevol element, però afectaran a aquell nivell i els seus descendents, fins que una altra declaració els redefineixi.

Com es pot comprovar, els *namespace* s’apliquen sobre els elements, però no sobre els atributs.

Un *namespace* pot anar acompanyat d’un prefix identificador.
```xml
<data xmlns:paisos="http://example.com">
```

Aquest cas s’utilitza habitualment quan s’ha de declarar més d’un *namespace* i, així, el prefix permet identificar quin element pertany a cada *namespace*. Així, seguint amb l’exemple, utilitzant dos prefixos associats a dues URI diferents s’aconsegueix diferenciar els elements segons si pertanyen a un grup o un altre.
```xml
<?xml version="1.0"?>
<data xmlns:europa="http://example_europa.com"
      xmlns:mon="http://example_mon.com">
	<europa:country name="Liechtenstein">
		<year>2008</year>
		<gdppc>141100</gdppc>
	</europa:country>
  <mon:country name="Singapore">
		<year>2011</year>
		<gdppc>59900</gdppc>
	</mon:country>
	<mon:country name="Panama">
		<year>2011</year>
		<gdppc>13600</gdppc>
	</mon:country>
</data>
```

Una manera més efectiva de cercar informació amb *namespaces* és crear prèviament un diccionari de prefixos. D’aquesta manera, s’evita escriure constantment les URI completes dins de funcions com `find` o `findall`.
```python
ns = {'ex': 'http://exemple.com'}

for country in root.findall('ex:country', ns):
  year = country.find('ex:year', ns)
  gdppc = country.find('ex:gdppc', ns)
  name = country.get('name')
 
  print(name, year.text, gdppc.text)

# Liechtenstein 2008 141100
# Singapore 2011 59900
# Panama 2011 13600
```

`ElementTree` substitueix internament els prefixos del diccionari pels *namespaces* corresponents. Els prefixos son propis, i no cal que coincideixin amb els prefixos originals del document XML, si n’hi ha.
