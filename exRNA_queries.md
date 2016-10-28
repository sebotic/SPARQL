# Set of queries to retrieve data on extracellular RNAs

#### Get all pre-miRNAs in Wikidata
[Execute](http://tinyurl.com/zdn4gpo)
```sparql
SELECT ?mirna ?mirnaLabel WHERE {
  ?mirna wdt:P279 wd:Q310899 .
  SERVICE wikibase:label {
    bd:serviceParam wikibase:language "en" .
  }
}
```
