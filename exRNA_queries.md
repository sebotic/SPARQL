## Set of queries to retrieve data on extracellular RNAs from [Wikidata](https://www.wikidata.org) and to integrate them with data from [Wikipathways](www.wikipathways.org).

#### Get all pre-miRNAs in Wikidata
[Execute](http://tinyurl.com/h4t5dxn)
```sparql
SELECT ?mirna ?mirnaLabel WHERE {
  ?mirna wdt:P31 wd:Q310899 .
  SERVICE wikibase:label {
    bd:serviceParam wikibase:language "en" .
  }
}
```
#### Get all mature miRNAs in Wikidata
[Execute](http://tinyurl.com/jrt3bao)
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
#### Retrieve all genes and mature miRNAs which are involved in the 'regulation of immune response' (GO:0050776)
[Execute](http://tinyurl.com/zwgpb9c)

```sparql
SELECT DISTINCT ?gene ?geneLabel ?mir ?mirLabel WHERE {
  ?protein wdt:P682 [wdt:P686 'GO:0050776'] . # regulation of immune response
  ?protein wdt:P702 ?gene .
  
  ?mir wdt:P128 ?gene .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en" .}
}
```
#### For these genes involved in regulation of immune reponse, are there drug which modulate the immune response, by targeting one of these gene?
[Execute](http://tinyurl.com/j79gvfq)
```sparql
SELECT DISTINCT ?gene ?geneLabel ?mir ?mirLabel ?drug ?drugLabel WHERE {
  ?x wdt:P682 [wdt:P686 'GO:0050776'] . # regulation of immune response
  ?x wdt:P702 ?gene .
  ?x wdt:P129 ?drug .
  
  ?mir wdt:P128 ?gene .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en" .}  
}
```
#### Genes regulated by hsa-miR-211-5p
[Execute](http://tinyurl.com/hmnl9la)
```sparql
SELECT ?gene ?geneLabel WHERE {
	?mirna rdfs:label 'hsa-miR-211-5p'@en .
    ?mirna wdt:P128 ?gene .
  
  	SERVICE wikibase:label { bd:serviceParam wikibase:language "en" .}
}
```
##### Biological processes impacted by hsa-miR-211-5p
[Execute](http://tinyurl.com/gnv2nj9)

```sparql
SELECT DISTINCT ?bioProcess ?bioProcessLabel WHERE {
	?mirna rdfs:label 'hsa-miR-211-5p'@en .
    ?mirna wdt:P128 ?gene .
    ?gene wdt:P688 ?protein .
    ?protein wdt:P682 ?bioProcess .
  
  	SERVICE wikibase:label { bd:serviceParam wikibase:language "en" .}
}
```
#### Retrieve all human genes which are involved in all bio-processes which are subclass of immune response as annotated by GO, based on these genes being regulated by hsa-miR-211-5p.
[Execute](http://tinyurl.com/zbxzlvo)
```sparql
SELECT DISTINCT ?geneLabel ?goLabel ?mirLabel WHERE {
  ?go wdt:P279* [wdt:P686 'GO:0006955'] . # immune response
  ?p ?pr ?go .
  ?p wdt:P702 ?gene .
  
  ?mir wdt:P128 ?gene .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en" .}  
}
GROUP BY ?goLabel ?mirLabel ?geneLabel
HAVING(?mirLabel = 'hsa-miR-211-5p'@en)
```
#### Same query as the previous, but also retrieving small molecules interacting with these gene products.
[Execute](http://tinyurl.com/zpxdn57)
```sparql
SELECT DISTINCT ?gene ?geneLabel ?mir ?mirLabel ?drug ?drugLabel WHERE {
  ?x wdt:P279* [wdt:P686 'GO:0006955'] . # regulation of immune response
  ?p ?pr ?x .
  ?p wdt:P702 ?gene .
  OPTIONAL { ?p wdt:P129 ?drug . }
  
  ?mir wdt:P128 ?gene . 
  
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en" .}
}
GROUP BY ?gene ?mir ?mirLabel ?geneLabel ?drug ?drugLabel
HAVING(?mirLabel = 'hsa-miR-211-5p'@en)
```

##### Retrieve Wikipathways pathways
```sparql
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

SELECT ?mirna ?gene ?pathway ?pLabel WHERE {
  ?target dc:identifier ?exact .
    ?target dcterms:isPartOf ?pathway .
    ?pathway a wp:Pathway .
    ?pathway <http://purl.org/dc/elements/1.1/title> ?pLabel .
  SERVICE <https://query.wikidata.org/sparql> {
    ?mirna rdfs:label 'hsa-miR-211-5p'@en .
    ?mirna wdt:P128 ?gene .
    ?gene wdt:P2888 ?exact filter (?exact = <http://identifiers.org/ncbigene/1234>)
  }

} LIMIT 10

```

##### Retrieve miRNAs involved in regulation of genes involved in postitive regulation of type 2 immune response (as annotated by Gene Ontology)
[Execute](http://tinyurl.com/jzhe2ba)

```sparql
SELECT DISTINCT ?gene ?geneLabel ?mir ?mirLabel WHERE {
  ?protein wdt:P682 wd:Q14907411 . # regulation of immune response
  ?protein wdt:P702 ?gene .
  
  ?mir wdt:P128 ?gene .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en" .}  
}
```