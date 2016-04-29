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
