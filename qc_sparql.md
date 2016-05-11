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
