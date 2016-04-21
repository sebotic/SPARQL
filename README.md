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
