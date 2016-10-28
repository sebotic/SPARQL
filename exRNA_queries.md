## Set of queries to retrieve data on extracellular RNAs from [Wikidata](https://www.wikidata.org) and to integrate them with data from [Wikipathways](www.wikipathways.org).

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
#### Get all mature miRNAs in Wikidata
[Execute](http://tinyurl.com/h5ct7qr)
```sparql
SELECT ?mirna ?mirnaLabel WHERE {
  ?mirna wdt:P279 wd:Q23838648.
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
```
