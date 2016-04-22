# A few SPARQL queries for Uniprot

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