#### A 4 step split query to get from a disease to a drug and the biochemical effects of the drug
A few simple queries to show the power of Wikidata. Depending on the use case, the queries can just combined into one.


##### Give me all drugs which can be used to treat melanoma:
[Execute](http://tinyurl.com/z6zs8f6)


```sparql
SELECT ?pot_drugs ?pot_drugsLabel WHERE {
	wd:Q180614 wdt:P2176 ?pot_drugs . # give me all drugs available for melanoma
	
  	SERVICE wikibase:label {
            bd:serviceParam wikibase:language "en" .
    }  
}
```

##### Give me all drug targets for vemurafenib
[Execute](http://tinyurl.com/hkvmc4p)


```sparql
SELECT ?drug_target ?drug_targetLabel WHERE {
	wd:Q423111 wdt:P129 ?drug_target . # give me all drugs targets of vemurafenib
	
  	SERVICE wikibase:label {
            bd:serviceParam wikibase:language "en" .
    }  
}
```

##### Give me all Gene Ontology annotations of the drugs targeted by vemurafenib
[Execute](http://tinyurl.com/zcdvyae)

```sparql
SELECT ?drug_target ?drug_targetLabel ?go_itemLabel ?go_term WHERE {
	wd:Q423111 wdt:P129 ?drug_target . # for vemurafenib, give me all drug targets
  	?drug_target wdt:P682 ?go_item .  # give me all GO biological process annotations for the targets
  	?go_item wdt:P686 ?go_term
	
  	SERVICE wikibase:label {
            bd:serviceParam wikibase:language "en" .
    }  
}
ORDER BY ?drug_targetLabel
```

##### Finally, give me all drugs that target the gene product of BRAF, one of the targets of vemurafenib
[Execute](http://tinyurl.com/jt9s6tb)

```sparql
SELECT ?inhibitor ?inhibitorLabel WHERE {
	?gene wdt:P351 '673' . # get the gene item with entrez ID 673
  	?gene wdt:P688/wdt:P129 ?inhibitor .
  
  	SERVICE wikibase:label {
            bd:serviceParam wikibase:language "en" .
    }      
    
}
```

#### All diseases which can potentially be treated by the monoclonal antibody nivolumab
[Execute](http://tinyurl.com/hjfwoj5)

```sparql
SELECT ?disease ?diseaseLabel WHERE {
	wd:Q7041828 wdt:P2175 ?disease . # give me all diseases which can be treated by nivolumab
	
  	SERVICE wikibase:label {
            bd:serviceParam wikibase:language "en" .
    }  
}
```

#### Get all items with a UNII but without a NDF-RT NUI
[Execute](http://tinyurl.com/jtgesp8)

```sparql
SELECT * WHERE {
  {?x wdt:P652 ?unii .} MINUS 
  {?x wdt:P2115 ?nui .}
}
```

#### Get all FDA approved monoclonal antibody items in Wikidata
[Execute](http://tinyurl.com/zf95wl7)

```sparql
SELECT ?mab ?mabLabel WHERE {
	{?mab wdt:P279 wd:Q422248 .} UNION
  	{?mab wdt:P31 wd:Q422248 .}

	SERVICE wikibase:label {
    	bd:serviceParam wikibase:language "en" .
    	bd:serviceParam wikibase:language "de" .
	}
}
```

#### Get all monoclonal antibody items in Wikidata
This query is based on the assumption that all items of [instance of](http://www.wikidata.org/entity/P31) [chemical compound](http://www.wikidata.org/entity/Q11173) or [monoclonal antibody](http://www.wikidata.org/entity/Q12140) who's label ends with 'mab' is a monoclonal antibody.
[Execute](http://tinyurl.com/jy6ynxp)

```sparql
SELECT DISTINCT ?ab ?abLabel where {
	{?ab wdt:P31 wd:Q12140 .} UNION
  	{?ab wdt:P31 wd:Q11173 . }
    ?ab rdfs:label ?abLabel filter (lang(?abLabel) = "en" || lang(?abLabel) = "de") .	
  	    SERVICE wikibase:label {
        bd:serviceParam wikibase:language "en" .
        bd:serviceParam wikibase:language "de" .
    }
  
  FILTER (STRENDS(?abLabel, 'mab'))
}
GROUP BY ?ab ?abLabel
```
#### Get all monoclonal antibodies with revevant external IDs, based on categorization with 'instance of' [monoclonal antibody](http://www.wikidata.org/entity/Q12140)
[Execute](http://tinyurl.com/hmfbvo3)

```sparql
SELECT ?ab ?abLabel ?cas ?gtp ?chembl WHERE {
  ?ab wdt:P31 wd:Q422248.
  OPTIONAL { ?ab wdt:P231 ?cas. }
  OPTIONAL { ?ab wdt:P595 ?gtp. }
  OPTIONAL { ?ab wdt:P592 ?chembl. }
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
```
#### Get all monoclonal antibodies which could be used for treatment of melanoma
[Execute](http://tinyurl.com/zn9zvg8)
```sparql
SELECT ?ab ?abLabel ?cas ?gtp ?chembl WHERE {
  ?ab wdt:P31 wd:Q422248.
  ?ab wdt:P2175 wd:Q180614 .
  OPTIONAL { ?ab wdt:P231 ?cas. }
  OPTIONAL { ?ab wdt:P595 ?gtp. }
  OPTIONAL { ?ab wdt:P592 ?chembl. }
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
```

#### Get all monoclonal antibodies which might treat melanoma, include references where knowledge has been imported from
[Execute](http://tinyurl.com/jollvbs)
```sparql
SELECT ?drugLabel ?drug ?ref_id ?ref_db ?ref_dbLabel ?ref_date WHERE {
  ?drug wdt:P2175 wd:Q180614 ;
  		wdt:P31 wd:Q422248 . 
    SERVICE wikibase:label {
        bd:serviceParam wikibase:language "en" .
    }
  
   OPTIONAL {
     ?drug p:P2175 ?s2 .
     ?s2 prov:wasDerivedFrom ?v .
     ?v <http://www.wikidata.org/prop/reference/P592>|<http://www.wikidata.org/prop/reference/P2115> ?ref_id .
     ?v <http://www.wikidata.org/prop/reference/P248> ?ref_db .
     ?v <http://www.wikidata.org/prop/reference/P813> ?ref_date .
  }
}
group by ?drug ?drugLabel ?ref_id ?ref_db ?ref_dbLabel ?ref_date
```
#### Get all drugs that can be used for treatment of [asthma](http://www.wikidata.org/entity/Q35869), then get the protein targets and other drugs these targets could be modified by. Also return the type of interaction.
[Execute](http://tinyurl.com/jna6n46)

```sparql
PREFIX q: <http://www.wikidata.org/prop/qualifier/>

SELECT DISTINCT ?drug ?drugLabel  ?protein ?proteinLabel ?drug2 ?drug2Label ?v ?vLabel WHERE {
	wd:Q35869 wdt:P2176 ?drug .
    ?drug wdt:P2175 ?condition .
    ?drug wdt:P129 ?protein .
  	?protein wdt:P129 ?drug2 .
    ?drug2 p:P129 ?s .
  	OPTIONAL {
    	?s q:P794 ?v . # P794 'as' qualifier property
	}
  	SERVICE wikibase:label { bd:serviceParam wikibase:language "en" . }  
}
```
