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

<br/>
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

<br/>
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

<br/>
##### Count all triples in a triple store:
[Execute](http://tinyurl.com/j8h5rxh) (on query.wikidata.org)

```sparql

select (count(*) as ?count) where {
	?s ?p ?o .
}

```
<br/>
##### Get all statement properties of a certain item, here the gene with Entrez ID 50943
[Execute](http://tinyurl.com/jtydugn)

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
   SELECT DISTINCT (SUBSTR(str(?prop), 37) AS ?prop_nr) WHERE {
     VALUES ?geneID {"50943"}
     ?gene wdt:P351 ?geneID ;
       ?prop ?value .
     FILTER (SUBSTR(str(?prop), 1, 36) = 'http://www.wikidata.org/prop/direct/')
     }     
  }
  BIND(IRI(CONCAT("http://www.wikidata.org/entity/", ?prop_nr)) as ?prop)
  {
  	SELECT * WHERE {
  		?prop rdfs:label ?propLabel filter (lang(?propLabel) = "en") .
   	}
  }
}
```
</br>
#### Get all property IDs with an entity URI for 2 different compounds
[Execute](http://tinyurl.com/zjs6ax2)

```sparql
SELECT DISTINCT ?property ?propLabel WHERE {
  VALUES ?v {wd:Q407431 wd:Q153}
  ?v ?prop ?vals FILTER (!(SUBSTR(str(?vals), 1, 41) = 'http://www.wikidata.org/entity/statement/')).
  ?property ?ref ?prop .
  ?property rdfs:label ?propLabel FILTER (LANG(?propLabel) = 'en') .
}

```

</br>
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
<br/>
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
<br/>
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
<br/>
##### Get all databases and bio-databases which are in Wikidata
Classified by instance of (P31) (most of them) or subclass of (P279) (a few).

[Execute](http://tinyurl.com/zvfo86t)

```sparql
SELECT DISTINCT ?d ?dLabel WHERE {
	{?d wdt:P31 wd:Q8513 .} UNION
	{?d wdt:P31 wd:Q4117139 .} UNION
	{?d wdt:P279 wd:Q8513 .} UNION
	{?d wdt:P279 wd:Q4117139 .}
  
	SERVICE wikibase:label {
		bd:serviceParam wikibase:language "en" .
	}
}
```
<br/>
##### Get all values with property number and property label on an item (here, [Indole](https://www.wikidata.org/wiki/Q319541))
[Execute](http://tinyurl.com/hzn6v7d)

```sparql
SELECT ?vals ?property ?propLabel WHERE {
  wd:Q319541 ?prop ?vals FILTER (!(SUBSTR(str(?vals), 1, 41) = 'http://www.wikidata.org/entity/statement/')).
  ?property ?ref ?prop .
  ?property rdfs:label ?propLabel FILTER (LANG(?propLabel) = 'en') .
}
```
<br/>  
##### Get all items 4 nodes away from a WD item and all edges in between (here, [Vemurafenib](https://www.wikidata.org/wiki/Q423111))
[Execute](http://tinyurl.com/hwr2hos)

Uncommenting the second filter will only return the edges which lead from Q423111 to Q925809. The limit can be removed, but then, the query will not finish in a reasonable time if executed in a browser. With a Python script, the query returns a json object with 226 MB size within a few minutes (as of 07/11/2016). 

```sparql
SELECT *  WHERE {
	?c ?p4 [?p3 [?p2 [?p1 wd:Q423111]]] #FILTER (?c = <http://www.wikidata.org/entity/Q925809>)
       FILTER((SUBSTR(str(?c), 1, 32) = 'http://www.wikidata.org/entity/Q'))
}
LIMIT 100000
```
