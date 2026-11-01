# 1. Objectius del projecte
## Objectiu principal
Desenvolupar un petit flux de treball automatitzat que permeti analitzar i comparar la distribució espacial de l'activitat industrial a Barcelona.

## Preguntes que volem respondre:
1. Com es distribueix territorialment l'activitat industrial?
2. Quins sectors concentren més edificis industrials?
3. La distribució canvia si mesurem el nombre d'edificis o la superfície construïda?
4. Quines zones presenten una concentració industrial especialment elevada?

Això ens permetrà treballar amb dues variables complementàries: nombre d'edificis i superfície construïda.

## Objectius d'aprenentatge
- Aprendre a treballar amb dades vectorials mitjançant GeoPandas.
- Manipular, filtrar i transformar dades geogràfiques amb Python.
- Realitzar operacions d'anàlisi espacial.
- Automatitzar processos que anteriorment fèiem amb QGIS/PyQGIS.
- Generar resultats cartogràfics i estadístics.
- Documentar i publicar un projecte reproduïble a GitHub.

# 2. Eines i tecnologies
Aquest serà el nostre entorn:

Eina - Funció
Python - Llenguatge principal
VS Code - Entorn de desenvolupament
GeoPandas - Manipulació de dades geogràfiques
Shapely - Operacions geomètriques
Pandas - Tractament de dades tabulars
Matplotlib - Gràfics i visualitzacions
(Jupyter Notebook - Exploració i anàlisi)
QGIS - Revisió i cartografia final
Git / GitHub - Control de versions i publicació

# 3. Dades de partida
Recuperarem les dades del projecte industrial que ja tens.

Dades - Utilització
Edificis del cadastre - Unitat d'anàlisi
Classificació d'edificis - Identificar els industrials
Límits de barris - Anàlisi territorial
Límits de districtes - Context administratiu
Graella hexagonal - Anàlisi de concentració

Treballarem principalment amb GeoPackage (.gpkg), perquè és un format pràctic i interoperable.

No incorporarem fonts noves innecessàriament. Primer aprofitarem les dades que ja tenim.

# 4. Desenvolupament del projecte
Proposta en quatre fases.

## FASE 1. Preparació de l'entorn i exploració de dades
Primera prioritat
 - Crear un entorn virtual Python.
 - Instal·lar les llibreries necessàries.
 - Connectar VS Code amb l'entorn.
 - Llegir els GeoPackage amb GeoPandas.
 - Inspeccionar camps, tipus de dades, geometries i sistemes de coordenades.
 - Comprovar la qualitat de les dades.

Resultat: entorn preparat i dades carregades correctament.

## FASE 2. Processament i anàlisi espacial
 - Seleccionar els edificis industrials.
 - Comprovar i tractar geometries.
 - Calcular superfície construïda.
 - Assignar edificis als barris.
 - Intersectar amb la graella hexagonal.
 - Calcular nombre d'edificis i superfície industrial per unitat territorial.

Resultat: GeoPackage amb les capes i variables d'anàlisi.

# FASE 3. Anàlisi i visualització
 - Comparar concentració per nombre d'edificis i superfície.
 - Identificar les zones més destacades.
 - Calcular estadístiques descriptives.
 - Elaborar dues o tres visualitzacions.
 - Preparar la cartografia final amb QGIS.

Resultat: resultats estadístics i cartogràfics.

# FASE 4. Automatització i publicació
 - Organitzar el codi en scripts reutilitzables.
 - Permetre executar el procés de principi a fi.
 - Preparar el README.
 - Documentar les fonts, metodologia i limitacions.
 - Publicar el repositori a GitHub.

Resultat: projecte reproduïble i presentable en un portafolis.

# 5. Estructura del repositori
El projecte s'estructura en les següents carpetes:

dades/: dades originals i processades.
scripts/: codi i processos d'anàlisi.
projectes_qgis/: projectes de treball de QGIS.
resultats/: mapes i figures generats.
documentacio/: metodologia i documentació tècnica.

# 6. Documents i resultats finals
El projecte haurà de tenir quatre elements:

README.md : Presentació, objectius, dades i instruccions d'execució
metodologia.md : Procés d'anàlisi, decisions i limitacions
Scripts Python : Codi reproduïble i organitzat
Cartografia final : 2–3 mapes o visualitzacions de resultats

També conservarem el GeoPackage resultant, però no necessàriament pujarem totes les dades originals al repositori: dependrà de les condicions de reutilització i del volum.

# 7. Estat del projecte
En desenvolupament.

# 8. Autoria
Projecte personal de creixement professional.