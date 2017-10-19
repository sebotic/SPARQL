## A set of SPARQL queries to retrieve and explore bibliography data in Wikidata and Colil


#### Retrieve the citation count of all papers cited by [Q37686905](https://www.wikidata.org/wiki/Q37686905)
[Execute](http://tinyurl.com/ydfeqtqk)

```sparql
SELECT (COUNT(?cited) AS ?count) ?cited ?title ?pubdate WHERE {
 wd:Q37686905 wdt:P2860 ?cited .
  OPTIONAL {?cited2 wdt:P2860 ?cited .}
  OPTIONAL {?cited wdt:P577 ?pubdate .}
  OPTIONAL {?cited wdt:P1476 ?title .}
}
GROUP BY ?cited ?title ?pubdate
```

#### Filter list of Wikidata QIDs for being instance of a publication, retrieve additional info about the publications
[Execute](http://tinyurl.com/ya4nkmwx)

```sparql
 SELECT DISTINCT ?c ?pmid ?doi ?title WHERE {
  {
    SELECT * WHERE {
      VALUES ?c { wd:Q21083261 wd:Q38735563 wd:Q38375220 wd:Q38610320 wd:Q38378516 wd:Q38855950 wd:Q39272798 wd:Q38920328 }
      ?c wdt:P31 wd:Q13442814 . 
    }
  } UNION 
  {
    SELECT * WHERE {
      ?c wdt:P698 'AVELUMAB' . 
    }
  } 
             
  OPTIONAL { ?c wdt:P698 ?pmid . }
  OPTIONAL { ?c wdt:P356 ?doi . }
  OPTIONAL { ?c wdt:P1476 ?title . }
}
```

#### SPARQL query to get a specific set of data for a publication
[Execute](http://tinyurl.com/y992rslf)

```sparql
SELECT * WHERE {
  VALUES ?props {wdt:P698 wdt:P932 wdt:P1476 wdt:P2093 wdt:P50 wdt:P356 wdt:P577 wdt:P433 wdt:P304 wdt:P478 wdt:P1433 wdt:P932}   
  wd:Q37658381 ?props ?v .
  wd:Q37658381 wdt:P1433 ?journal .
  ?journal wdt:P1476 ?journal_title . 
}
```
