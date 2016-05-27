# SPARQL
collection of SPARQL queries

##### A query to retrieve all protein coding genes regulated by human miRNA hsa-miR-127-3p: 

```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

select distinct * where {
    ?qid rdfs:label 'hsa-miR-127-3p'@en .
	?qid wdt:P128 ?gene .
  	?gene rdfs:label ?label filter (lang(?label) = "en") .
}

```
[Execute](http://tinyurl.com/zmz4ekt)


##### A query to retrieve all Gene Ontology annotations for genes regulated by the human miRNA [hsa-miR-127-3p](https://www.wikidata.org/wiki/Q23839066):

```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

select distinct ?protein ?label ?go_id ?go_label ?go where {
	wd:Q23839066 wdt:P128 [wdt:P688 ?protein] .
  	?protein wdt:P681 ?go .
  	?protein rdfs:label ?label filter (lang(?label) = "en") .
  	?go wdt:P686 ?go_id .
  	?go rdfs:label ?go_label filter (lang(?go_label) = "en") .
} order by ?label

```
[Execute](http://tinyurl.com/gpj48ps)

##### A query to retrieve all property names present on a gene with NCBI entrez gene ID (in this case FOXP3). This is using standard wikidata namespace.

Using the wikibase: namespace this can be done more efficiently, as id does not require the string operations to get to the entity (wd:) namespace.

```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT  *  WHERE {
  {
   	SELECT DISTINCT (SUBSTR(str(?prop), 37) as ?prop_nr) WHERE {
     		VALUES ?geneID {"50943"}
     		?gene wdt:P351 ?geneID ;
     		?prop ?value .
     		FILTER (SUBSTR(str(?prop), 1, 36) = 'http://www.wikidata.org/prop/direct/')
     	}     
  }
  
  BIND(IRI(CONCAT("http://www.wikidata.org/entity/", ?prop_nr)) as ?prop)
  
  {
   	SELECT * WHERE {
  		?prop rdfs:label ?propLabel FILTER (LANG(?propLabel) = "en") .
   	}
  }
}
```

The simplified version using the wikibase namespace:
```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wikibase: <http://wikiba.se/ontology#>

SELECT DISTINCT ?prop ?pLabel WHERE {
	VALUES ?geneID {"50943"}
        ?gene wdt:P351 ?geneID ;
        	?prop ?value .
      	?p wikibase:directClaim ?prop .
        SERVICE wikibase:label {
     		bd:serviceParam wikibase:language "en" .
  	}  
}     
```


##### Count all triples in a triple store:
[Execute](http://tinyurl.com/j8h5rxh) (on query.wikidata.org)

```sparql

select (count(*) as ?count) where {
	?s ?p ?o .
}

```

##### Get the latest Wikidata revision ID for all human genes :
[Execute](http://tinyurl.com/jjgkqr4)

```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX schema: <http://schema.org/>

SELECT ?gene ?revision WHERE {
	?gene wdt:P351 ?entrez . 
  	?gene wdt:P703 wd:Q5 .
    ?gene schema:version ?revision . 
}
```

##### Get all human genes on chromosome 9 with a start position between 21 MB and 30 MB.
[Execute](http://tinyurl.com/zgyqsmk)
This query can be potentially useful for e.g. quick annotation of copy number abberations.

```sparql
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX wd: <http://www.wikidata.org/entity/> 
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX q: <http://www.wikidata.org/prop/qualifier/>
PREFIX v: <http://www.wikidata.org/prop/statement/>

SELECT distinct ?gene ?geneLabel ?start ?stop WHERE {
  ?gene wdt:P1057 wd:Q840604 .
  ?gene p:P644 ?statement .
  ?statement v:P644 ?start .
  ?statement q:P659 wd:Q20966585 . 
  
  ?gene p:P645 ?statement2 .
  ?statement2 v:P645 ?stop .
  ?statement2 q:P659 wd:Q20966585 . 
  
  SERVICE wikibase:label {
        bd:serviceParam wikibase:language "en" .
  }
  
  FILTER (xsd:integer(?start) > 21000000 && xsd:integer(?start) < 30000000)
}
ORDER BY ?geneLabel
```

##### Get counts for all human gene subclasses
[Execute](http://tinyurl.com/j4z5kzf)

```sparql
SELECT DISTINCT ?subc ?subcLabel (COUNT(?subc) as ?count) WHERE {
    ?g wdt:P351 ?entrez .
    ?g wdt:P703 wd:Q5 .
  
    ?g wdt:P279 ?subc .
  
    SERVICE wikibase:label {
    	bd:serviceParam wikibase:language "en" .
    }  
}
GROUP BY ?subc ?subcLabel
```
