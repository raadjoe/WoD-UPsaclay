# Queries used in the last WoD course


1. Query for linking countries in our local (GraphDB) dataset to countries in DBpedia
```
PREFIX dbo: <http://dbpedia.org/ontology/>
PREFIX dbp: <http://dbpedia.org/property/>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX ex: <http://example.com/upsaclay/wod/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
INSERT {
    ?countryWOD owl:sameAs ?countryDBP ;
                ex:population ?population .
} WHERE {
    SERVICE <https://dbpedia.org/sparql/> {
        ?countryDBP rdf:type dbo:Country ;
                    rdfs:label ?countryDBPlabel
        { ?countryDBP dbo:populationTotal ?population }
        UNION
        { ?countryDBP dbp:populationCensus ?population }
        FILTER(LANG(?countryDBPlabel) = "" || LANGMATCHES(LANG(?countryDBPlabel), "en"))
    }
    ?countryWOD rdfs:label ?countryWODlabel .
    FILTER (STR(?countryWODlabel) = STR(?countryDBPlabel))
}
```


2. Query for listing the most efficient countries in winning summer Olympics medals with respect to their population
```
PREFIX ex: <http://example.com/upsaclay/wod/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT DISTINCT ?country (MIN(?individualsPerMedal) AS ?normalisedMedals)
WHERE {
    ?countryIRI ex:medalsWon ?medals ;
             rdfs:label ?country ;
             ex:population ?population
    BIND(ROUND(?population/?medals) AS ?individualsPerMedal)
    FILTER (?medals > 300)
} GROUP BY (?country)
ORDER BY ?normalisedMedals
```
