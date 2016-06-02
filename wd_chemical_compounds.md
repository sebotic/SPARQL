#### Some queries to explore chemical compounds in Wikidata

##### A query to retrieve all [chemical compounds](https://www.wikidata.org/wiki/Q11173)
Chemical compounds can either be [instance of](https://www.wikidata.org/wiki/Property:P31) or [subclass of](https://www.wikidata.org/wiki/Property:P279). On the long run, this should be unified to the semantically more appropriate '[subclass of](https://www.wikidata.org/wiki/Property:P279)' aka [is a](http://www.w3.org/2000/01/rdf-schema#subClassOf).

[execute](http://tinyurl.com/zbxdwyz)

```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT DISTINCT ?cmpnd ?label WHERE {
    {?cmpnd wdt:P279 wd:Q11173 .} UNION
  	{?cmpnd wdt:P31 wd:Q11173 .}
    ?cmpnd rdfs:label ?label .
}
```

Use Wikdata SPARQL label service instead, to get only English labels
[execute](http://tinyurl.com/jkevzwn)

```sparql
SELECT DISTINCT ?cmpnd ?cmpndLabel WHERE {
    {?cmpnd wdt:P279 wd:Q11173 .} UNION
  	{?cmpnd wdt:P31 wd:Q11173 .} 
    
  	SERVICE wikibase:label {
            bd:serviceParam wikibase:language "en" .
      }
}
```

##### Get all drug to drug-target interactions which have references indicating that they where impoorted from [Guide to Pharmacology](www.guidetopharmacology.com)
[execute](http://tinyurl.com/hwmuulb)

```sparql
PREFIX prov: <http://www.w3.org/ns/prov#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX r: <http://www.wikidata.org/prop/reference/>
PREFIX bd: <http://www.bigdata.com/rdf#>

SELECT ?compound ?compoundLabel  ?iuphar_id WHERE {
    ?compound wdt:P31 wd:Q11173 .
  	?compound p:P129 ?stmnt .
    
    ?stmnt prov:wasDerivedFrom ?ref .
    ?ref  r:P248 wd:Q2793172  .  
    ?ref  r:P595 ?iuphar_id  .  
  
     SERVICE wikibase:label {
        bd:serviceParam wikibase:language "en" .
    }
}
ORDER BY ?compoundLabel
```

##### Get all basic chemical identifiers for items with IUPHAR ID
[execute](http://tinyurl.com/gsmryjz)

```sparql
SELECT ?compound ?compoundLabel ?iuphar_id ?inchi_key ?inchi ?canonical_smiles WHERE {
    ?compound wdt:P595 ?iuphar_id .
    OPTIONAL {?compound wdt:P235 ?inchi_key .}
    OPTIONAL {?compound wdt:P234 ?inchi .}
    OPTIONAL {?compound wdt:P233 ?canonical_smiles .}

     SERVICE wikibase:label {
     	bd:serviceParam wikibase:language "en" .
     }
    
} ORDER BY ?compoundLabel
```
