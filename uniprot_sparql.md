# A few SPARQL queries for Uniprot

The full Uniprot RDF schema can be found [here](http://www.uniprot.org/format/uniprot_rdf)

#### Retrieve the Uniprot IDs for Swissprot entries based on the Entrez gene ID 1029 (CDKN2A)
[execute](http://sparql.uniprot.org/sparql/?format=html&query=PREFIX+up%3A%3Chttp%3A%2F%2Fpurl.uniprot.org%2Fcore%2F%3E+%0D%0APREFIX+rdfs%3A%3Chttp%3A%2F%2Fwww.w3.org%2F2000%2F01%2Frdf-schema%23%3E+%0D%0A%0D%0A%0D%0A++++SELECT+DISTINCT+%3Funiprot+%3Fgene+WHERE+{%0D%0A++++++++BIND%28IRI%28CONCAT%28%22http%3A%2F%2Fpurl.uniprot.org%2Fgeneid%2F%22%2C+%271029%27%29%29+as+%3Fgene%29%0D%0A++++++++%3Funiprot+rdfs%3AseeAlso+%3Fgene+.%0D%0A++++++++%3Funiprot+up%3Areviewed+%22true%22^^xsd%3Aboolean+.%0D%0A++++}%0D%0A++++GROUP+BY+%3Funiprot+%3Fgene)

This will currently return 2 Uniprot IDs, one for the protein p16INK4A and one for p14ARF.

```sparql
PREFIX up:<http://purl.uniprot.org/core/> 
PREFIX rdfs:<http://www.w3.org/2000/01/rdf-schema#> 

    SELECT DISTINCT ?uniprot ?gene WHERE {
        BIND(IRI(CONCAT("http://purl.uniprot.org/geneid/", '1029')) as ?gene)
        ?uniprot rdfs:seeAlso ?gene .
        ?uniprot up:reviewed "true"^^xsd:boolean .
    }
    GROUP BY ?uniprot ?gene
```

#### Get all triples the given Uniprot ID is part of
[execute](http://sparql.uniprot.org/sparql/?format=html&query=describe+%3Chttp%3A%2F%2Fpurl.uniprot.org%2Funiprot%2FP04637%3E&format=srj)

```sparql
describe <http://purl.uniprot.org/uniprot/P04637>
```

#### Get all triples the given Uniprot ID is the subject of
[execute](http://sparql.uniprot.org/sparql/?format=html&query=++++SELECT+DISTINCT+*+WHERE+{%0D%0A%09%09BIND%28IRI%28%3Chttp%3A%2F%2Fpurl.uniprot.org%2Funiprot%2FP42771%3E%29+as+%3Funiprot%29%0D%0A++++++++%23%3Funiprot+rdfs%3AseeAlso+%3Fgene++.%0D%0A++++++++%23%3Funiprot+up%3Areviewed+%22true%22^^xsd%3Aboolean+.%0D%0A++++++%09%3Funiprot+%3Fx+%3Fy++.%0D%0A++++}%0D%0A++++GROUP+BY+%3Funiprot+%3Fx+%3Fy)

```sparql
    SELECT DISTINCT * WHERE {
		BIND(IRI(<http://purl.uniprot.org/uniprot/P42771>) as ?uniprot)
        #?uniprot rdfs:seeAlso ?gene  .
        #?uniprot up:reviewed "true"^^xsd:boolean .
      	?uniprot ?x ?y  .
    }
    GROUP BY ?uniprot ?x ?y
```

#### Get all Swissprot entries for human and mouse with the mapping to NCBI Entrez gene IDs
[execute](http://sparql.uniprot.org/sparql/?format=html&query=PREFIX+taxon%3A%3Chttp%3A%2F%2Fpurl.uniprot.org%2Ftaxonomy%2F%3E+%0D%0APREFIX+up%3A%3Chttp%3A%2F%2Fpurl.uniprot.org%2Fcore%2F%3E+%0D%0APREFIX+rdf%3A%3Chttp%3A%2F%2Fwww.w3.org%2F1999%2F02%2F22-rdf-syntax-ns%23%3E+%0D%0A%0D%0A%0D%0A++++SELECT+DISTINCT+*+WHERE+{%0D%0A++++++++%3Funiprot+rdfs%3AseeAlso+%3Fgene+.%0D%0A++++++%09%3Funiprot+up%3Areviewed+%3Freviewed+.%0D%0A++++++%09{%3Funiprot+up%3Aorganism+taxon%3A9606}%0D%0A++++++%09UNION+{%3Funiprot+up%3Aorganism+taxon%3A10090}+.%0D%0A%0D%0A++++++%09FILTER+regex%28%3Fgene%2C+%22^http%3A%2F%2Fpurl.uniprot.org%2Fgeneid%2F%22%29+%0D%0A++++}%0D%0A++++GROUP+BY+%3Funiprot+%3Fgene+%3Freviewed)

```sparql
PREFIX taxon:<http://purl.uniprot.org/taxonomy/> 
PREFIX up:<http://purl.uniprot.org/core/> 
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#> 


    SELECT DISTINCT * WHERE {
        ?uniprot rdfs:seeAlso ?gene .
      	?uniprot up:reviewed ?reviewed .
      	{?uniprot up:organism taxon:9606}
      	UNION {?uniprot up:organism taxon:10090} .

      	FILTER regex(?gene, "^http://purl.uniprot.org/geneid/") 
    }
    GROUP BY ?uniprot ?gene ?reviewed
```


#### A federated query to get all human proteins with 'colorectal cancer' in their Uniprot disease annotation.
The query currently only works on [sparql.uniprot.org](sparql.uniprot.org), because [query.wikidata.org](query.wikidata.org]) does not allow federated queries. The query selects for GRCh38 genomic coordinates.

```sparql
PREFIX up: <http://purl.uniprot.org/core/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX v: <http://www.wikidata.org/prop/statement/>
PREFIX q: <http://www.wikidata.org/prop/qualifier/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>


SELECT ?gene ?geneLabel ?wdncbi ?start ?stop ?disease_text WHERE {    
    SERVICE <https://query.wikidata.org/sparql> {    

        ?gene wdt:P351 ?wdncbi ;
              wdt:P703 wd:Q15978631;
              rdfs:label ?geneLabel ;
              p:P644 ?geneLocStart ;
              p:P645 ?geneLocStop ;
              wdt:P688 ?wd_protein .
              
        ?geneLocStart v:P644 ?start .
        ?geneLocStart q:P659 wd:Q20966585 . 
              
        ?geneLocStop v:P645 ?stop .
        ?geneLocStop q:P659 wd:Q20966585 . 
  
        ?wd_protein wdt:P352 ?uniprot_id ;
                    wdt:P681 ?go_term .
        ?go_term wdt:P686 "GO:0016020" .

		FILTER (LANG(?geneLabel) = "en") .
    }
    BIND(IRI(CONCAT("http://purl.uniprot.org/uniprot/", ?uniprot_id)) as ?protein)
        ?protein up:annotation ?annotation .
        ?annotation a up:Disease_Annotation .
        ?annotation up:disease ?disease_annotation .
        ?disease_annotation <http://www.w3.org/2004/02/skos/core#prefLabel> ?disease_text .
    FILTER(REGEX(?disease_text, "Colorectal cancer", "i"))
}
```

#### Get all human proteins and their full labels from Uniprot
[Execute](http://tinyurl.com/lvptyxk)

```sparql
PREFIX up:<http://purl.uniprot.org/core/> 
PREFIX taxon:<http://purl.uniprot.org/taxonomy/> 
PREFIX skos:<http://www.w3.org/2004/02/skos/core#> 

SELECT ?protein ?name WHERE
{
	?protein a up:Protein .
  	?protein up:organism taxon:9606 .
	?protein up:recommendedName ?recommended .
	?recommended up:fullName ?name .
	 # ?protein <http://www.w3.org/2000/01/rdf-schema#label> ?label . #this can be used as a quick way to get to the label
}
```
#### Get all human proteins and their full labels from Uniprot (alternative way)
[Execute](http://tinyurl.com/y84wktoq)
```sparql
PREFIX up:<http://purl.uniprot.org/core/> 
PREFIX taxon:<http://purl.uniprot.org/taxonomy/> 
PREFIX skos:<http://www.w3.org/2004/02/skos/core#> 

SELECT ?protein ?label WHERE
{
	?protein a up:Protein .
  	?protein up:organism taxon:9606 .
	?protein up:recommendedName ?recommended .
	
	?protein <http://www.w3.org/2000/01/rdf-schema#label> ?label . 
}
```

#### Get all human and mouse proteins with annotation for celluar localization and membrane protein type, if available.
[Execute](http://tinyurl.com/y8nel2lm)
```sparql
PREFIX up:<http://purl.uniprot.org/core/> 
PREFIX taxon:<http://purl.uniprot.org/taxonomy/> 
PREFIX skos:<http://www.w3.org/2004/02/skos/core#> 

SELECT distinct 
?protein ?taxon ?taxonLabel ?topology ?topologyLabel ?cellularComponent ?cellularComponentLabel

WHERE
{
  ?protein a up:Protein .
  {?protein up:organism taxon:9606 .} 
  UNION
  {?protein up:organism taxon:10090 .}
  
  ?protein up:organism ?taxon .
  ?taxon up:scientificName ?taxonLabel .
  ?protein up:annotation/up:locatedIn ?annotation .
  OPTIONAL {
    ?annotation up:topology ?topology .
    ?topology skos:prefLabel ?topologyLabel . 
  }
  OPTIONAL {
    ?annotation up:cellularComponent ?cellularComponent . 
    ?cellularComponent skos:prefLabel ?cellularComponentLabel .
  }
}
```
#### Same as above, but with ECO evidence codes
[Execute](http://tinyurl.com/y795wcgu)

```sparql
PREFIX up:<http://purl.uniprot.org/core/> 
PREFIX taxon:<http://purl.uniprot.org/taxonomy/> 
PREFIX skos:<http://www.w3.org/2004/02/skos/core#> 

SELECT distinct 
?protein ?taxon ?taxonLabel ?topology ?topologyLabel ?cellularComponent ?cellularComponentLabel ?evidence

WHERE
{
  ?protein a up:Protein .
  {?protein up:organism taxon:9606 .} 
  UNION
  {?protein up:organism taxon:10090 .}
  
  ?protein up:attribution ?attribution .
  ?attribution up:evidence ?evidence .
  
  ?protein up:organism ?taxon .
  ?taxon up:scientificName ?taxonLabel .
  ?protein up:annotation/up:locatedIn ?annotation .
  OPTIONAL {
    ?annotation up:topology ?topology .
    ?topology skos:prefLabel ?topologyLabel . 
  }
  OPTIONAL {
    ?annotation up:cellularComponent ?cellularComponent . 
    ?cellularComponent skos:prefLabel ?cellularComponentLabel .
  }
}
```

#### Same as above, but all annotation and attribution nodes included in the result set.
[Execute](http://tinyurl.com/ycb4bzvo)

```sparql
PREFIX up:<http://purl.uniprot.org/core/> 
PREFIX taxon:<http://purl.uniprot.org/taxonomy/> 
PREFIX skos:<http://www.w3.org/2004/02/skos/core#> 

SELECT distinct 
?protein ?taxon ?taxonLabel ?annotation ?topology ?topologyLabel ?cellularComponent ?cellularComponentLabel ?attribution ?evidence

WHERE
{
  ?protein a up:Protein .
  {?protein up:organism taxon:9606 .} 
  UNION
  {?protein up:organism taxon:10090 .}
  
  ?protein up:attribution ?attribution .
  ?attribution up:evidence ?evidence .
  
  ?protein up:organism ?taxon .
  ?taxon up:scientificName ?taxonLabel .
  ?protein up:annotation/up:locatedIn ?annotation .
  OPTIONAL {
    ?annotation up:topology ?topology .
    ?topology skos:prefLabel ?topologyLabel . 
  }
  OPTIONAL {
    ?annotation up:cellularComponent ?cellularComponent . 
    ?cellularComponent skos:prefLabel ?cellularComponentLabel .
  } 
}
```
