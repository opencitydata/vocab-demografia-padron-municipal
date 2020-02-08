## Ejemplos de consultas sobre los cubos de datos de padrón

A continuación, utilizando como referencia los cubos generados a partir del raw data del padrón municipal se presentan algunos ejemplos de consultas.

El raw data del padrón municipal (disponible en el grafo:http://ciudadesabiertas.linkeddata.es/padron-municipal), generado a partir de un extracto del conjunto de datos de prueba del INE que se corresponde con los ficheros del registro de habitantes en diferentes Ayuntamientos, ha sido filtrado por dos distritos de Madrid (Latina y Arganzuela) y es una muestra reducida de valores, por lo tanto los resultados provienen de pocos registros.

Los grafos de cada cubo se encuentran disponibles en el siguiente SPARQL endpoint: http://ciudadesabiertas.linkeddata.es/sparql. Para cada ejemplo se ha considerado como periodo de referencia el año 2019.

### 1. Cubo de datos según edad

El grafo con los datos de este cubo es: http://ciudadesabiertas.linkeddata.es/cubo-datos/poblacion-por-edad. Para la generación de este grafo se ha ejecutado la siguiente query:

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX qb: <http://purl.org/linked-data/cube#>

CONSTRUCT {
  ?uriObs a qb:Observation .
  ?uriObs qb:dataSet <http://vocab.ciudadesabiertas.es/recurso/demografia/padron-municipal/DS_PoblacionPorEdad> .
  ?uriObs sdmx-dimension:refArea ?distritoResidencia .
  ?uriObs sdmx-dimension:refPeriod <http://reference.data.gov.uk/id/year/2019> .
  ?uriObs sdmx-dimension:sexo ?genderKOS .
  ?uriObs iaest-dimension:edad-grupos-quinquenales ?grupoQuinquenal .
  ?uriObs espad-medida:numero-personas ?numHabitantes .
}
WHERE
{
SELECT DISTINCT ?uriObs ?distritoResidencia ?genderKOS ?grupoQuinquenal (COUNT(DISTINCT ?x) AS ?numHabitantes) WHERE {
  ?x a espad:Habitante .
  ?x espad:activo ?activo .
  ?x espad:distritoResidencia ?distritoResidencia .
  ?x schema:birthDay ?birthDay .
  ?x schema:gender ?gender .
  FILTER(?activo)
  BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
  BIND(IRI(
  IF(?age <= 4, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/00-a-04",
    IF(?age <= 9, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/05-a-09",
      IF(?age <= 14, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/10-a-14",
        IF(?age <= 19, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/15-a-19",
          IF(?age <= 24, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/20-a-24",
            IF(?age <= 29, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/25-a-29",
              IF(?age <= 34, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/30-a-34",
                IF(?age <= 39, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/35-a-39",
                  IF(?age <= 44, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/40-a-44",
                    IF(?age <= 49, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/45-a-49",
                      IF(?age <= 54, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/50-a-54",
                        IF(?age <= 59, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/55-a-59",
                          IF(?age <= 64 , "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/60-a-64",
                            IF(?age <= 69, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/65-a-69",
                              IF(?age <= 74, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/70-a-74",
                                IF(?age <= 79, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/75-a-79",
                                  IF(?age <= 84, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/80-a-84",
                                    IF(?age <= 89, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/85-a-89",
                                      IF(?age <= 94, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/90-a-94",
                                        "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/95-y-mas"))))))))))))))))))) AS ?grupoQuinquenal)
   BIND(IRI(CONCAT("http://vocab.ciudadesabiertas.es/recurso/demografia/padron-municipal/",md5(xsd:string(CONCAT(?distritoResidencia,?gender,?grupoQuinquenal))))) AS ?uriObs)
   BIND(IF(?gender = schema:Male, <http://purl.org/linked-data/sdmx/2009/code#sex-M>, <http://purl.org/linked-data/sdmx/2009/code#sex-F>) AS ?genderKOS)
 }
}

```
#### Ejemplos

a) En esta [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Fpoblacion-por-edad&query=PREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0APREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+sdmx-kos%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fcode%23%3E%0D%0A%0D%0ASELECT+%3Fsexo+%28sum%28%3Fx%29+as+%3FnumeroTotalPoblacion%29%0D%0AWHERE+%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefArea+%3Chttp%3A%2F%2Fvocab.linkeddata.es%2Frecurso%2Fterritorio%2Fdistrito%2F79602%3E+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+espad-medida%3Anumero-personas+%3Fx.%0D%0A++%3FuriObs+sdmx-dimension%3Asexo+%3Fsexo.%0D%0A+%7D&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene población total según área y sexo. Concretamente la población total del distrito Arganzuela según sexo.
```
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX sdmx-kos: <http://purl.org/linked-data/sdmx/2009/code#>

SELECT ?sexo (sum(?x) as ?numeroTotalPoblacion)
WHERE {
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refArea <http://vocab.linkeddata.es/recurso/territorio/distrito/79602> .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs espad-medida:numero-personas ?x.
  ?uriObs sdmx-dimension:sexo ?sexo.
 }

```

b) En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Fpoblacion-por-edad&query=PREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0APREFIX+iaest-dimension%3A+%3Chttp%3A%2F%2Fopendata.aragon.es%2Fdef%2Fiaest%2Fdimension%23%3E%0D%0APREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+sdmx-kos%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fcode%23%3E%0D%0A%0D%0ASELECT+sum%28%3Fx%29+as+%3FtotalPoblacion+WHERE+%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefArea+%3Chttp%3A%2F%2Fvocab.linkeddata.es%2Frecurso%2Fterritorio%2Fdistrito%2F79610%3E+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+sdmx-dimension%3Asexo+sdmx-kos%3Asex-M+.%0D%0A++%3FuriObs+iaest-dimension%3Aedad-grupos-quinquenales+%3FgruposQuinquenales+.%0D%0A++FILTER+%28%3FgruposQuinquenales+%3D+%3Chttps%3A%2F%2Fopendata.aragon.es%2Fkos%2Fiaest%2Fedad-grupos-quinquenales%2F00-a-04%3E+%7C%7C+%3FgruposQuinquenales+%3D+%3Chttps%3A%2F%2Fopendata.aragon.es%2Fkos%2Fiaest%2Fedad-grupos-quinquenales%2F05-a-09%3E+%7C%7C+%3FgruposQuinquenales+%3D+%3Chttps%3A%2F%2Fopendata.aragon.es%2Fkos%2Fiaest%2Fedad-grupos-quinquenales%2F10-a-14%3E%29%0D%0A++%3FuriObs+espad-medida%3Anumero-personas+%3Fx+.%0D%0A%7D&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene la población total según sexo, rango de edad, y área. Concretamente la población de sexo masculino de entre 0 y 14 años que habita en el distrito Latina.

```
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX sdmx-kos: <http://purl.org/linked-data/sdmx/2009/code#>

SELECT sum(?x) as ?totalPoblacion WHERE {
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refArea <http://vocab.linkeddata.es/recurso/territorio/distrito/79610> .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs sdmx-dimension:sexo sdmx-kos:sex-M .
  ?uriObs iaest-dimension:edad-grupos-quinquenales ?gruposQuinquenales .
  FILTER (?gruposQuinquenales = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/00-a-04> || ?gruposQuinquenales = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/05-a-09> || ?gruposQuinquenales = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/10-a-14>)
  ?uriObs espad-medida:numero-personas ?x .
}
```
c) En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Fpoblacion-por-edad&query=PREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0APREFIX+iaest-dimension%3A+%3Chttp%3A%2F%2Fopendata.aragon.es%2Fdef%2Fiaest%2Fdimension%23%3E%0D%0APREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+sdmx-kos%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fcode%23%3E%0D%0A%0D%0ASELECT+%3FgruposQuinquenales+%2C+%28sum%28%3Fx%29+as+%3FtotalPoblacion%29+WHERE+%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefArea+%3Chttp%3A%2F%2Fvocab.linkeddata.es%2Frecurso%2Fterritorio%2Fdistrito%2F79610%3E+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+sdmx-dimension%3Asexo+sdmx-kos%3Asex-M+.%0D%0A++%3FuriObs+iaest-dimension%3Aedad-grupos-quinquenales+%3FgruposQuinquenales+.%0D%0A++%3FuriObs+espad-medida%3Anumero-personas+%3Fx+.%0D%0A%7D%0D%0AORDER+BY+ASC+%28%3FgruposQuinquenales%29&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el total de población según rangos de edad de cierta área. Concretamente la población total según rangos de edad del distrito Latina.

```
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX sdmx-kos: <http://purl.org/linked-data/sdmx/2009/code#>

SELECT ?gruposQuinquenales , (sum(?x) as ?totalPoblacion) WHERE {
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refArea <http://vocab.linkeddata.es/recurso/territorio/distrito/79610> .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs sdmx-dimension:sexo sdmx-kos:sex-M .
  ?uriObs iaest-dimension:edad-grupos-quinquenales ?gruposQuinquenales .
  ?uriObs espad-medida:numero-personas ?x .
}
ORDER BY ASC (?gruposQuinquenales)
```

### 2. Cubo de datos según nacionalidad

El grafo con los datos de este cubo es: http://ciudadesabiertas.linkeddata.es/cubo-datos/poblacion-por-nacionalidad. Para la generación del grafo se ha ejecutado la siguiente query:

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>
PREFIX dcterms: <http://purl.org/dc/terms/>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX qb: <http://purl.org/linked-data/cube#>

CONSTRUCT {
  ?uriObs a qb:Observation .
  ?uriObs qb:dataSet <http://vocab.ciudadesabiertas.es/recurso/demografia/padron-municipal/DS_PoblacionPorNacionalidad> .
  ?uriObs sdmx-dimension:refArea ?distritoResidencia .
  ?uriObs sdmx-dimension:refPeriod <http://reference.data.gov.uk/id/year/2019> .
  ?uriObs sdmx-dimension:sexo ?genderKOS .
  ?uriObs iaest-dimension:edad-grupos-quinquenales ?grupoQuinquenal .
  ?uriObs iaest-dimension:nacionalidad ?nacionalidadSchema.
  ?uriObs espad-medida:numero-personas ?numPersonas .
}
WHERE
{
SELECT DISTINCT ?uriObs ?distritoResidencia ?genderKOS ?nacionalidadSchema ?grupoQuinquenal (COUNT(DISTINCT ?x) AS ?numPersonas)
WHERE {
  ?x a espad:Habitante .
  ?x espad:activo ?activo .
  ?x espad:distritoResidencia ?distritoResidencia .
  ?x schema:nationality ?nacionalidad .
  ?x schema:birthDay ?birthDay .
  ?x schema:gender ?gender .

  FILTER(?activo)
  BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
  BIND(IRI(
    IF(?age <= 4, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/00-a-04",
      IF(?age <= 9, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/05-a-09",
        IF(?age <= 14, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/10-a-14",
          IF(?age <= 19, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/15-a-19",
            IF(?age <= 24, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/20-a-24",
              IF(?age <= 29, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/25-a-29",
                IF(?age <= 34, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/30-a-34",
                  IF(?age <= 39, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/35-a-39",
                    IF(?age <= 44, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/40-a-44",
                      IF(?age <= 49, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/45-a-49",
                        IF(?age <= 54, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/50-a-54",
                          IF(?age <= 59, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/55-a-59",
                            IF(?age <= 64 , "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/60-a-64",
                              IF(?age <= 69, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/65-a-69",
                                IF(?age <= 74, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/70-a-74",
                                  IF(?age <= 79, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/75-a-79",
                                    IF(?age <= 84, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/80-a-84",
                                      IF(?age <= 89, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/85-a-89",
                                        IF(?age <= 94, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/90-a-94",
                                          "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/95-y-mas")))))))))))))))))))) AS ?grupoQuinquenal)
  BIND(IRI(CONCAT("http://vocab.ciudadesabiertas.es/recurso/demografia/padron-municipal/",md5(xsd:string(CONCAT(?distritoResidencia,?gender,?grupoQuinquenal,?nacionalidad))))) AS ?uriObs)
  BIND(IF(?gender = schema:Male, <http://purl.org/linked-data/sdmx/2009/code#sex-M>, <http://purl.org/linked-data/sdmx/2009/code#sex-F>) AS ?genderKOS)
  BIND(IRI(
    IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/108>, "https://opendata.aragon.es/kos/iaest/nacionalidad/espanoles",
      IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/126>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
        IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/102>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
          IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/103>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
              IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/104>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/106>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                  IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/146>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                    IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/107>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                      IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/144>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                        IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/147>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                          IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/141>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                            IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/109>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                              IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/110>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/111>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                  IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/112>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                    IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/113>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                      IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/115>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                        IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/136>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                          IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/142>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                            IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/117>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                              IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/118>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                                IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/121>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                                  IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/122>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                                    IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/123>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                                      IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/143>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                                        IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/128>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                                          IF(?nacionalidad = <https://vocab.linkeddata.es/recurso/territorio/pais/131>, "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27",
                                                            "https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-no-comunitarios")))))))))))))))))))))))))))) AS ?nacionalidadSchema)
  }
}

```
#### Ejemplos
a) En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Fpoblacion-por-nacionalidad&query=PREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0APREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+iaest-dimension%3A+%3Chttp%3A%2F%2Fopendata.aragon.es%2Fdef%2Fiaest%2Fdimension%23%3E%0D%0A%0D%0ASELECT+%28sum+%28%3Fx%29+AS+%3FnumeroExtranjeros%29+WHERE+%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+iaest-dimension%3Anacionalidad+%3Fnacionalidad.%0D%0A++FILTER+%28%3Fnacionalidad+%21%3D+%3Chttps%3A%2F%2Fopendata.aragon.es%2Fkos%2Fiaest%2Fnacionalidad%2Fespanoles%3E%29%0D%0A++%3FuriObs+espad-medida%3Anumero-personas+%3Fx+.%0D%0A%7D%0D%0A&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el total de población extranjera.

```
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>

SELECT (sum (?x) AS ?numeroExtranjeros) WHERE {
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs iaest-dimension:nacionalidad ?nacionalidad.
  FILTER (?nacionalidad != <https://opendata.aragon.es/kos/iaest/nacionalidad/espanoles>)
  ?uriObs espad-medida:numero-personas ?x .
}

```
b) En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Fpoblacion-por-nacionalidad&query=PREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0APREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+iaest-dimension%3A+%3Chttp%3A%2F%2Fopendata.aragon.es%2Fdef%2Fiaest%2Fdimension%23%3E%0D%0A%0D%0ASELECT+%28sum+%28%3Fx%29+AS+%3FnumeroEspanoles%29+WHERE+%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+iaest-dimension%3Anacionalidad+%3Fnacionalidad.%0D%0A++FILTER+%28%3Fnacionalidad+%3D+%3Chttps%3A%2F%2Fopendata.aragon.es%2Fkos%2Fiaest%2Fnacionalidad%2Fespanoles%3E%29%0D%0A++%3FuriObs+espad-medida%3Anumero-personas+%3Fx+.%0D%0A%7D&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el total de población española.

```
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>

SELECT (sum (?x) AS ?numeroEspanoles) WHERE {
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs iaest-dimension:nacionalidad ?nacionalidad.
  FILTER (?nacionalidad = <https://opendata.aragon.es/kos/iaest/nacionalidad/espanoles>)
  ?uriObs espad-medida:numero-personas ?x .
}

```
c) En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Fpoblacion-por-nacionalidad&query=PREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0APREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+iaest-dimension%3A+%3Chttp%3A%2F%2Fopendata.aragon.es%2Fdef%2Fiaest%2Fdimension%23%3E%0D%0A%0D%0ASELECT+%3Fsexo+%2C%28sum+%28%3Fx%29+AS+%3FnumeroExtranjeros%29+WHERE+%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+iaest-dimension%3Anacionalidad+%3Fnacionalidad.%0D%0A++FILTER+%28%3Fnacionalidad+%21%3D+%3Chttps%3A%2F%2Fopendata.aragon.es%2Fkos%2Fiaest%2Fnacionalidad%2Fespanoles%3E%29%0D%0A++%3FuriObs+espad-medida%3Anumero-personas+%3Fx+.%0D%0A++%3FuriObs+sdmx-dimension%3Asexo+%3Fsexo%0D%0A%7D&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el total de población extranjera según sexo.
```
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>

SELECT ?sexo ,(sum (?x) AS ?numeroExtranjeros) WHERE {
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs iaest-dimension:nacionalidad ?nacionalidad.
  FILTER (?nacionalidad != <https://opendata.aragon.es/kos/iaest/nacionalidad/espanoles>)
  ?uriObs espad-medida:numero-personas ?x .
  ?uriObs sdmx-dimension:sexo ?sexo
}
```
d) En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Fpoblacion-por-nacionalidad&query=PREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0APREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+iaest-dimension%3A+%3Chttp%3A%2F%2Fopendata.aragon.es%2Fdef%2Fiaest%2Fdimension%23%3E%0D%0A%0D%0ASELECT+%3Fsexo%2C%28sum+%28%3Fx%29+AS+%3FnumeroExtranjeros%29+WHERE+%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+iaest-dimension%3Anacionalidad+%3Fnacionalidad.%0D%0A%0D%0A++FILTER+%28%3Fnacionalidad+%21%3D+%3Chttps%3A%2F%2Fopendata.aragon.es%2Fkos%2Fiaest%2Fnacionalidad%2Fespanoles%3E%29%0D%0A++%3FuriObs+sdmx-dimension%3ArefArea+%3Chttp%3A%2F%2Fvocab.linkeddata.es%2Frecurso%2Fterritorio%2Fdistrito%2F79610%3E+.%0D%0A++%3FuriObs+iaest-dimension%3Aedad-grupos-quinquenales+%3FgruposQuinquenales.%0D%0A%0D%0A+++FILTER+%28%3FgruposQuinquenales+%3D+%3Chttps%3A%2F%2Fopendata.aragon.es%2Fkos%2Fiaest%2Fedad-grupos-quinquenales%2F00-a-04%3E+%7C%7C+%3FgruposQuinquenales+%3D+%3Chttps%3A%2F%2Fopendata.aragon.es%2Fkos%2Fiaest%2Fedad-grupos-quinquenales%2F05-a-09%3E+%7C%7C+%3FgruposQuinquenales+%3D+%3Chttps%3A%2F%2Fopendata.aragon.es%2Fkos%2Fiaest%2Fedad-grupos-quinquenales%2F10-a-14%3E%29%0D%0A++%3FuriObs+espad-medida%3Anumero-personas+%3Fx+.%0D%0A++%3FuriObs+sdmx-dimension%3Asexo+%3Fsexo%0D%0A%7D%0D%0A&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el total de población extranjera según sexo y rango de edad que habita en cierta área. Concretamente la población total de extranjeros según sexo en el rango de edad de 0 a 14 años que habita en el distrito Latina.

```
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>

SELECT ?sexo,(sum (?x) AS ?numeroExtranjeros) WHERE {
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs iaest-dimension:nacionalidad ?nacionalidad.

  FILTER (?nacionalidad != <https://opendata.aragon.es/kos/iaest/nacionalidad/espanoles>)
  ?uriObs sdmx-dimension:refArea <http://vocab.linkeddata.es/recurso/territorio/distrito/79610> .
  ?uriObs iaest-dimension:edad-grupos-quinquenales ?gruposQuinquenales.

   FILTER (?gruposQuinquenales = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/00-a-04> || ?gruposQuinquenales = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/05-a-09> || ?gruposQuinquenales = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/10-a-14>)
  ?uriObs espad-medida:numero-personas ?x .
  ?uriObs sdmx-dimension:sexo ?sexo
}

```
e) En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Fpoblacion-por-nacionalidad&query=PREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0APREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+iaest-dimension%3A+%3Chttp%3A%2F%2Fopendata.aragon.es%2Fdef%2Fiaest%2Fdimension%23%3E%0D%0A%0D%0ASELECT+%28sum+%28%3Fx%29+AS+%3FnumeroExtranjerosNoComunitarios%29+WHERE+%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+iaest-dimension%3Anacionalidad+%3Fnacionalidad.%0D%0A++FILTER+%28%3Fnacionalidad+%3D+%3Chttps%3A%2F%2Fopendata.aragon.es%2Fkos%2Fiaest%2Fnacionalidad%2Fextranjeros-no-comunitarios%3E%29%0D%0A++%3FuriObs+espad-medida%3Anumero-personas+%3Fx+.%0D%0A%7D%0D%0A&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el total de población extranjera no comunitaria.


```
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>

SELECT (sum (?x) AS ?numeroExtranjerosNoComunitarios) WHERE {
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs iaest-dimension:nacionalidad ?nacionalidad.
  FILTER (?nacionalidad = <https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-no-comunitarios>)
  ?uriObs espad-medida:numero-personas ?x .
}

```

f) En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Fpoblacion-por-nacionalidad&query=PREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0APREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+iaest-dimension%3A+%3Chttp%3A%2F%2Fopendata.aragon.es%2Fdef%2Fiaest%2Fdimension%23%3E%0D%0A%0D%0ASELECT+%28sum+%28%3Fx%29+AS+%3FnumeroExtranjerosComunitarios%29+WHERE+%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+iaest-dimension%3Anacionalidad+%3Fnacionalidad.%0D%0A++FILTER+%28%3Fnacionalidad+%3D+%3Chttps%3A%2F%2Fopendata.aragon.es%2Fkos%2Fiaest%2Fnacionalidad%2Fextranjeros-comunitarios-ue-27%3E%29%0D%0A++%3FuriObs+espad-medida%3Anumero-personas+%3Fx+.%0D%0A%7D&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el total de población extranjera comunitaria.

```
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>

SELECT (sum (?x) AS ?numeroExtranjerosComunitarios) WHERE {
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs iaest-dimension:nacionalidad ?nacionalidad.
  FILTER (?nacionalidad = <https://opendata.aragon.es/kos/iaest/nacionalidad/extranjeros-comunitarios-ue-27>)
  ?uriObs espad-medida:numero-personas ?x .
}

```

### 3. Cubo de datos según nivel de estudios

El grafo en el que se recogen los datos de este cubo es: http://ciudadesabiertas.linkeddata.es/cubo-datos/poblacion-por-nivel-estudio. Para la generación del grafo se ha ejecutado la siguiente query:
```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX espad-skos-tipo-nivel-estudio: <http://vocab.linkeddata.es/datosabiertos/kos/demografia/padron-municipal/tipo-nivel-estudio/>
PREFIX espad-medida:    <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX sdmx-dimension:  <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX schema: <http://schema.org/>

CONSTRUCT {
  ?uriObs a qb:Observation .
  ?uriObs qb:dataSet <http://vocab.ciudadesabiertas.es/recurso/demografia/padron-municipal/DS_PoblacionPorNivelEstudio> .
  ?uriObs sdmx-dimension:refPeriod <http://reference.data.gov.uk/id/year/2019> .
  ?uriObs sdmx-dimension:educationLev ?nivelEstudios.
  ?uriObs espad-medida:numero-personas ?numPersonas .
}
WHERE
{
SELECT ?nivelEstudios, (count(?nivelEstudios) as ?numPersonas), ?uriObs, ?age
WHERE {
  ?x a espad:Habitante .
  ?x espad:activo ?activo .
  ?x espad:nivelEstudios ?nivelEstudios.
  ?x schema:birthDay ?birthDay .

  FILTER(?activo)
  BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
  BIND(IRI(CONCAT("http://vocab.ciudadesabiertas.es/recurso/demografia/padron-municipal/",md5(xsd:string(CONCAT(?age,?nivelEstudios))))) AS ?uriObs)
  FILTER (?age>=25)
}
}
```
#### Ejemplos
a) En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Fpoblacion-por-nivel-estudio&query=PREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0APREFIX+iaest-dimension%3A+%3Chttp%3A%2F%2Fopendata.aragon.es%2Fdef%2Fiaest%2Fdimension%23%3E%0D%0APREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0A%0D%0ASELECT+%28count+%28%3Fx%29+AS+%3FnumeroPersonas%29+WHERE+%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+sdmx-dimension%3AeducationLev+%3Chttp%3A%2F%2Fvocab.linkeddata.es%2Fdatosabiertos%2Fkos%2Fdemografia%2Fpadron-municipal%2Ftipo-nivel-estudio%2F41%3E+.%0D%0A++%3FuriObs+espad-medida%3Anumero-personas+%3Fx+.%0D%0A++%7D&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el total de población (de 25 años y más) con estudios medios o educación secundaria. Para resolver esta query se ha empleado el código del nivel de estudio que para este caso corresponde a 41, "Formación profesional segundo grado/grado superior, maestría industrial". Los diferentes niveles de estudio se encuentran recogidos en el SKOS: http://vocab.linkeddata.es/datosabiertos/kos/demografia/padron-municipal/tipo-nivel-estudio

```
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX schema: <http://schema.org/>

SELECT (count (?x) AS ?numeroPersonas) WHERE {
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs sdmx-dimension:educationLev <http://vocab.linkeddata.es/datosabiertos/kos/demografia/padron-municipal/tipo-nivel-estudio/41> .
  ?uriObs espad-medida:numero-personas ?x .
  }

```

b) En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Fpoblacion-por-nivel-estudio&query=PREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0APREFIX+iaest-dimension%3A+%3Chttp%3A%2F%2Fopendata.aragon.es%2Fdef%2Fiaest%2Fdimension%23%3E%0D%0APREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0A%0D%0ASELECT+%28count+%28%3Fx%29+AS+%3FnumeroPersonas%29+WHERE+%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+sdmx-dimension%3AeducationLev+%3Chttp%3A%2F%2Fvocab.linkeddata.es%2Fdatosabiertos%2Fkos%2Fdemografia%2Fpadron-municipal%2Ftipo-nivel-estudio%2F48%3E+.%0D%0A++%3FuriObs+espad-medida%3Anumero-personas+%3Fx+.%0D%0A++%7D&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el total de población (de 25 años y más) con estudios muy avanzados. Para resolver esta query se ha empleado el código del nivel de estudios que para este caso corresponde a 48, "Doctorado, postgrado, especialización licenciado, máster universitario".

```
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX schema: <http://schema.org/>

SELECT (count (?x) AS ?numeroPersonas) WHERE {
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs sdmx-dimension:educationLev <http://vocab.linkeddata.es/datosabiertos/kos/demografia/padron-municipal/tipo-nivel-estudio/48> .
  ?uriObs espad-medida:numero-personas ?x .
  }

```
### 4. Cubo de datos según el lugar de procedencia

El grafo que contiene este cubo de datos es: http://ciudadesabiertas.linkeddata.es/cubo-datos/poblacion-por-lugar-procedencia. Para la generación del grafo se ha ejecutado la siguiente query:

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>
PREFIX dcterms: <http://purl.org/dc/terms/>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX qb: <http://purl.org/linked-data/cube#>

CONSTRUCT {
  ?uriObs a qb:Observation .
  ?uriObs qb:dataSet <http://vocab.ciudadesabiertas.es/recurso/demografia/padron-municipal/DS_PoblacionPorLugarProcedencia> .

 ?uriObs sdmx-dimension:refArea ?distritoResidencia .
  ?uriObs sdmx-dimension:refPeriod <http://reference.data.gov.uk/id/year/2019> .
  ?uriObs sdmx-dimension:educationLev ?nivelEstudios .
  ?uriObs iaest-dimension:edad-grupos-quinquenales ?grupoQuinquenal .
  ?uriObs espad:provinciaProcedencia ?provinciaProcedencia .
  ?uriObs espad:municipioProcedencia ?municipioProcedencia .
  ?uriObs espad-medida:numero-personas ?numeroPersonas
}
WHERE
{

SELECT (COUNT (DISTINCT ?x) AS ?numeroPersonas)?uriObs ?distritoResidencia ?provinciaProcedencia ?municipioProcedencia ?nivelEstudios ?grupoQuinquenal WHERE {
  ?x a espad:Habitante .
  ?x espad:activo ?activo .
  ?x espad:distritoResidencia ?distritoResidencia .
  ?x schema:birthDay ?birthDay .
  ?x espad:nivelEstudios ?nivelEstudios .
  ?x espad:municipioProcedencia ?municipioProcedencia .
  ?x espad:provinciaProcedencia ?provinciaProcedencia .

  FILTER(?activo)
  BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
  BIND(IRI(
    IF(?age <= 4, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/00-a-04",
      IF(?age <= 9, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/05-a-09",
        IF(?age <= 14, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/10-a-14",
          IF(?age <= 19, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/15-a-19",
            IF(?age <= 24, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/20-a-24",
              IF(?age <= 29, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/25-a-29",
                IF(?age <= 34, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/30-a-34",
                  IF(?age <= 39, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/35-a-39",
                    IF(?age <= 44, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/40-a-44",
                      IF(?age <= 49, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/45-a-49",
                        IF(?age <= 54, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/50-a-54",
                          IF(?age <= 59, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/55-a-59",
                            IF(?age <= 64 , "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/60-a-64",
                              IF(?age <= 69, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/65-a-69",
                                IF(?age <= 74, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/70-a-74",
                                  IF(?age <= 79, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/75-a-79",
                                    IF(?age <= 84, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/80-a-84",
                                      IF(?age <= 89, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/85-a-89",
                                        IF(?age <= 94, "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/90-a-94",
                                          "https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/95-y-mas")))))))))))))))))))) AS ?grupoQuinquenal)
   BIND(IRI(CONCAT("http://vocab.ciudadesabiertas.es/recurso/demografia/padron-municipal/",md5(xsd:string(CONCAT(?distritoResidencia,?nivelEstudios,?grupoQuinquenal,?municipioProcedencia,?provinciaProcedencia))))) AS ?uriObs)
  }
 }

```
#### Ejemplos

a) En esta [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Fpoblacion-por-lugar-procedencia&query=PREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0APREFIX+iaest-dimension%3A+%3Chttp%3A%2F%2Fopendata.aragon.es%2Fdef%2Fiaest%2Fdimension%23%3E%0D%0APREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+sdmx-kos%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fcode%23%3E%0D%0APREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0A%0D%0ASELECT+DISTINCT+%3FprovinciaProcedencia+%28Sum%28%3FnumeroPersonas%29+AS+%3FnumeroHabitantes%29+WHERE+%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefArea+%3Chttp%3A%2F%2Fvocab.linkeddata.es%2Frecurso%2Fterritorio%2Fdistrito%2F79610%3E+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+espad%3AprovinciaProcedencia+%3FprovinciaProcedencia+.%0D%0A++%3FuriObs+espad-medida%3Anumero-personas+%3FnumeroPersonas+.%0D%0A%7D%0D%0AORDER+BY+ASC%28%3FprovinciaProcedencia%29%0D%0A&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el número de habitantes que residen en cierta área según las provincias de las que proceden. Concretamente se obtiene el número de habitantes que residen en el distrito “Latina” según las provincias de procedencia.

```
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX sdmx-kos: <http://purl.org/linked-data/sdmx/2009/code#>
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>

SELECT DISTINCT ?provinciaProcedencia (Sum(?numeroPersonas) AS ?numeroHabitantes) WHERE {
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refArea <http://vocab.linkeddata.es/recurso/territorio/distrito/79610> .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs espad:provinciaProcedencia ?provinciaProcedencia .
  ?uriObs espad-medida:numero-personas ?numeroPersonas .
}
ORDER BY ASC(?provinciaProcedencia)
```
b) En esta [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Fpoblacion-por-lugar-procedencia&query=PREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0APREFIX+iaest-dimension%3A+%3Chttp%3A%2F%2Fopendata.aragon.es%2Fdef%2Fiaest%2Fdimension%23%3E%0D%0APREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+sdmx-kos%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fcode%23%3E%0D%0APREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0A%0D%0ASELECT+DISTINCT+%3FgrupoQuinquenal+%3FmunicipioProcedencia+%3FnivelEstudios+%28Sum%28%3FnumeroPersonas%29+AS+%3FnumeroHabitantes%29+WHERE+%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefArea+%3Chttp%3A%2F%2Fvocab.linkeddata.es%2Frecurso%2Fterritorio%2Fdistrito%2F79602%3E+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+espad%3AmunicipioProcedencia+%3FmunicipioProcedencia+.%0D%0A++%3FuriObs+espad-medida%3Anumero-personas+%3FnumeroPersonas+.%0D%0A++%3FuriObs+iaest-dimension%3Aedad-grupos-quinquenales+%3FgrupoQuinquenal+.%0D%0A++%3FuriObs+sdmx-dimension%3AeducationLev+%3FnivelEstudios+.%0D%0A%7D%0D%0AORDER+BY+ASC%28%3FgrupoQuinquenal%29&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el número de habitantes que residen en cierta área según rangos de edad, municipios de los que proceden, y niveles de estudio. Concretamente se obtiene el número de habitantes que residen en el distrito “Arganzuela” según rangos de edad, municipios de los que proceden, y niveles de estudio.

```
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX sdmx-kos: <http://purl.org/linked-data/sdmx/2009/code#>
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>

SELECT DISTINCT ?grupoQuinquenal ?municipioProcedencia ?nivelEstudios (Sum(?numeroPersonas) AS ?numeroHabitantes) WHERE {
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refArea <http://vocab.linkeddata.es/recurso/territorio/distrito/79602> .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs espad:municipioProcedencia ?municipioProcedencia .
  ?uriObs espad-medida:numero-personas ?numeroPersonas .
  ?uriObs iaest-dimension:edad-grupos-quinquenales ?grupoQuinquenal .
  ?uriObs sdmx-dimension:educationLev ?nivelEstudios .
}
ORDER BY ASC(?grupoQuinquenal)
```

### 5. Cubo de datos según indicadores demográficos

El grafo de este cubo es: http://ciudadesabiertas.linkeddata.es/cubo-datos/indicadores-estructura-demografica. Para este cubo se ha generado el grafo de datos para tres indicadores (índice de infancia, índice de juventud, índice de población activa) mediante la siguiente query:
```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>
PREFIX dcterms: <http://purl.org/dc/terms/>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX iaest-dimension: <http://opendata.aragon.es/def/iaest/dimension#>
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX qb: <http://purl.org/linked-data/cube#>
CONSTRUCT {
 # Indice Infancia
 ?uriObsIndiceInfancia a qb:Observation .
 ?uriObsIndiceInfancia qb:dataSet <http://vocab.ciudadesabiertas.es/recurso/demografia/padron-municipal/DS_IndicadoresEstructuraDemografica> .
 ?uriObsIndiceInfancia sdmx-dimension:refPeriod <http://reference.data.gov.uk/id/year/2019> .
 ?uriObsIndiceInfancia espad-medida:indice-de-infancia ?indiceInfancia .

 # Indice Juventud
 ?uriObsIndiceJuventud a qb:Observation .
 ?uriObsIndiceJuventud qb:dataSet <http://vocab.ciudadesabiertas.es/recurso/demografia/padron-municipal/DS_IndicadoresEstructuraDemografica> .
 ?uriObsIndiceJuventud sdmx-dimension:refPeriod <http://reference.data.gov.uk/id/year/2019> .
 ?uriObsIndiceJuventud espad-medida:indice-de-juventud ?indiceJuventud .

 # Indice Poblacion Activa
 ?uriObsIndicePoblacionActiva a qb:Observation .
 ?uriObsIndicePoblacionActiva qb:dataSet <http://vocab.ciudadesabiertas.es/recurso/demografia/padron-municipal/DS_IndicadoresEstructuraDemografica> .
 ?uriObsIndicePoblacionActiva sdmx-dimension:refPeriod <http://reference.data.gov.uk/id/year/2019> .
 ?uriObsIndicePoblacionActiva espad-medida:indice-de-poblacion-activa ?indicePoblacionActiva .


}
WHERE
{
  #Indice de infancia
  SELECT ((?numeroInfantes*100)/(count(?x)) as ?indiceInfancia), ((?jovenes*100)/(count(?x)) as ?indiceJuventud), ?uriObsIndiceInfancia, ?uriObsIndiceJuventud, ?indicePoblacionActiva, ?uriObsIndicePoblacionActiva
  WHERE {
    ?x a espad:Habitante .
    ?x espad:activo ?activo .
    FILTER(?activo)
    {
     SELECT  (count(?infantes) as ?numeroInfantes)
     FROM <http://ciudadesabiertas.linkeddata.es/cubos-datos/poblacion-por-edad>
     WHERE {
      ?uriObs a qb:Observation .
      ?uriObs sdmx-dimension:refPeriod <http://reference.data.gov.uk/id/year/2019> .
      ?uriObs iaest-dimension:edad-grupos-quinquenales ?gruposQuinquenales .
      FILTER (?gruposQuinquenales = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/00-a-04> || ?gruposQuinquenales = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/05-a-09> || ?gruposQuinquenales = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/10-a-14> )
      ?uriObs espad-medida:numero-personas ?infantes
      }
   }.

 {
   #Indice de juventud
   SELECT  (count(?x) as ?jovenes)
   FROM <http://ciudadesabiertas.linkeddata.es/cubos-datos/poblacion-por-edad>
   WHERE {
    ?uriObs a qb:Observation .
    ?uriObs iaest-dimension:edad-grupos-quinquenales ?grupoQuinquenal .
    ?uriObs sdmx-dimension:refPeriod <http://reference.data.gov.uk/id/year/2019> .
    FILTER (?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/00-a-04> || ?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/05-a-09> || ?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/10-a-14> || ?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/15-a-19> || ?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/20-a-24> )
    ?uriObs espad-medida:numero-personas ?x
    }
}.
{
  #Indice de población potencialmente activa
  SELECT ((?poblacion15a39*100)/count(?x)) as ?indicePoblacionActiva
  FROM <http://ciudadesabiertas.linkeddata.es/cubos-datos/poblacion-por-edad>
  WHERE
  {
    ?uriObs a qb:Observation .
    ?uriObs sdmx-dimension:refPeriod <http://reference.data.gov.uk/id/year/2019> .
    ?uriObs iaest-dimension:edad-grupos-quinquenales ?grupoQuinquenal .
    FILTER (?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/40-a-44> || ?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/45-a-49> || ?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/50-a-54> || ?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/55-a-59> || ?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/60-a-64> )
    ?uriObs espad-medida:numero-personas ?x
    {
      SELECT count(?x) as ?poblacion15a39
      WHERE {
        ?uriObs a qb:Observation .
        ?uriObs sdmx-dimension:refPeriod <http://reference.data.gov.uk/id/year/2019> .
        ?uriObs iaest-dimension:edad-grupos-quinquenales ?grupoQuinquenal .
        FILTER (?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/15-a-19> || ?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/20-a-24> || ?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/25-a-29> || ?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/30-a-34> || ?grupoQuinquenal = <https://opendata.aragon.es/kos/iaest/edad-grupos-quinquenales/35-a-39> )
        ?uriObs espad-medida:numero-personas ?x
      }
    }
  }
 }.

 BIND(IRI(CONCAT("http://vocab.ciudadesabiertas.es/recurso/demografia/padron-municipal/",md5(xsd:string(CONCAT("indiceInfancia",<http://reference.data.gov.uk/id/year/2019>))))) AS ?uriObsIndiceInfancia)
 BIND(IRI(CONCAT("http://vocab.ciudadesabiertas.es/recurso/demografia/padron-municipal/",md5(xsd:string(CONCAT("indiceJuventud",<http://reference.data.gov.uk/id/year/2019>))))) AS ?uriObsIndiceJuventud)
 BIND(IRI(CONCAT("http://vocab.ciudadesabiertas.es/recurso/demografia/padron-municipal/",md5(xsd:string(CONCAT("indicePoblacionActiva",<http://reference.data.gov.uk/id/year/2019>))))) AS ?uriObsIndicePoblacionActiva)
  }
}

```

Para la generación de otros indicadores se deben ejecutar queries similares a las presentadas anteriormente y cargarlas en el grafo de indicadores de estructura demográfica.

#### Ejemplos

a) En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Findicadores-estructura-demografica&query=PREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0A%0D%0ASELECT+%3FindiceInfancia+%0D%0AWhere%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+espad-medida%3Aindice-de-infancia+%3FindiceInfancia+.%0D%0A%7D%0D%0A%0D%0A&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el índice de infancia.

```
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>

SELECT ?indiceInfancia
Where{
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs espad-medida:indice-de-infancia ?indiceInfancia .
}

```

b) En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Findicadores-estructura-demografica&query=PREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0A%0D%0ASELECT+%3FindiceJuventud+%0D%0AWhere%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+espad-medida%3Aindice-de-juventud+%3FindiceJuventud+.%0D%0A%7D%0D%0A%0D%0A&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el índice de juventud.

```
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>

SELECT ?indiceJuventud
Where{
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs espad-medida:indice-de-juventud ?indiceJuventud .
}

```

c) En esta [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fcubo-datos%2Findicadores-estructura-demografica&query=PREFIX+espad-medida%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%2Fmedida%23%3E%0D%0APREFIX+qb%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fcube%23%3E%0D%0APREFIX+sdmx-dimension%3A+%3Chttp%3A%2F%2Fpurl.org%2Flinked-data%2Fsdmx%2F2009%2Fdimension%23%3E%0D%0APREFIX+kos-year%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fid%2Fyear%2F%3E%0D%0A%0D%0ASELECT+%3FindicePoblacionActiva%0D%0AWhere%7B%0D%0A++%3FuriObs+a+qb%3AObservation+.%0D%0A++%3FuriObs+sdmx-dimension%3ArefPeriod+kos-year%3A2019+.%0D%0A++%3FuriObs+espad-medida%3Aindice-de-poblacion-activa+%3FindicePoblacionActiva+.%0D%0A%7D%0D%0A%0D%0A&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el índice de población potencialmente activa.

```
PREFIX espad-medida: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal/medida#>
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX sdmx-dimension: <http://purl.org/linked-data/sdmx/2009/dimension#>
PREFIX kos-year: <http://reference.data.gov.uk/id/year/>

SELECT ?indicePoblacionActiva
Where{
  ?uriObs a qb:Observation .
  ?uriObs sdmx-dimension:refPeriod kos-year:2019 .
  ?uriObs espad-medida:indice-de-poblacion-activa ?indicePoblacionActiva .
}

```
