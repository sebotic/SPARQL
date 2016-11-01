## Set of queries to retrieve data on extracellular RNAs from [Wikidata](https://www.wikidata.org) and to integrate them with data from [Wikipathways](www.wikipathways.org).

#### Get all pre-miRNAs in Wikidata
[Execute](http://tinyurl.com/zdn4gpo)
```sparql
SELECT ?mirna ?mirnaLabel WHERE {
  ?mirna wdt:P31 wd:Q310899 .
  SERVICE wikibase:label {
    bd:serviceParam wikibase:language "en" .
  }
}
```
#### Get all mature miRNAs in Wikidata
[Execute](http://tinyurl.com/h5ct7qr)
```sparql
SELECT ?mirna ?mirnaLabel WHERE {
  ?mirna wdt:P31 wd:Q23838648.
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
```

#### Also include the miRNA IDs in the result set
[Execute](http://tinyurl.com/hj8u5tf)
```sparql
SELECT ?mirna ?mirna_id ?mirnaLabel WHERE {
  ?mirna wdt:P31 wd:Q310899 .
  ?mirna wdt:P2870 ?mirna_id .
  SERVICE wikibase:label {
    bd:serviceParam wikibase:language "en" .
  }
}
```
####  Retrieve all mature miRNAs and their targets (names and NCBI entrez gene ID)
[Execute](http://tinyurl.com/jry2u84)

```sparql
SELECT DISTINCT ?mirna ?mirnaLabel ?gene ?geneLabel ?entrez WHERE {
  ?mirna wdt:P31 wd:Q23838648 ;
         wdt:P128 ?gene .
  ?gene wdt:P351 ?entrez .
  
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
```
