## A set of SPARQL queries to retrieve and explore bibliography data in Wikidata and Colil


##### Retrieve the citation count of all papers cited by [Q37686905](https://www.wikidata.org/w/Q37686905)

```sparql
SELECT (COUNT(?cited) AS ?count) ?cited ?title ?pubdate WHERE {
 wd:Q37686905 wdt:P2860 ?cited .
  OPTIONAL {?cited2 wdt:P2860 ?cited .}
  OPTIONAL {?cited wdt:P577 ?pubdate .}
  OPTIONAL {?cited wdt:P1476 ?title .}
}
GROUP BY ?cited ?title ?pubdate
```
