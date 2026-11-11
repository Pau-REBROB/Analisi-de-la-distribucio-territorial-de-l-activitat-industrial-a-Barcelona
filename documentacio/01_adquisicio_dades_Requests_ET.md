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

#### Petició al cadastre
En el cas d'aquest projecte, es realitza una petició `get` al cadastre espanyol dels edificis (*buildings*) en format XML a través de la seva URL.
```python
url = "https://www.catastro.hacienda.gob.es/INSPIRE/buildings/ES.SDGC.BU.atom.xml"

response = requests.get(url)
response.raise_for_status()

print("Codi de resposta:", response.status_code)
print("Mida del catàleg:", len(response.content), "bytes")

# Codi de resposta: 200
# Mida del catàleg: 682565 bytes
```

El codi de resposta `200` indica que la petició s'ha realitzat correctament.

El resultat de la petició s'emmagatzema en la variable `response`, un objecte de tipus `Response`.

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
```XML
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
```XML
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
```XML
<actors xmlns="http://people.example.com">
```

D’aquesta manera, tots els fills conservaran l’URI com a prefix de la seva etiqueta, de la forma `{namespace}tag`.

Així, en l’arxiu XML següent d’exemple totes les etiquetes dels nodes conserven l’URI com a prefix.
```XML 
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
```XML
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

#### XML del cadastre
En el cas d'aquest projecte, es crea primerament un objecte arrel del contingut de la resposta HTTP.
```python
root = ET.fromstring(response.content)
```

Els arxius XML del cadastre estan codificats segons l'estàndard **ATOM**.
```python
ET.indent(root)
ET.dump(root)
```

L'arrel de l'arxiu XML està etiquetat com a *feed*. L'arxiu presenta una primera capçalera informativa: títol, subtítol, links, drets d'autor, última actualització i autor.
```python
<ns0:feed xmlns:html="http://www.w3.org/1999/xhtml" xmlns:ns0="http://www.w3.org/2005/Atom" xmlns:ns1="http://www.georss.org/georss" xml:lang="en">
  <ns0:title>Download service of Buildings. Territorial Office</ns0:title>
  <ns0:subtitle>INSPIRE Buildings. Spanish Directorate General for Cadastre </ns0:subtitle>
  <ns0:link href="http://www.catastro.hacienda.gob.es/INSPIRE/buildings/ES.SDGC.bu.DownloadServ.MD.xml" rel="describedby" type="application/vnd.iso.19139+xml" />
  <ns0:link href="http://www.catastro.hacienda.gob.es/INSPIRE/buildings/ES.SDGC.BU.atom.xml" rel="self" type="application/atom+xml" hreflang="en" title="This document" />
  <ns0:link href="http://www.catastro.hacienda.gob.es/" rel="alternate" type="text/html" hreflang="en" title="Cadastral web page" />
  <ns0:id>http://www.catastro.hacienda.gob.es/INSPIRE/buildings/ES.SDGC.BU.atom.xml</ns0:id>
  <ns0:rights>Copyright (c) 2012", ES.SDGC; all rights reserved</ns0:rights>
  <ns0:updated>2026-08-21T00:00:00Z</ns0:updated>
  <ns0:author>
    <ns0:name>productosyservicios</ns0:name>
    <ns0:email>productosyservicios@catastro.hacienda.gob.es</ns0:email>
  </ns0:author>
  ```
Després de la capçalera venen les entrades (*entry*) com a fills directes de l'arrel, amb informació del cadastre de cada província.
```python
# Província d'Albacete
<ns0:entry>
    <ns0:title>Territorial office 02 Albacete</ns0:title>
    <ns0:link rel="enclosure" href="http://www.catastro.hacienda.gob.es/INSPIRE/buildings/02/ES.SDGC.bu.atom_02.xml" type="application/atom+xml" />
    <ns0:id>http://www.catastro.hacienda.gob.es/INSPIRE/buildings/02/ES.SDGC.bu.atom_02.xml</ns0:id>
    <ns0:rights>Copyright (c) 2012", ES.SDGC; all rights reserved</ns0:rights>
    <ns0:updated>2026-08-21T00:00:00Z</ns0:updated>
    <ns1:polygon> 38.022	-2.883	38.022	-0.916	39.423	-0.916	39.423	-2.883	38.022	-2.883</ns1:polygon>
    <ns0:category term="Buildings" scheme="http://inspire-registry.jrc.ec.europa.eu/registers/FCD/items/160" label="Buildings" xml:lang="en" />
    <ns0:content type="xhtml" xml:lang="sp">
      <html:div>
		  Buildings by municipalities:
		  <html:br />
        <html:font color="#000000" size="2" face="Verdana, Arial, Helvetica, sans-serif">
          <html:strong>Municipalities </html:strong>
        </html:font>
        <html:br />
        <html:div id="scrolltable" style="border-right: #6699CC 1px solid; border-top: #6699CC 1px solid; border-left: #6699CC 1px solid; border-bottom: #6699CC 1px solid; overflow:auto;  height:100px "> 
		
02001-ABENGIBRE                                      <html:br />
02002-ALATOZ                                         <html:br />
02004-ALBATANA                                       <html:br />
02005-ALBOREA                                        <html:br />
02006-ALCADOZO                                       <html:br />
02007-ALCALA DEL JUCAR                               <html:br />
02008-ALCARAZ                                        <html:br />
02009-ALMANSA                                        <html:br />
02010-ALPERA                                         <html:br />
02011-AYNA                                           <html:br />
02012-BALAZOTE                                       <html:br />
02013-BALSA DE VES                                   <html:br />
02014-EL BALLESTERO                                  <html:br />
02015-BARRAX                                         <html:br />
02016-BIENSERVIDA                                    <html:br />
02017-BOGARRA                                        <html:br />
02018-BONETE                                         <html:br />
02019-EL BONILLO                                     <html:br />
02020-CARCELEN                                       <html:br />
02021-CASAS DE JUAN NUÑEZ                            <html:br />
02022-CASAS DE LAZARO                                <html:br />
02023-CASAS DE VES                                   <html:br />
02024-CASAS IBAÑEZ                                   <html:br />
02025-CAUDETE                                        <html:br />
02026-CENIZATE                                       <html:br />
02027-CORRAL-RUBIO                                   <html:br />
02028-COTILLAS                                       <html:br />
02029-CHINCHILLA DE MONTE-ARAGON                     <html:br />
02030-ELCHE DE LA SIERRA                             <html:br />
02031-FEREZ                                          <html:br />
02032-FUENSANTA                                      <html:br />
02033-FUENTE-ALAMO                                   <html:br />
02034-FUENTEALBILLA                                  <html:br />
02035-LA GINETA                                      <html:br />
02036-GOLOSALVO                                      <html:br />
02037-HELLIN                                         <html:br />
02038-LA HERRERA                                     <html:br />
02039-HIGUERUELA                                     <html:br />
02040-HOYA-GONZALO                                   <html:br />
02041-JORQUERA                                       <html:br />
02042-LETUR                                          <html:br />
02043-LEZUZA                                         <html:br />
02044-LIETOR                                         <html:br />
02045-MADRIGUERAS                                    <html:br />
02046-MAHORA                                         <html:br />
02047-MASEGOSO                                       <html:br />
02048-MINAYA                                         <html:br />
02049-MOLINICOS                                      <html:br />
02050-MONTALVOS                                      <html:br />
02051-MONTEALEGRE DEL CASTILLO                       <html:br />
02052-MOTILLEJA                                      <html:br />
02053-MUNERA                                         <html:br />
02054-NAVAS DE JORQUERA                              <html:br />
02055-NERPIO                                         <html:br />
02056-ONTUR                                          <html:br />
02057-OSSA DE MONTIEL                                <html:br />
02058-PATERNA DEL MADERA                             <html:br />
02059-PEÑASCOSA                                      <html:br />
02060-PEÑAS DE SAN PEDRO                             <html:br />
02061-PETROLA                                        <html:br />
02062-POVEDILLA                                      <html:br />
02063-POZOHONDO                                      <html:br />
02064-POZO-LORENTE                                   <html:br />
02065-POZUELO                                        <html:br />
02066-LA RECUEJA                                     <html:br />
02067-RIOPAR                                         <html:br />
02068-ROBLEDO                                        <html:br />
02069-LA RODA                                        <html:br />
02070-SALOBRE                                        <html:br />
02071-SAN PEDRO                                      <html:br />
02072-SOCOVOS                                        <html:br />
02073-TARAZONA DE LA MANCHA                          <html:br />
02074-TOBARRA                                        <html:br />
02075-VALDEGANGA                                     <html:br />
02076-VIANOS                                         <html:br />
02077-VILLA DE VES                                   <html:br />
02078-VILLALGORDO DEL JUCAR                          <html:br />
02079-VILLAMALEA                                     <html:br />
02080-VILLAPALACIOS                                  <html:br />
02081-VILLARROBLEDO                                  <html:br />
02082-VILLATOYA                                      <html:br />
02083-VILLAVALIENTE                                  <html:br />
02084-VILLAVERDE DE GUADALIMAR                       <html:br />
02085-VIVEROS                                        <html:br />
02086-YESTE                                          <html:br />
02087-POZO CAÑADA                                    <html:br />
02900-ALBACETE                                       <html:br />
        </html:div>
      </html:div>
    </ns0:content>
  </ns0:entry>
```

De cada *entry* pengen els elements *title*, *link*, *id*, *rights*, *updated*, *polygon*, *category*, *content*.

En el cas que ocupa aquest projecte, caldrà treballar amb les dades de la província de Barcelona.

A la mateixa arrel apareixen fins a tres *namespace* diferents:
- xmlns:html="http://www.w3.org/1999/xhtml"
- xmlns:ns0="http://www.w3.org/2005/Atom"
- xmlns:ns1="http://www.georss.org/georss"
  
El prefix present a les entrades provincials és el que es correspon amb el servei ATOM (*"http://www.w3.org/2005/Atom"*). Es crea un diccionari de *namespace* per agilitzar l'extracció d'informació.
```python
ns = {"atom": "http://www.w3.org/2005/Atom"}
```

Per començar a treballar, es crea una variable amb totes les entrades (*entry*) del cadastre.
```python
entries = root.findall("atom:entry", ns)

print("Nombre total d'entrades:", len(entries))

# Nombre total d'entrades: 56
```

Es fa una iteració per cada entrada (per cada província) per trobar aquella que es correspon amb Barcelona.
```python
barcelona_url = None

for entry in entries:
    title = entry.findtext("atom:title", default="", namespaces=ns)

    if "Barcelona" in title:
      print("\nEntrada trobada:")
      print("Títol:", title)

# Entrada trobada:
# Títol: Territorial office 08 Barcelona
```

L'accés al catàleg provincial és, de nou, a través d'un XML proporcionat com a URL. Aquesta direcció es troba a l'element *link*, del tipus *<ns0:link rel="enclosure" href="http://www.catastro.hacienda.gob.es/INSPIRE/buildings/02/ES.SDGC.bu.atom_02.xml" type="application/atom+xml" />*. L'element *link* presenta dos atributs: *rel* i *href*.

Es realitza una segona cerca en el nivell inferior a l'entrada. Es filtra per *rel* com a *enclosure* i es recupera l'URL que es correspon amb l'atribut *href*.
```python
barcelona_url = None

for entry in entries:
  title = entry.findtext("atom:title", default="", namespaces=ns)

  if "Barcelona" in title:
    print("\nEntrada trobada:")
    print("Títol:", title)

    for link in entry.findall("atom:link", ns):
      if link.attrib.get("rel") == "enclosure":
        barcelona_url = link.attrib["href"]
        print("Província:", title)
        print("Enllaç:", barcelona_url)

        break

# Província: Territorial office 08 Barcelona
# Enllaç: http://www.catastro.hacienda.gob.es/INSPIRE/buildings/08/ES.SDGC.bu.atom_08.xml
```

Un cop extreta l'adreça del catàleg provincial, es genera una nova petició `get` i es crea una nova arrel del `response.content`.
```python
response_bcn = requests.get(barcelona_url)
response_bcn.raise_for_status()

print("Codi de resposta:", response_bcn.status_code)
print("Mida del catàleg:", len(response_bcn.content), "bytes")

root_bcn = ET.fromstring(response_bcn.content)

# Codi de resposta: 200
# Mida del catàleg: 543609 bytes
```

El catàleg provincial és similar al genèric. Conté la mateixa capçalera descriptiva introductòria i els mateixos *namespaces*.
```XML
<ns0:feed xmlns:html="http://www.w3.org/1999/xhtml" xmlns:ns0="http://www.w3.org/2005/Atom" xmlns:ns1="http://inspire.ec.europa.eu/schemas/inspire_dls/1.0" xmlns:ns3="http://www.georss.org/georss" xml:lang="en">
  <ns0:title>Download service of Building. Territorial Office 08 - Barcelona</ns0:title>
  <ns0:subtitle>INSPIRE Buildings. Spanish Directorate General for Cadastre</ns0:subtitle>
  <ns0:link href="http://www.catastro.hacienda.gob.es/INSPIRE/Building/ES.SDGC.BU.DownloadServ.MD.xml" rel="describedby" type="application/vnd.iso.19139+xml" />
  <ns0:link href="http://www.catastro.hacienda.gob.es/INSPIRE/Building/08/ES.SDGC.BU.atom_08.xml" rel="self" type="application/atom+xml" hreflang="en" title="This document" />
  <ns0:link href="http://www.catastro.hacienda.gob.es/" rel="alternate" type="text/html" hreflang="en" title="Cadastral web page" />
  <ns0:id>http://www.catastro.hacienda.gob.es/INSPIRE/Building/08/ES.SDGC.BU.atom_08.xml</ns0:id>
  <ns0:rights>This service can be used free of charge in every instance,  as long as that the D. G. of the Cadastre (Ministry of Finance)  is mentioned as author and owner of the information</ns0:rights>
  <ns0:updated>2026-08-21T00:00:00Z</ns0:updated>
  <ns0:author>
    <ns0:name>productosyservicios</ns0:name>
    <ns0:email>productosyservicios@catastro.hacienda.gob.es</ns0:email>
  </ns0:author>
```

Seguidament venen les entrades (*entry*) amb la informació del cadastre de cada municipi.
```XML
<ns0:entry>
    <ns0:title> 08001-ABRERA buildings</ns0:title>
    <ns1:spatial_dataset_identifier_code>BU</ns1:spatial_dataset_identifier_code>
    <ns1:spatial_dataset_identifier_namespace>SDGC</ns1:spatial_dataset_identifier_namespace>
    <ns0:content type="xhtml" xml:lang="en">
      <html:div>
		  </html:div>
    </ns0:content>
    <ns0:link rel="enclosure" href="https://www.catastro.hacienda.gob.es/INSPIRE/Buildings/08/08001-ABRERA/A.ES.SDGC.BU.08001.zip" type="application/atom+xml" hreflang="en" />
    <ns0:id>https://www.catastro.hacienda.gob.es/INSPIRE/Buildings/08/08001-ABRERA/A.ES.SDGC.BU.08001.zip</ns0:id>
    <ns0:rights>This service can be used free of charge in every instance,  as long as that the D. G. of the Cadastre (Ministry of Finance)  is mentioned as author and owner of the information</ns0:rights>
    <ns0:updated>2026-08-21T00:00:00Z</ns0:updated>
    <ns3:polygon>41.49491008748271 1.8626930856981512 41.49491008748271 1.9484048041773987 41.53681609005559 1.9484048041773987 41.53681609005559 1.8626930856981512 41.49491008748271 1.8626930856981512</ns3:polygon>
    <ns0:category term="http://www.opengis.net/def/crs/EPSG/0/25831" label="ETRS89" />
  </ns0:entry>
```

La informació geogràfica es troba en l'element *link*, que ofereix un enllaç de descàrrega d'un arxiu comprimit ZIP: *<ns0:link rel="enclosure" href="https://www.catastro.hacienda.gob.es/INSPIRE/Buildings/08/08001-ABRERA/A.ES.SDGC.BU.08001.zip" type="application/atom+xml" hreflang="en" />*.

EXPLICACIÓ DEL NEXT

```python
print("Nombre total d'entrades BCN:", len(entries_bcn))

registres_cataleg = []

for entry in entries_bcn:
  titol = entry.findtext("atom:title", default="", namespaces=ns)

  enllaç = None

  for link in entry.findall("atom:link", ns):
      if link.get("rel") == "enclosure":
          enllaç = link
          break

  registres_cataleg.append({
      "titol_cataleg": titol,
      "href_original": enllaç.get("href") if enllaç is not None else None,
      "tipus": enllaç.get("type") if enllaç is not None else None
  })

df_cataleg = pd.DataFrame(registres_cataleg)

print("Entrades del catàleg:", len(df_cataleg))

# Nombre total d'entrades BCN: 311
# Entrades del catàleg: 311
```

El resultat és un dataframe de totes les entrades del catàleg, amb el títol (el nom del municpi) i l'adreça que permet descarregar l'arxiu de les geometries dels edificis.
