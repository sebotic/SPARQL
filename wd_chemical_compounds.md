#### Some queries to explore chemical compounds in Wikidata

##### A query to retrieve all [chemical compounds](https://www.wikidata.org/wiki/Q11173)
Chemical compounds can either be [instance of](https://www.wikidata.org/wiki/Property:P31) or [subclass of](https://www.wikidata.org/wiki/Property:P279). On the long run, this should be unified to the semantically more appropriate '[subclass of](https://www.wikidata.org/wiki/Property:P279)' aka [is a](http://www.w3.org/2000/01/rdf-schema#subClassOf).

[execute](http://tinyurl.com/zbxdwyz)

```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT DISTINCT ?cmpnd ?label WHERE {
    {?cmpnd wdt:P279 wd:Q11173 .} UNION
  	{?cmpnd wdt:P31 wd:Q11173 .}
    ?cmpnd rdfs:label ?label .
}
```

Use Wikdata SPARQL label service instead, to get only English labels
[execute](http://tinyurl.com/jkevzwn)

```sparql
SELECT DISTINCT ?cmpnd ?cmpndLabel WHERE {
    {?cmpnd wdt:P279 wd:Q11173 .} UNION
  	{?cmpnd wdt:P31 wd:Q11173 .} 
    
  	SERVICE wikibase:label {
            bd:serviceParam wikibase:language "en" .
      }
}
```

##### Get all drug to drug-target interactions which have references indicating that they where impoorted from [Guide to Pharmacology](www.guidetopharmacology.com)
[execute](http://tinyurl.com/hwmuulb)

```sparql
PREFIX prov: <http://www.w3.org/ns/prov#>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX r: <http://www.wikidata.org/prop/reference/>
PREFIX bd: <http://www.bigdata.com/rdf#>

SELECT ?compound ?compoundLabel  ?iuphar_id WHERE {
    ?compound wdt:P31 wd:Q11173 .
  	?compound p:P129 ?stmnt .
    
    ?stmnt prov:wasDerivedFrom ?ref .
    ?ref  r:P248 wd:Q2793172  .  
    ?ref  r:P595 ?iuphar_id  .  
  
     SERVICE wikibase:label {
        bd:serviceParam wikibase:language "en" .
    }
}
ORDER BY ?compoundLabel
```

##### Get all basic chemical identifiers for items with IUPHAR ID
[execute](http://tinyurl.com/gsmryjz)

```sparql
SELECT ?compound ?compoundLabel ?iuphar_id ?inchi_key ?inchi ?canonical_smiles WHERE {
    ?compound wdt:P595 ?iuphar_id .
    OPTIONAL {?compound wdt:P235 ?inchi_key .}
    OPTIONAL {?compound wdt:P234 ?inchi .}
    OPTIONAL {?compound wdt:P233 ?canonical_smiles .}

     SERVICE wikibase:label {
     	bd:serviceParam wikibase:language "en" .
     }
    
} ORDER BY ?compoundLabel
```

##### Retrieve chemical compounds with CAS number or canonical SMILES but without references
[execute](http://tinyurl.com/zb2v2ro)

```sparql
SELECT DISTINCT * WHERE {
    {?cmpnd wdt:P279 wd:Q11173 .} UNION
    {?cmpnd wdt:P31 wd:Q11173 .}
  
    OPTIONAL {?cmpnd p:P233 ?csmiles FILTER NOT exists {?csmiles prov:wasDerivedFrom ?smref}.}
    OPTIONAL {?cmpnd p:P231 ?cas FILTER NOT exists {?cas prov:wasDerivedFrom ?smref}.}
 	FILTER (?csmiles  != '' ||  ?cas  != '')
}
```

##### Retrieve chemical compounds with CAS number or canonical SMILES but either without any references or a reference stating that the value has been imported from Russian, English or German Wikipedia.
[execute]()
```sparql
SELECT DISTINCT * WHERE {
    {?cmpnd wdt:P279 wd:Q11173 .} UNION
    {?cmpnd wdt:P31 wd:Q11173 .}
  
    OPTIONAL {
      {?cmpnd p:P233 ?csmiles FILTER NOT EXISTS {?csmiles prov:wasDerivedFrom ?smref}} UNION
      {?cmpnd p:P233 ?csmiles FILTER EXISTS {?csmiles prov:wasDerivedFrom <http://www.wikidata.org/reference/d6e3ab4045fb3f3feea77895bc6b27e663fc878a>}} UNION # imported from Russian Wikipedia
      {?cmpnd p:P233 ?csmiles FILTER EXISTS {?csmiles prov:wasDerivedFrom wdref:7eb64cf9621d34c54fd4bd040ed4b61a88c4a1a0}} UNION # imported from English Wikipedia
      {?cmpnd p:P233 ?csmiles FILTER EXISTS {?csmiles prov:wasDerivedFrom wdref:004ec6fbee857649acdbdbad4f97b2c8571df97b}} # importe from German Wikipedia
    }
    OPTIONAL {
      {?cmpnd p:P231 ?cas FILTER NOT EXISTS {?cas prov:wasDerivedFrom ?smref}.} UNION
      {?cmpnd p:P231 ?cas FILTER EXISTS {?cas prov:wasDerivedFrom wdref:d6e3ab4045fb3f3feea77895bc6b27e663fc878a}} UNION
      {?cmpnd p:P231 ?cas FILTER EXISTS {?cas prov:wasDerivedFrom wdref:7eb64cf9621d34c54fd4bd040ed4b61a88c4a1a0}} UNION
      {?cmpnd p:P231 ?cas FILTER EXISTS {?cas prov:wasDerivedFrom wdref:004ec6fbee857649acdbdbad4f97b2c8571df97b}}
    }
 	FILTER (?csmiles  != '' ||  ?cas  != '')
}

```
</br>
##### Retrieve all chemical identifiers and properties currently in Wikidata
[execute](http://tinyurl.com/hly5pew)
```sparql
SELECT * WHERE {
    {?cmpnd wdt:P279 wd:Q11173 .} UNION
    {?cmpnd wdt:P31 wd:Q11173 .}
    optional{?cmpnd wdt:P2129 ?idlh .}
    optional{?cmpnd wdt:P2177 ?solubility .} 
    optional{?cmpnd wdt:P2404 ?time_weighted_average_exposure_limit .} 
    optional{?cmpnd wdt:P2067 ?mass.} 
    optional{?cmpnd wdt:P2119 ?vapor_pressure .} 
    optional{?cmpnd wdt:P2101 ?melting_point .} 
    optional{?cmpnd wdt:P661 ?chemSpider .} 
    optional{?cmpnd wdt:P662 ?pubchem_cid .} 
    optional{?cmpnd wdt:P652 ?unii .} 
    optional{?cmpnd wdt:P657 ?rtecs_number .} 
    optional{?cmpnd wdt:P1542 ?cause_of .} 
    optional{?cmpnd wdt:P486 ?mesh_id .} 
    optional{?cmpnd wdt:P665 ?kegg_id .} 
    optional{?cmpnd wdt:P672 ?mesh_code .} 
    optional{?cmpnd wdt:P679 ?zvg_number .} 
    optional{?cmpnd wdt:P683 ?chebi .} 
    optional{?cmpnd wdt:P715 ?drugbank .} 
    optional{?cmpnd wdt:P2275 ?who_inn .} 
    optional{?cmpnd wdt:P2877 ?surechembl .}
    optional{?cmpnd wdt:P2892 ?umls_cui .}
    optional{?cmpnd wdt:P636 ?route_of_administration .}
    optional{?cmpnd wdt:P592 ?chembl .}
    optional{?cmpnd wdt:P595 ?iuphar .}
    optional{?cmpnd wdt:P231 ?cas .}
    optional{?cmpnd wdt:P233 ?csmiles .}
    optional{?cmpnd wdt:P234 ?inchi .}
    optional{?cmpnd wdt:P235 ?inchi_key .}
    optional{?cmpnd wdt:P274 ?chemical_formula .}
    optional{?cmpnd wdt:P267 ?atc_code .}
    optional{?cmpnd wdt:P2017 ?ismiles .}
    optional{?cmpnd wdt:P2153 ?pubchem_sid .}

    optional{?cmpnd wdt:P2054 ?density .}
    optional{?cmpnd wdt:P1117 ?pka .}
    optional{?cmpnd wdt:P227 ?gnd_id .}
    optional{?cmpnd wdt:P589 ?point_group .}
    optional{?cmpnd wdt:P556 ?crystal_system .}
    optional{?cmpnd wdt:P2075 ?speed_of_sound .}
    optional{?cmpnd wdt:P2102 ?boiling_point .}
    optional{?cmpnd wdt:P2260 ?ionization_energy .}
    optional{?cmpnd wdt:P2202 ?lower_flammable_limit .}
    optional{?cmpnd wdt:P2128 ?flash_point .}
    optional{?cmpnd wdt:P2203 ?upper_flammable_limit .}
    optional{?cmpnd wdt:P349 ?ndl_id .}
    optional{?cmpnd wdt:P232 ?einecs_number .}
    optional{?cmpnd wdt:P244 ?lcauth_id .}
    optional{?cmpnd wdt:P628 ?e_number .}
    optional{?cmpnd wdt:P695 ?un_number .}
    optional{?cmpnd wdt:P690 ?space_group .}
    optional{?cmpnd wdt:P700 ?kemler_code .}
    optional{?cmpnd wdt:P728 ?ghs_hazard_statement .}
    optional{?cmpnd wdt:P874 ?un_class .}
    optional{?cmpnd wdt:P876 ?un_packaging_group .}
    optional{?cmpnd wdt:P1931 ?niosh_pocket_guide_id .}
    optional{?cmpnd wdt:P995 ?nfpa_reactivity .}
    optional{?cmpnd wdt:P940 ?ghs_precautionary_statements .}
    optional{?cmpnd wdt:P993 ?nfpa_health .}
    optional{?cmpnd wdt:P94	?nfpa_fire .}
    optional{?cmpnd wdt:P2057 ?hmdb_id .}
    optional{?cmpnd wdt:P2084 ?zinc_id .}
    optional{?cmpnd wdt:P2062 ?hsdb_id .}
    optional{?cmpnd wdt:P2796 ?3dmet_id .}
}

```
