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


#### Get all PDB IDs which are shared by more than one protein item
[execute](http://tinyurl.com/zasepo6)

```sparql
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

select ?u ?p Where {
	?p wdt:P638 ?u .
    ?p2 wdt:P638 ?u .
  
	filter (?p != ?p2)  
}

group by ?u ?p
```

#### Get all human and mouse gene Wikidata item IDs and the NCBI Entrez IDs
[execute](http://tinyurl.com/z6kfgrn)

```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

SELECT * WHERE {
	?p wdt:P351 ?entrez .
    {?p wdt:P703 wd:Q5}
    UNION {?p wdt:P703 wd:Q83310} .
}
```


#### Get all proteins with Gene Ontology annotation for biological process including all qualifiers
[execute](http://tinyurl.com/h7rjhsg)

```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>

SELECT ?p ?up ?s2 ?bioProcess ?qualifier WHERE {
      ?p wdt:P352 ?up .
      ?p wdt:P703 wd:Q5 .
      ?p p:P682 ?s2 .
 
  	  ?s2 ps:P682 ?bioProcess .
  	  ?s2 ?pr ?qualifier .
      
      FILTER(STRSTARTS(STR(?pr), "http://www.wikidata.org/prop/qualifier/"))

  
}
```

#### Retrieve all human protein (SwissProt) Gene Ontology annotations in Wikidata
[execute](http://tinyurl.com/hqcke55)

```sparql
SELECT * WHERE {
  {?p wdt:P680 ?go .} union
  {?p wdt:P681 ?go .} union
  {?p wdt:P682 ?go .} 
  ?p wdt:P703 wd:Q5 .
}
```

#### Retrieve all human and mouse Gene Ontology annotations
[execute](http://tinyurl.com/zc2l39z)

```sparql
SELECT ?p ?up ?molecular_function ?cellular_component ?biological_process WHERE {
	?p wdt:P352 ?up .
    {?p wdt:P703 wd:Q5} UNION
    {?p wdt:P703 wd:Q83310}
    
    {?p wdt:P680 ?molecular_function} union
    {?p wdt:P681 ?cellular_component} union
  	{?p wdt:P682 ?biological_process} 
}
ORDER BY ?p
```

#### Get all Gene Ontology IDs used for annotation of all human protein annotations item in Wikidata
[Execute]()

```sparql
SELECT * WHERE {
   ?p wdt:P279 wd:Q8054 .
   ?p wdt:P703 wd:Q5 .

  {?p wdt:P680 [wdt:P686 ?go] .} UNION
  {?p wdt:P681 [wdt:P686 ?go] .} UNION
  {?p wdt:P682 [wdt:P686 ?go] .}
}
```

#### Get all protein which [bind p53](https://www.wikidata.org/wiki/Q14817974) (as annotated by Gene Ontology) and the chemical compounds which can inhibit/modulate those proteins.

[Execute](http://tinyurl.com/zdfmot2)

```sparql
SELECT ?p ?pLabel ?chem_compound ?chem_compoundLabel WHERE {
	?p wdt:P680 wd:Q14817974 .
  	OPTIONAL { ?p wdt:P129 ?chem_compound . }
    
    SERVICE wikibase:label { bd:serviceParam wikibase:language "en" . } 
}
```
#### Get all PDB IDs for human proteins
[Execute](http://tinyurl.com/hkt7prq)

```sparql
SELECT * WHERE {
  ?p wdt:P703 wd:Q15978631. 
  ?p wdt:P352 ?uniprot .
  ?p wdt:P638 ?pdb.
}
```

#### Get all human proteins with have at least on PDB structure
[Execute](http://tinyurl.com/grf6qo5)

```sparql
SELECT DISTINCT ?p WHERE {
  ?p wdt:P703 wd:Q15978631.
  ?p wdt:P352 ?uniprot .
  ?p wdt:P638 ?pdb.
}
```
#### Get all human proteins which have a cellular component annotation in GO
[Execute](http://tinyurl.com/m25oj52)

```sparql
SELECT DISTINCT ?c ?cLabel ?component ?componentLabel WHERE {
  ?c wdt:P279 wd:Q8054.
  ?c wdt:P703 wd:Q15978631.
  ?c wdt:P681 ?component .
  
  SERVICE wikibase:label {
     		bd:serviceParam wikibase:language "en" .
  	} 
}

ORDER BY ?c
```
#### Get all GO terms of type 'Biological Process'
[Execute](http://tinyurl.com/lyue9wk)
```sparql
SELECT ?go ?goLabel WHERE {
  ?go wdt:P31 wd:Q2996394 .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en" . }
}
```

#### Retrieve all genes/proteins from all pathways and sort them by most genes per pathway
[Execute](http://tinyurl.com/y8ga85a6)

```sparql
SELECT ?p ?pLabel (count(?p) AS ?pcount) WHERE {
  ?p wdt:P2410 ?wikipathways_id .
  ?p wdt:P527 ?gene .
  ?gene wdt:P31 wd:Q7187 .

  SERVICE wikibase:label { bd:serviceParam wikibase:language "en" . }
}
GROUP BY ?p ?pLabel
ORDER BY DESC(?pcount) 
```
