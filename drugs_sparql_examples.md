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