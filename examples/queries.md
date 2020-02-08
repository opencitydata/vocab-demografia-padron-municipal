## Ejemplos de consultas sobre el padrón de habitantes

A continuación se presentan algunos ejemplos de consultas, utilizando como referencia un extracto del conjunto de datos de prueba del INE, que se corresponden con los ficheros del registro de habitantes en diferentes Ayuntamientos. Este registro ha sido filtrado con los distritos de Madrid.

Por ejemplo, en la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0A%0D%0ASELECT+%28AVG%28%3Fage%29+as+%3FedadMedia%29%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A++++%3Fhabitante+espad%3Aactivo+%3Factivo+.%0D%0A++++FILTER+%28%3Factivo%29%0D%0A++++%3Fhabitante+schema%3AbirthDay+%3FbirthDay+.%0D%0A++++BIND%28year%28NOW%28%29%29+-+year%28%3FbirthDay%29+-+IF%28month%28NOW%28%29%29%3Cmonth%28%3FbirthDay%29+%7C%7C+%28month%28NOW%28%29%29%3Dmonth%28%3FbirthDay%29+%26%26+day%28NOW%28%29%29%3Cday%28%3FbirthDay%29%29%2C1%2C0%29+as+%3Fage%29%0D%0A%7D&format=text%2Fhtml&timeout=0&debug=on) se obtiene la edad media de la población .

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>

SELECT (AVG(?age) as ?edadMedia)
WHERE {
    ?habitante a espad:Habitante   .     
    ?habitante espad:activo ?activo .
    FILTER (?activo)
    ?habitante schema:birthDay ?birthDay .
    BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
}
```

En esta [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+dcterms%3A+%3Chttp%3A%2F%2Fpurl.org%2Fdc%2Fterms%2F%3E%0D%0A%0D%0ASELECT+%3FnombreDistrito+%28count%28distinct+%3Fhabitante%29+as+%3FpoblacionTotal%29%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+a+espad%3AHabitante+++.%0D%0A++++%3Fhabitante+espad%3Aactivo+%3Factivo+.%0D%0A++++FILTER+%28%3Factivo%29%0D%0A++++%3Fhabitante+espad%3AdistritoResidencia+%3Fdistrito+.%0D%0A++++%3Fdistrito+dcterms%3Atitle+%22Arganzuela%22+.%0D%0A++++%3Fdistrito+dcterms%3Atitle+%3FnombreDistrito+.%0D%0A%7D%0D%0A&format=text%2Fhtml&timeout=0&debug=on) se la obtiene población total del distrito “Arganzuela”.  
```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX dcterms: <http://purl.org/dc/terms/>

SELECT ?nombreDistrito (count(distinct ?habitante) as ?poblacionTotal)
WHERE {
    ?habitante a espad:Habitante   .
    ?habitante espad:activo ?activo .
    FILTER (?activo)
    ?habitante espad:distritoResidencia ?distrito .
    ?distrito dcterms:title "Arganzuela" .
    ?distrito dcterms:title ?nombreDistrito .
}

```
En esta [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0APREFIX+dcterms%3A+%3Chttp%3A%2F%2Fpurl.org%2Fdc%2Fterms%2F%3E%0D%0A%0D%0ASELECT+%3Fsexo+%28count%28%3Fsexo%29+as+%3FpoblacionTotal%29%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+a+espad%3AHabitante+++.%0D%0A++++%3Fhabitante+espad%3Aactivo+%3Factivo+.%0D%0A++++FILTER+%28%3Factivo%29%0D%0A++++%3Fhabitante+espad%3AdistritoResidencia+%3Fdistrito+.%0D%0A++++%3Fdistrito+dcterms%3Atitle+%22Latina%22+.%0D%0A++++%3Fhabitante+schema%3Agender+%3Fsexo+.%0D%0A%7D&format=text%2Fhtml&timeout=0&debug=on) se obtiene la población total según sexo del distrito “Latina”.

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>
PREFIX dcterms: <http://purl.org/dc/terms/>

SELECT ?sexo (count(?sexo) as ?poblacionTotal)
WHERE {
    ?habitante a espad:Habitante   .
    ?habitante espad:activo ?activo .
    FILTER (?activo)
    ?habitante espad:distritoResidencia ?distrito .
    ?distrito dcterms:title "Latina" .
    ?habitante schema:gender ?sexo .
}

```

En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0APREFIX+dcterms%3A+%3Chttp%3A%2F%2Fpurl.org%2Fdc%2Fterms%2F%3E%0D%0A%0D%0ASELECT+distinct+%3Fsexo%2C+%28count%28%3Fage%29+as+%3FpoblacionTotal%29+WHERE+%7B%0D%0A++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A++++%3Fhabitante+espad%3Aactivo+%3Factivo+.%0D%0A++++FILTER+%28%3Factivo%29%0D%0A++++%3Fhabitante+espad%3AdistritoResidencia+%3Fdistrito+.%0D%0A++++%3Fdistrito+dcterms%3Atitle+%22Latina%22+.%0D%0A++++%3Fhabitante+schema%3AbirthDay+%3FbirthDay+.%0D%0A++++BIND%28year%28NOW%28%29%29+-+year%28%3FbirthDay%29+-+IF%28month%28NOW%28%29%29%3Cmonth%28%3FbirthDay%29+%7C%7C+%28month%28NOW%28%29%29%3Dmonth%28%3FbirthDay%29+%26%26+day%28NOW%28%29%29%3Cday%28%3FbirthDay%29%29%2C1%2C0%29+as+%3Fage%29%0D%0A++++FILTER+%28%3Fage%3E%3D35+%26%26+%3Fage%3C%3D39%29%0D%0A++++%3Fhabitante+schema%3Agender+%3Fsexo+.%0D%0A%7D&format=text%2Fhtml&timeout=0&debug=on) se obtienen la población total según sexo y rango de edad, comprendido entre los 35 y 39 años, del distrito “Latina”.

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>
PREFIX dcterms: <http://purl.org/dc/terms/>

SELECT distinct ?sexo, (count(?age) as ?poblacionTotal) WHERE {
    ?habitante a espad:Habitante   .     
    ?habitante espad:activo ?activo .
    FILTER (?activo)
    ?habitante espad:distritoResidencia ?distrito .
    ?distrito dcterms:title "Latina" .
    ?habitante schema:birthDay ?birthDay .
    BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
    FILTER (?age>=35 && ?age<=39)
    ?habitante schema:gender ?sexo .
}

```
En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0APREFIX+dcterms%3A+%3Chttp%3A%2F%2Fpurl.org%2Fdc%2Fterms%2F%3E%0D%0A%0D%0ASELECT+%28count%28distinct+%3Fhabitante%29+as+%3FpoblacionTotal%29%2C+%3FpoblacionExtranjeros%2C+%28%28%3FpoblacionExtranjeros*100%29%2F%28count%28distinct+%3Fhabitante%29%29+as+%3FporcentajeExtranjeros%29%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+espad%3Aactivo+%3Factivo+.%0D%0A++++FILTER+%28%3Factivo%29%0D%0A+++++%7B%0D%0A+++++++SELECT+%28count%28%3Fhabitante%29+as+%3FpoblacionExtranjeros%29%0D%0A+++++++WHERE+%7B%0D%0A++++++++%3Fhabitante+schema%3Anationality+%3Fpais+.%0D%0A++++++++%3Fpais+dcterms%3Atitle+%3FpaisNombre+.%0D%0A+++++++%7D%0D%0A++++%7D%0D%0A%7D%0D%0A&format=text%2Fhtml&timeout=0&debug=on) se obtiene el porcentaje de población nacida en el extranjero.

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>
PREFIX dcterms: <http://purl.org/dc/terms/>

SELECT (count(distinct ?habitante) as ?poblacionTotal), ?poblacionExtranjeros, ((?poblacionExtranjeros*100)/(count(distinct ?habitante)) as ?porcentajeExtranjeros)
WHERE {
    ?habitante espad:activo ?activo .
    FILTER (?activo)
     {
       SELECT (count(?habitante) as ?poblacionExtranjeros)
       WHERE {
        ?habitante schema:nationality ?pais .
        ?pais dcterms:title ?paisNombre .
       }
    }
}

```
En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0ASELECT+distinct+%3Fsexo%2C+%28count%28%3Fage%29+as+%3FTotalExtranjeros%29%2C+%28%28count%28%3Fage%29*100%29%2F%3FpoblacionTotal+as+%3FporcentajeExtranjeros%29%0D%0AWHERE+%7B+++%0D%0A++++%3Fhabitante+espad%3ApaisNacimiento+%3Fpais+.%0D%0A++++%3Fhabitante+schema%3AbirthDay+%3FbirthDay+.%0D%0A++++BIND%28year%28NOW%28%29%29+-+year%28%3FbirthDay%29+-+IF%28month%28NOW%28%29%29%3Cmonth%28%3FbirthDay%29+%7C%7C+%28month%28NOW%28%29%29%3Dmonth%28%3FbirthDay%29+%26%26+day%28NOW%28%29%29%3Cday%28%3FbirthDay%29%29%2C1%2C0%29+as+%3Fage%29%0D%0A%0D%0A++++%7B++++%0D%0A++++SELECT+%28count%28%3Fhabitante%29+as+%3FpoblacionTotal%29%0D%0A+++++++WHERE+%7B%0D%0A++++++++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A++++++++++%3Fhabitante+espad%3Aactivo+%22true%22%5E%5E%3Chttp%3A%2F%2Fwww.w3.org%2F2001%2FXMLSchema%23boolean%3E+.%0D%0A+++++++%7D%0D%0A++++%7D%0D%0A++++FILTER+%28%3Fage%3E%3D0+%26%26+%3Fage%3C%3D39%29%0D%0A++++%3Fhabitante+schema%3Agender+%3Fsexo+.%0D%0A%7D%0D%0A%0D%0A&format=text%2Fhtml&timeout=0&debug=on) se obtiene el porcentaje de población nacida en el extranjero según sexo y rango de edad (por ejemplo, de 0 a 39 años).

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>

SELECT distinct ?sexo, (count(?age) as ?TotalExtranjeros), ((count(?age)*100)/?poblacionTotal as ?porcentajeExtranjeros)
WHERE {   
    ?habitante espad:paisNacimiento ?pais .
    ?habitante schema:birthDay ?birthDay .
    BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)

    {    
    SELECT (count(?habitante) as ?poblacionTotal)
       WHERE {
          ?habitante a espad:Habitante   .     
          ?habitante espad:activo "true"^^<http://www.w3.org/2001/XMLSchema#boolean> .
       }
    }
    FILTER (?age>=0 && ?age<=39)
    ?habitante schema:gender ?sexo .
}

```

En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0APREFIX+dcterms%3A+%3Chttp%3A%2F%2Fpurl.org%2Fdc%2Fterms%2F%3E%0D%0A%0D%0ASELECT+%3FpaisNombre+%28count%28distinct+%3Fhabitante%29+as+%3FtotalPoblacion%29%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A++++%3Fhabitante+espad%3Aactivo+%3Factivo+.%0D%0A++++FILTER+%28%3Factivo%29%0D%0A++++%3Fhabitante+schema%3Anationality+%3Fpais+.%0D%0A++++%3Fpais+dcterms%3Atitle+%3FpaisNombre+.%0D%0A%7D%0D%0AORDER+BY+ASC+%28%3FpaisNombre%29&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene la población según país de nacionalidad.

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>
PREFIX dcterms: <http://purl.org/dc/terms/>

SELECT ?paisNombre (count(distinct ?habitante) as ?totalPoblacion)
WHERE {
    ?habitante a espad:Habitante   .     
    ?habitante espad:activo ?activo .
    FILTER (?activo)
    ?habitante schema:nationality ?pais .
    ?pais dcterms:title ?paisNombre .
}
ORDER BY ASC (?paisNombre)
```
En esta [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0A%0D%0ASELECT+%28count%28distinct+%3Fhabitante%29+as+%3FpoblacionJoven%29%2C+%3FpoblacionTotal%2C+%28%28count%28distinct+%3Fhabitante%29*100%29%2F%3FpoblacionTotal+as+%3FporcentajePoblacionJoven%29%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+schema%3AbirthDay+%3FbirthDay+.%0D%0A++++BIND%28year%28NOW%28%29%29+-+year%28%3FbirthDay%29+-+IF%28month%28NOW%28%29%29%3Cmonth%28%3FbirthDay%29+%7C%7C+%28month%28NOW%28%29%29%3Dmonth%28%3FbirthDay%29+%26%26+day%28NOW%28%29%29%3Cday%28%3FbirthDay%29%29%2C1%2C0%29+as+%3Fage%29%0D%0A++++FILTER+%28%3Fage%3E%3D0+%26%26+%3Fage%3C25%29%0D%0A++++%7B%0D%0A+++++++SELECT+%28count%28%3Fhabitante%29+as+%3FpoblacionTotal%29%0D%0A+++++++WHERE+%7B%0D%0A++++++++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A++++++++++%3Fhabitante+espad%3Aactivo+%22true%22%5E%5E%3Chttp%3A%2F%2Fwww.w3.org%2F2001%2FXMLSchema%23boolean%3E+.%0D%0A+++++++%7D%0D%0A++++%7D%0D%0A%7D%0D%0A&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtienen el porcentaje de población joven del ayuntamiento.
```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>

SELECT (count(distinct ?habitante) as ?poblacionJoven), ?poblacionTotal, ((count(distinct ?habitante)*100)/?poblacionTotal as ?porcentajePoblacionJoven)
WHERE {
    ?habitante schema:birthDay ?birthDay .
    BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
    FILTER (?age>=0 && ?age<25)
    {
       SELECT (count(?habitante) as ?poblacionTotal)
       WHERE {
          ?habitante a espad:Habitante   .     
          ?habitante espad:activo "true"^^<http://www.w3.org/2001/XMLSchema#boolean> .
       }
    }
}

```
En esta [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0A%0D%0ASELECT+%28count%28distinct+%3Fhabitante%29+as+%3Fpoblacion40a64%29%2C+%3Fpoblacion15a39%2C+%28%28%3Fpoblacion15a39*100%29%2F%28count%28distinct+%3Fhabitante%29%29+as+%3FindicePoblacionActiva%29%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A++++%3Fhabitante+espad%3Aactivo+%3Factivo.%0D%0A++++FILTER+%28%3Factivo%29%0D%0A++++%3Fhabitante+schema%3AbirthDay+%3FbirthDay+.%0D%0A++++BIND%28year%28NOW%28%29%29+-+year%28%3FbirthDay%29+-+IF%28month%28NOW%28%29%29%3Cmonth%28%3FbirthDay%29+%7C%7C+%28month%28NOW%28%29%29%3Dmonth%28%3FbirthDay%29+%26%26+day%28NOW%28%29%29%3Cday%28%3FbirthDay%29%29%2C1%2C0%29+as+%3Fage%29%0D%0A++++FILTER+%28%3Fage%3E%3D40+%26%26+%3Fage%3C%3D64%29%0D%0A++++%7B%0D%0A+++++++SELECT+%28count%28%3Fhabitante%29+as+%3Fpoblacion15a39%29%0D%0A+++++++WHERE+%7B%0D%0A+++++++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A+++++++++%3Fhabitante+espad%3Aactivo+%3Factivo.%0D%0A+++++++++FILTER+%28%3Factivo%29%0D%0A+++++++++%3Fhabitante+schema%3AbirthDay+%3FbirthDay+.%0D%0A+++++++++BIND%28year%28NOW%28%29%29+-+year%28%3FbirthDay%29+-+IF%28month%28NOW%28%29%29%3Cmonth%28%3FbirthDay%29+%7C%7C+%28month%28NOW%28%29%29%3Dmonth%28%3FbirthDay%29+%26%26+day%28NOW%28%29%29%3Cday%28%3FbirthDay%29%29%2C1%2C0%29+as+%3Fage%29%0D%0A++++++++++FILTER+%28%3Fage%3E%3D15+%26%26+%3Fage%3C%3D39%29%0D%0A+++++++%7D%0D%0A++++%7D%0D%0A%7D&format=text%2Fhtml&timeout=0&debug=on) se obtiene el índice de población potencialmente activa.

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>

SELECT (count(distinct ?habitante) as ?poblacion40a64), ?poblacion15a39, ((?poblacion15a39*100)/(count(distinct ?habitante)) as ?indicePoblacionActiva)
WHERE {
    ?habitante a espad:Habitante   .     
    ?habitante espad:activo ?activo.
    FILTER (?activo)
    ?habitante schema:birthDay ?birthDay .
    BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
    FILTER (?age>=40 && ?age<=64)
    {
       SELECT (count(?habitante) as ?poblacion15a39)
       WHERE {
         ?habitante a espad:Habitante   .     
         ?habitante espad:activo ?activo.
         FILTER (?activo)
         ?habitante schema:birthDay ?birthDay .
         BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
          FILTER (?age>=15 && ?age<=39)
       }
    }
}
```
En esta [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+dcterms%3A+%3Chttp%3A%2F%2Fpurl.org%2Fdc%2Fterms%2F%3E%0D%0A%0D%0ASELECT+DISTINCT+%3Fprovincia%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A++++%3Fhabitante+espad%3Aactivo+%3Factivo+.%0D%0A++++FILTER+%28%3Factivo%29%0D%0A++++%3Fhabitante+espad%3AdistritoResidencia+%3Fdistrito+.%0D%0A++++%3Fdistrito+dcterms%3Atitle+%22Latina%22+.%0D%0A++++%3Fhabitante+espad%3AprovinciaProcedencia+%3Fprovincia+.%0D%0A%7D%0D%0A&format=text%2Fhtml&timeout=0&debug=on) se obtienen las provincias de las que proceden los habitantes del distrito “Latina”.

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX dcterms: <http://purl.org/dc/terms/>

SELECT DISTINCT ?provincia
WHERE {
    ?habitante a espad:Habitante   .     
    ?habitante espad:activo ?activo .
    FILTER (?activo)
    ?habitante espad:distritoResidencia ?distrito .
    ?distrito dcterms:title "Latina" .
    ?habitante espad:provinciaProcedencia ?provincia .
}
```
En esta [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+dcterms%3A+%3Chttp%3A%2F%2Fpurl.org%2Fdc%2Fterms%2F%3E%0D%0A%0D%0ASELECT+DISTINCT+%3Fmunicipio%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A++++%3Fhabitante+espad%3Aactivo+%3Factivo+.%0D%0A++++FILTER+%28%3Factivo%29%0D%0A++++%3Fhabitante+espad%3AdistritoResidencia+%3Fdistrito+.%0D%0A++++%3Fdistrito+dcterms%3Atitle+%22Arganzuela%22+.%0D%0A++++%3Fhabitante+espad%3AmunicipioProcedencia+%3Fmunicipio+.+++%0D%0A%7D%0D%0A&format=text%2Fhtml&timeout=0&debug=on) se obtienen los municipios de los que proceden los habitantes del distrito "Arganzuela"”.

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX dcterms: <http://purl.org/dc/terms/>

SELECT DISTINCT ?municipio
WHERE {
    ?habitante a espad:Habitante   .     
    ?habitante espad:activo ?activo .
    FILTER (?activo)
    ?habitante espad:distritoResidencia ?distrito .
    ?distrito dcterms:title "Arganzuela" .
    ?habitante espad:municipioProcedencia ?municipio .   
}
```

En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0APREFIX+dcterms%3A+%3Chttp%3A%2F%2Fpurl.org%2Fdc%2Fterms%2F%3E%0D%0APREFIX+espad-skos-tipo-nivel-estudio%3A+%3Chttp%3A%2F%2Fvocab.linkeddata.es%2Fdatosabiertos%2Fkos%2Fdemografia%2Fpadron-municipal%2Ftipo-nivel-estudio%2F%3E%0D%0A%0D%0ASELECT+%22Mayores+25%22+as+%3Frango%2C+%22Formaci%C3%B3n+profesional+segundo+grado%2Fgrado+superior%2C+maestr%C3%ADa+industrial%22+as+%3FNivelEstudio%2C+%28count%28%3Fhabitante%29+as+%3FtotalPoblacion%29+%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A++++%3Fhabitante+espad%3Aactivo+%22true%22%5E%5E%3Chttp%3A%2F%2Fwww.w3.org%2F2001%2FXMLSchema%23boolean%3E+.%0D%0A++++%3Fhabitante+schema%3AbirthDay+%3FfechaNacimiento+.%0D%0A++++bind+%28year%28now%28%29%29+-+year%28%3FfechaNacimiento%29+as+%3Fage%29+%0D%0A++++FILTER+%28%3Fage%3E%3D25%29%0D%0A++++%3Fhabitante+espad%3AnivelEstudios+espad-skos-tipo-nivel-estudio%3A41+.+++%0D%0A++++%3Fhabitante+espad%3AnivelEstudios+%3FnivelEstudios+.++++%0D%0A%7D%0D%0A%0D%0A&format=text%2Fhtml&timeout=0&debug=on) se obtiene el número de población (de 25 años o más) con estudios medios o educación secundaria. Para resolver esta query se ha empleado el código del nivel de estudio que para este caso corresponde a 41, "Formación profesional segundo grado/grado superior, maestría industrial". Los tipos de niveles de estudio se encuentran en el SKOS: http://vocab.linkeddata.es/datosabiertos/kos/demografia/padron-municipal/tipo-nivel-estudio

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>
PREFIX espad-skos-tipo-nivel-estudio: <http://vocab.linkeddata.es/datosabiertos/kos/demografia/padron-municipal/tipo-nivel-estudio/>

SELECT "Mayores 25" as ?rango, "Formación profesional segundo grado/grado superior, maestría industrial" as ?NivelEstudio, (count(?habitante) as ?totalPoblacion)
WHERE {
    ?habitante a espad:Habitante   .     
    ?habitante espad:activo "true"^^<http://www.w3.org/2001/XMLSchema#boolean> .
    ?habitante schema:birthDay ?birthDay .
    BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
    FILTER (?age>=25)
    ?habitante espad:nivelEstudios espad-skos-tipo-nivel-estudio:41 .   
}

```

En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0APREFIX+esadm%3A+%3Chttp%3A%2F%2Fvocab.linkeddata.es%2Fdatosabiertos%2Fdef%2Fsector-publico%2Fterritorio%23%3E%0D%0APREFIX+esdir%3A+%3Chttp%3A%2F%2Fvocab.linkeddata.es%2Fdatosabiertos%2Fdef%2Furbanismo-infraestructuras%2Fdireccion-postal%23%3E%0D%0APREFIX+dcterms%3A+%3Chttp%3A%2F%2Fpurl.org%2Fdc%2Fterms%2F%3E%0D%0A%0D%0ASELECT+%28count%28distinct+%3Fhabitante%29+as+%3FtotalExtranjeros%29%2C+%3FpoblacionTotal%2C+%28%28count%28distinct+%3Fhabitante%29*100%29%2F%3FpoblacionTotal+as+%3FporcentajeExtranjeros%29%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+espad%3ApaisNacimiento+%3Fpais+.%0D%0A++++%3Fpais+dcterms%3Atitle+%3FpaisNombre+.%0D%0A++++++++++%3Fhabitante+schema%3AbirthDay+%3FfechaNacimiento+.%0D%0A++++++++++bind+%28year%28now%28%29%29+-+year%28%3FfechaNacimiento%29+as+%3FageExtranjeros%29+%0D%0A++++++++++FILTER+%28%3FageExtranjeros%3E%3D0+%26%26+%3FageExtranjeros%3C%3D15%29%0D%0A%0D%0A++++%7B%0D%0A+++++++SELECT+%28count%28%3Fhabitante%29+as+%3FpoblacionTotal%29%0D%0A+++++++WHERE+%7B%0D%0A++++++++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A++++++++++%3Fhabitante+espad%3Aactivo+%22true%22%5E%5E%3Chttp%3A%2F%2Fwww.w3.org%2F2001%2FXMLSchema%23boolean%3E+.%0D%0A++++++++++%3Fhabitante+schema%3AbirthDay+%3FfechaNacimiento+.%0D%0A++++++++++bind+%28year%28now%28%29%29+-+year%28%3FfechaNacimiento%29+as+%3Fage%29+%0D%0A++++++++++FILTER+%28%3Fage%3E%3D0+%26%26+%3Fage%3C%3D15%29%0D%0A+++++++%7D%0D%0A++++%7D%0D%0A%7D%0D%0A&format=text%2Fhtml&timeout=0&debug=on) se obtienen porcentaje de población extranjera infantil (menores de 15 años).

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>
PREFIX dcterms: <http://purl.org/dc/terms/>

SELECT (count(distinct ?habitante) as ?totalExtranjeros), ?poblacionTotal, ((count(distinct ?habitante)*100)/?poblacionTotal as ?porcentajeExtranjeros)
WHERE {
    ?habitante espad:paisNacimiento ?pais .
    ?pais dcterms:title ?paisNombre .
    ?habitante schema:birthDay ?birthDay .
    BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?ageExtranjeros)
          FILTER (?ageExtranjeros>=0 && ?ageExtranjeros<15)

    {
       SELECT (count(?habitante) as ?poblacionTotal)
       WHERE {
          ?habitante espad:activo ?activo.
          FILTER (?activo)
          ?habitante schema:birthDay ?birthDay .
          BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
          FILTER (?age>=0 && ?age<15)
       }
    }
}

```

En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0A%0D%0ASELECT+%28count%28distinct+%3Fhabitante%29+as+%3FpoblacionJoven%29%2C+%3FpoblacionTotal%2C+%28%28count%28distinct+%3Fhabitante%29*100%29%2F%3FpoblacionTotal+as+%3FporcentajePoblacionJoven%29%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+schema%3AbirthDay+%3FfechaNacimiento+.%0D%0A++++bind+%28year%28now%28%29%29+-+year%28%3FfechaNacimiento%29+as+%3Fage%29+%0D%0A++++FILTER+%28%3Fage%3E%3D0+%26%26+%3Fage%3C25%29%0D%0A++++%7B%0D%0A+++++++SELECT+%28count%28%3Fhabitante%29+as+%3FpoblacionTotal%29%0D%0A+++++++WHERE+%7B%0D%0A++++++++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A++++++++++%3Fhabitante+espad%3Aactivo+%22true%22%5E%5E%3Chttp%3A%2F%2Fwww.w3.org%2F2001%2FXMLSchema%23boolean%3E+.%0D%0A+++++++%7D%0D%0A++++%7D%0D%0A%7D%0D%0A&format=text%2Fhtml&timeout=0&debug=on) se obtienen el porcentaje de población joven del ayuntamiento.

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>

SELECT (count(distinct ?habitante) as ?poblacionJoven), ?poblacionTotal, ((count(distinct ?habitante)*100)/?poblacionTotal as ?porcentajePoblacionJoven)
WHERE {
  ?habitante schema:birthDay ?birthDay .
  BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
  FILTER (?age>=0 && ?age<25)
    {
       SELECT (count(?habitante) as ?poblacionTotal)
       WHERE {
          ?habitante a espad:Habitante   .     
          ?habitante espad:activo "true"^^<http://www.w3.org/2001/XMLSchema#boolean> .
       }
    }
}

```

En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0A%0D%0ASELECT+%28count%28distinct+%3Fhabitante%29+as+%3FpoblacionMayor85%29%2C+%3FpoblacionMayor65%2C+%28%28count%28distinct+%3Fhabitante%29*100%29%2F%3FpoblacionMayor65+as+%3FindiceSobreenvejecimiento%29%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+schema%3AbirthDay+%3FfechaNacimiento+.%0D%0A++++bind+%28year%28now%28%29%29+-+year%28%3FfechaNacimiento%29+as+%3Fage%29+%0D%0A++++FILTER+%28%3Fage%3E%3D85%29%0D%0A++++%7B%0D%0A+++++++SELECT+%28count%28%3Fhabitante%29+as+%3FpoblacionMayor65%29%0D%0A+++++++WHERE+%7B%0D%0A++++++++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A++++++++++%3Fhabitante+espad%3Aactivo+%22true%22%5E%5E%3Chttp%3A%2F%2Fwww.w3.org%2F2001%2FXMLSchema%23boolean%3E+.%0D%0A++%3Fhabitante+schema%3AbirthDay+%3FfechaNacimiento+.%0D%0A++++bind+%28year%28now%28%29%29+-+year%28%3FfechaNacimiento%29+as+%3Fage%29+%0D%0A++++FILTER+%28%3Fage%3E%3D65%29%0D%0A+++++++%7D%0D%0A++++%7D%0D%0A%7D%0D%0A&format=text%2Fhtml&timeout=0&debug=on) se obtiene el porcentaje de sobreenvejecimiento del ayuntamiento.

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>

SELECT (count(distinct ?habitante) as ?poblacionMayor85), ?poblacionMayor65, ((count(distinct ?habitante)*100)/?poblacionMayor65 as ?indiceSobreenvejecimiento)
WHERE {
  ?habitante schema:birthDay ?birthDay .
  BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
    FILTER (?age>=85)
    {
       SELECT (count(?habitante) as ?poblacionMayor65)
       WHERE {
          ?habitante a espad:Habitante   .     
          ?habitante espad:activo "true"^^<http://www.w3.org/2001/XMLSchema#boolean> .
          ?habitante schema:birthDay ?birthDay .
          BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
          FILTER (?age>=65)
       }
    }
}
```

En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0A%0D%0ASELECT+%28count%28distinct+%3Fhabitante%29+as+%3FpoblacionJoven%29%2C+%3FpoblacionTotal%2C+%28%28count%28distinct+%3Fhabitante%29*100%29%2F%3FpoblacionTotal+as+%3FindiceInfancia%29%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+schema%3AbirthDay+%3FfechaNacimiento+.%0D%0A++++bind+%28year%28now%28%29%29+-+year%28%3FfechaNacimiento%29+as+%3Fage%29+%0D%0A++++FILTER+%28%3Fage%3E%3D0+%26%26+%3Fage%3C15%29%0D%0A++++%7B%0D%0A+++++++SELECT+%28count%28%3Fhabitante%29+as+%3FpoblacionTotal%29%0D%0A+++++++WHERE+%7B%0D%0A++++++++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A++++++++++%3Fhabitante+espad%3Aactivo+%22true%22%5E%5E%3Chttp%3A%2F%2Fwww.w3.org%2F2001%2FXMLSchema%23boolean%3E+.%0D%0A+++++++%7D%0D%0A++++%7D%0D%0A%7D&format=text%2Fhtml&timeout=0&debug=on) se obtiene el índice de infancia.

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>

SELECT (count(distinct ?habitante) as ?poblacionJoven), ?poblacionTotal, ((count(distinct ?habitante)*100)/?poblacionTotal as ?indiceInfancia)
WHERE {
  ?habitante schema:birthDay ?birthDay .
  BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
  FILTER (?age>=0 && ?age<15)
    {
       SELECT (count(?habitante) as ?poblacionTotal)
       WHERE {
          ?habitante a espad:Habitante   .     
          ?habitante espad:activo "true"^^<http://www.w3.org/2001/XMLSchema#boolean> .
       }
    }
}
```

En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0A%0D%0ASELECT+%28count%28distinct+%3Fhabitante%29+as+%3FpoblacionMenorDe15%29%2C+%3FpoblacionMayorDe64%2C+%28%28count%28distinct+%3Fhabitante%29*100%29%2F%3FpoblacionMayorDe64+as+%3FindiceJuventud%29%0D%0AWHERE+%7B%0D%0A++++%3Fhabitante+schema%3AbirthDay+%3FfechaNacimiento+.%0D%0A++++bind+%28year%28now%28%29%29+-+year%28%3FfechaNacimiento%29+as+%3Fage%29+%0D%0A++++FILTER+%28%3Fage%3E%3D0+%26%26+%3Fage%3C15%29%0D%0A++++%7B%0D%0A+++++++SELECT+%28count%28%3Fhabitante%29+as+%3FpoblacionMayorDe64%29%0D%0A+++++++WHERE+%7B%0D%0A++++++++++%3Fhabitante+a+espad%3AHabitante+++.+++++%0D%0A++++++++++%3Fhabitante+espad%3Aactivo+%22true%22%5E%5E%3Chttp%3A%2F%2Fwww.w3.org%2F2001%2FXMLSchema%23boolean%3E+.%0D%0A++++++++++%3Fhabitante+schema%3AbirthDay+%3FfechaNacimiento+.%0D%0A++++++++++bind+%28year%28now%28%29%29+-+year%28%3FfechaNacimiento%29+as+%3Fage%29+%0D%0A++++++++++FILTER+%28%3Fage%3E%3D64%29%0D%0A%0D%0A+++++++%7D%0D%0A++++%7D%0D%0A%7D&format=text%2Fhtml&timeout=0&debug=on) se obtiene el índice de juventud.

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>

SELECT (count(distinct ?habitante) as ?poblacionMenorDe15), ?poblacionMayorDe64, ((count(distinct ?habitante)*100)/?poblacionMayorDe64 as ?indiceJuventud)
WHERE {
  ?habitante schema:birthDay ?birthDay .
  BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
  FILTER (?age>=0 && ?age<15)
    {
       SELECT (count(?habitante) as ?poblacionMayorDe64)
       WHERE {
          ?habitante a espad:Habitante   .     
          ?habitante espad:activo "true"^^<http://www.w3.org/2001/XMLSchema#boolean> .
          ?habitante schema:birthDay ?fechaNacimiento .
          bind (year(now()) - year(?fechaNacimiento) as ?age)
          FILTER (?age>=64)

       }
    }
}
```

En la siguiente [consulta](http://ciudadesabiertas.linkeddata.es/sparql?default-graph-uri=http%3A%2F%2Fciudadesabiertas.linkeddata.es%2Fpadron-municipal&query=PREFIX+espad%3A+%3Chttp%3A%2F%2Fvocab.ciudadesabiertas.es%2Fdef%2Fdemografia%2Fpadron-municipal%23%3E%0D%0APREFIX+schema%3A+%3Chttp%3A%2F%2Fschema.org%2F%3E%0D%0A%0D%0ASELECT+%28count%28distinct+%3Fhabitante%29+as+%3FpoblacionMenorDe5%29%2C+%3FpoblacionMujeresEntre15y50%2C+%28%28count%28distinct+%3Fhabitante%29*100%29%2F%3FpoblacionMujeresEntre15y50+as+%3FindiceMaternidad%29%0D%0AWHERE+%7B%0D%0A++%3Fhabitante+schema%3AbirthDay+%3FbirthDay+.%0D%0A++BIND%28year%28NOW%28%29%29+-+year%28%3FbirthDay%29+-+IF%28month%28NOW%28%29%29%3Cmonth%28%3FbirthDay%29+%7C%7C+%28month%28NOW%28%29%29%3Dmonth%28%3FbirthDay%29+%26%26+day%28NOW%28%29%29%3Cday%28%3FbirthDay%29%29%2C1%2C0%29+as+%3Fage%29%0D%0A++FILTER+%28%3Fage%3E%3D0+%26%26+%3Fage%3C%3D5%29%0D%0A++++%7B%0D%0A+++++++SELECT+%28count%28%3Fhabitante%29+as+%3FpoblacionMujeresEntre15y50%29%0D%0A+++++++WHERE+%7B++++++++++%0D%0A++++++++++%3Fhabitante+espad%3Aactivo+%3Factivo+.%0D%0A++++++++++FILTER+%28%3Factivo%29%0D%0A++++++++++%3Fhabitante+schema%3AbirthDay+%3FfechaNacimiento+.%0D%0A++++++++++%3Fhabitante+schema%3Agender+%3Fsexo+.%0D%0A++++++++++bind+%28year%28now%28%29%29+-+year%28%3FfechaNacimiento%29+as+%3Fage%29%0D%0A++++++++++FILTER+%28%3Fage%3E15+%26%26+%3Fage%3C50+%26%26+%3Fsexo%3Dschema%3AFemale%29%0D%0A%0D%0A+++++++%7D%0D%0A++++%7D%0D%0A%7D&format=text%2Fhtml&timeout=0&debug=on&run=+Run+Query+) se obtiene el índice de maternidad.

```
PREFIX espad: <http://vocab.ciudadesabiertas.es/def/demografia/padron-municipal#>
PREFIX schema: <http://schema.org/>

SELECT (count(distinct ?habitante) as ?poblacionMenorDe5), ?poblacionMujeresEntre15y50, ((count(distinct ?habitante)*100)/?poblacionMujeresEntre15y50 as ?indiceMaternidad)
WHERE {
  ?habitante schema:birthDay ?birthDay .
  BIND(year(NOW()) - year(?birthDay) - IF(month(NOW())<month(?birthDay) || (month(NOW())=month(?birthDay) && day(NOW())<day(?birthDay)),1,0) as ?age)
  FILTER (?age>=0 && ?age<=5)
    {
       SELECT (count(?habitante) as ?poblacionMujeresEntre15y50)
       WHERE {          
          ?habitante espad:activo ?activo .
          FILTER (?activo)
          ?habitante schema:birthDay ?fechaNacimiento .
          ?habitante schema:gender ?sexo .
          bind (year(now()) - year(?fechaNacimiento) as ?age)
          FILTER (?age>15 && ?age<50 && ?sexo=schema:Female)

       }
    }
}
```
