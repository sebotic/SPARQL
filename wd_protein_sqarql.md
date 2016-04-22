### A set of SPARQL queries operating on Wikidata genes and proteins

#### This query gets all human proteins which encode for more than 1 human protein
[execute](http://tinyurl.com/j3jxhy2)

```sparql
PREFIX wd: <http://www.wikidata.org/entity/> 
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX v: <http://www.wikidata.org/prop/statement/>

SELECT ?gene ?uniprot ?protein_aff ?total WHERE {
  {
  SELECT  ?uniprot (count(?uniprot) as ?total) WHERE {
  	?gene wdt:P703 wd:Q5 ;
          wdt:P279 wd:Q7187 ;
  		  wdt:P688 ?protein . 
  	?protein wdt:P352 ?uniprot ;
             wdt:P703 wd:Q5 .
 	}
    Group BY ?uniprot
  }
  
  ?protein_aff wdt:P352 ?uniprot .
  #?gene wdt:P688 ?protein_aff ;
  #wdt:P703 wd:Q5 .
  
  FILTER (?total > 1)
 }
 ORDER BY ?gene

```

#### Retrieve all Wikidata items which are subclass of gene and protein at the same time
[execute](http://tinyurl.com/jetbwwk)

```sparql
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

SELECT * WHERE {
  ?gene wdt:P279 wd:Q7187 .
  ?gene wdt:P279 wd:Q8054 .
 }
```

#### Retrieve all Wikidata items which are have a Uniprot ID and an Entrez gene ID at the same time
[execute](http://tinyurl.com/jgqone3)

```sparql
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

SELECT * WHERE {
  ?some_item p:P351 ?entrez_gene_id .
  ?some_item p:P352 ?uniprot .
}
```


#### Get all human and mouse proteins based on the Uniprot ID
[execute](http://tinyurl.com/z94m7an)

```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

        SELECT * WHERE {
	        ?protein wdt:P352 ?uniprot .
            {?protein wdt:P703 wd:Q5}
            UNION {?protein wdt:P703 wd:Q83310} .
        }
```
