## A set of quality control and maintenance queries for the Wikidata SPARQL endpoint (query.wikidata.org)

##### Check for items being human genes and instances of Wikipedia disambiguation pages at the same time:
[Execute](http://tinyurl.com/htjmtsj)

```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

SELECT * WHERE {
  ?gene wdt:P351 ?w .
  ?gene wdt:P31 wd:Q4167410 .
}
```

##### Retrieve all Wikidata items which share a MeSH ID. That can point to duplicate human disease items in Wikidata
[Execute](http://tinyurl.com/zlhg4e9)

```sparql
SELECT ?d2 ?mesh (COUNT(?mesh) + 1 as ?mesh_count) WHERE {
        ?d p:P486/ps:P486 ?mesh .
        ?d2 p:P486/ps:P486 ?mesh .

	FILTER (?d != ?d2)
}
GROUP BY ?d2 ?mesh
HAVING (?mesh_count > 1)
ORDER BY ?mesh
```

##### Get all Wikidata items with Gene Ontology IDs which miss the 'GO:' prefix 
[Execute](http://tinyurl.com/hher4vl)

```sparql
SELECT DISTINCT ?gene ?go WHERE {
   ?gene wdt:P686 ?go .
   FILTER(!REGEX(?go, "^GO:[0-9]", "i"))
}
```
