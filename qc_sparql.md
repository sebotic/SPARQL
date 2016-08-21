## A set of quality control and maintenance queries for the Wikidata SPARQL endpoint (query.wikidata.org)

##### Check for items being human genes and instances of Wikipedia disambiguation pages at the same time:
[Execute](http://tinyurl.com/htjmtsj)

```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

SELECT * WHERE {
  ?gene wdt:P351 ?w .
  ?gene wdt:P31 wd:Q4167410 .
}
```

##### Retrieve all Wikidata items which share a MeSH ID. That can point to duplicate human disease items in Wikidata
[Execute](http://tinyurl.com/zlhg4e9)

```sparql
SELECT ?d2 ?mesh (COUNT(?mesh) + 1 as ?mesh_count) WHERE {
        ?d p:P486/ps:P486 ?mesh .
        ?d2 p:P486/ps:P486 ?mesh .

	FILTER (?d != ?d2)
}
GROUP BY ?d2 ?mesh
HAVING (?mesh_count > 1)
ORDER BY ?mesh
```

##### Get all Wikidata items with Gene Ontology IDs which miss the 'GO:' prefix 
[Execute](http://tinyurl.com/hher4vl)

```sparql
SELECT DISTINCT ?gene ?go WHERE {
   ?gene wdt:P686 ?go .
   FILTER(!REGEX(?go, "^GO:[0-9]", "i"))
}
```

##### Get all human or mouse proteins which lack a link to the gene they are encoded by
[Execute](http://tinyurl.com/jhwgunq)

```sparql
SELECT * WHERE {
	?p wdt:P352 ?up .
	{?p wdt:P703 wd:Q5} UNION {?p wdt:P703 wd:Q83310} .
  	FILTER NOT EXISTS {?p wdt:P702 ?enc}
}
```

##### Get mouse genes with HGNC gene symbol
[Execute](http://tinyurl.com/hydt8xf)

```sparql
SELECT * WHERE {
  	?g wdt:P353 ?hgnc_symbol .
	?g wdt:P703 wd:Q83310 .   
}
```

##### Get human genes with HGNC symbol but withou HGNC ID (this suggests that the HGNC symbol is wrong, or HGNC ID really missing)
[Execute](http://tinyurl.com/j6jevyp)

```sparql
SELECT * WHERE {
  	{?g wdt:P353 ?hgnc_symbol .} MINUS
	{?g wdt:P354 ?hgnc_id .}   
}
```

##### Get all compounds with CAS numbers which are not ['instance of'](http://www.wikidata.org/entity/P31)  or ['subclass of'](http://www.wikidata.org/entity/P279) a [chemical compound](http://www.wikidata.org/entity/Q11173)
[Execute](http://tinyurl.com/jt7p2n2)

```sparql
SELECT * WHERE {
  ?cmpnd wdt:P231 ?pc .
  FILTER NOT EXISTS{
	{?cmpnd wdt:P279 wd:Q11173 .} UNION
    {?cmpnd wdt:P31 wd:Q11173 .}
    }
}
```

##### Get all human gene Wikidata items with HGNC symbol but without HGNC gene ID
This suggests that the gene does not have a real HGNC symbol, but the one on the human gene item is actually wrong
[Execute](http://tinyurl.com/zdrtyd6)

```sparql
SELECT * WHERE {
	{?x wdt:P353 ?gs .} MINUS
	{?x wdt:P354 ?hgnc_id .}
  
  	{?x wdt:P703 wd:Q5 .} MINUS  
  	{?x wdt:P703 wd:Q83310 .} 
}
```
