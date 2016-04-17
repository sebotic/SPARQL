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

##### A query to retrieve all Gene Ontology annotations for genes regulated by the human miRNA hsa-miR-127-3p (www.wikidata.org/wiki/Q23839066):

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
