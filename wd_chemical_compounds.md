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

##### Retrieve all items which are of subclass of instance [chemical substance](http://www.wikidata.org/entity/Q79529)
Chemical substances are currently defined as all non-single cemical compound mixtures or substance classes in Wikidata. The present query also returns item labels, prefering English labels, if no English label is present, it falls back to German labels. </br>
</br>
[Execute](http://tinyurl.com/j8ee42e)
```sparql
SELECT ?substance ?substanceLabel WHERE {
	{?substance wdt:P31  wd:Q79529 .} UNION
	{?substance wdt:P279  wd:Q79529 .}
  
  	SERVICE wikibase:label {
            bd:serviceParam wikibase:language "en" .
      		bd:serviceParam wikibase:language "de" .
    }
}
```

</br>
##### Retrieve all chemical identifiers and properties currently in Wikidata
[execute](http://tinyurl.com/zy5ry5q)
```sparql
SELECT * WHERE {
    {?cmpnd wdt:P279 wd:Q11173 .} UNION
    {?cmpnd wdt:P31 wd:Q11173 .}
    OPTIONAL{?cmpnd wdt:P2129 ?idlh .}
    OPTIONAL{?cmpnd wdt:P2177 ?solubility .} 
    OPTIONAL{?cmpnd wdt:P2404 ?time_weighted_average_exposure_limit .} 
    OPTIONAL{?cmpnd wdt:P2067 ?mass.} 
    OPTIONAL{?cmpnd wdt:P2119 ?vapor_pressure .} 
    OPTIONAL{?cmpnd wdt:P2101 ?melting_point .} 
    OPTIONAL{?cmpnd wdt:P661 ?chemSpider .} 
    OPTIONAL{?cmpnd wdt:P662 ?pubchem_cid .} 
    OPTIONAL{?cmpnd wdt:P652 ?unii .} 
    OPTIONAL{?cmpnd wdt:P657 ?rtecs_number .} 
    OPTIONAL{?cmpnd wdt:P1542 ?cause_of .} 
    OPTIONAL{?cmpnd wdt:P486 ?mesh_id .} 
    OPTIONAL{?cmpnd wdt:P665 ?kegg_id .} 
    OPTIONAL{?cmpnd wdt:P672 ?mesh_code .} 
    OPTIONAL{?cmpnd wdt:P679 ?zvg_number .} 
    OPTIONAL{?cmpnd wdt:P683 ?chebi .} 
    OPTIONAL{?cmpnd wdt:P715 ?drugbank .} 
    OPTIONAL{?cmpnd wdt:P2275 ?who_inn .} 
    OPTIONAL{?cmpnd wdt:P2877 ?surechembl .}
    OPTIONAL{?cmpnd wdt:P2892 ?umls_cui .}
    OPTIONAL{?cmpnd wdt:P636 ?route_of_administration .}
    OPTIONAL{?cmpnd wdt:P592 ?chembl .}
    OPTIONAL{?cmpnd wdt:P595 ?iuphar .}
    OPTIONAL{?cmpnd wdt:P231 ?cas .}
    OPTIONAL{?cmpnd wdt:P233 ?csmiles .}
    OPTIONAL{?cmpnd wdt:P234 ?inchi .}
    OPTIONAL{?cmpnd wdt:P235 ?inchi_key .}
    OPTIONAL{?cmpnd wdt:P274 ?chemical_formula .}
    OPTIONAL{?cmpnd wdt:P267 ?atc_code .}
    OPTIONAL{?cmpnd wdt:P2017 ?ismiles .}
    OPTIONAL{?cmpnd wdt:P2153 ?pubchem_sid .}

    OPTIONAL{?cmpnd wdt:P2054 ?density .}
    OPTIONAL{?cmpnd wdt:P1117 ?pka .}
    OPTIONAL{?cmpnd wdt:P227 ?gnd_id .}
    OPTIONAL{?cmpnd wdt:P589 ?point_group .}
    OPTIONAL{?cmpnd wdt:P556 ?crystal_system .}
    OPTIONAL{?cmpnd wdt:P2075 ?speed_of_sound .}
    OPTIONAL{?cmpnd wdt:P2102 ?boiling_point .}
    OPTIONAL{?cmpnd wdt:P2260 ?ionization_energy .}
    OPTIONAL{?cmpnd wdt:P2202 ?lower_flammable_limit .}
    OPTIONAL{?cmpnd wdt:P2128 ?flash_point .}
    OPTIONAL{?cmpnd wdt:P2203 ?upper_flammable_limit .}
    OPTIONAL{?cmpnd wdt:P349 ?ndl_id .}
    OPTIONAL{?cmpnd wdt:P232 ?einecs_number .}
    OPTIONAL{?cmpnd wdt:P244 ?lcauth_id .}
    OPTIONAL{?cmpnd wdt:P628 ?e_number .}
    OPTIONAL{?cmpnd wdt:P695 ?un_number .}
    OPTIONAL{?cmpnd wdt:P690 ?space_group .}
    OPTIONAL{?cmpnd wdt:P700 ?kemler_code .}
    OPTIONAL{?cmpnd wdt:P728 ?ghs_hazard_statement .}
    OPTIONAL{?cmpnd wdt:P874 ?un_class .}
    OPTIONAL{?cmpnd wdt:P876 ?un_packaging_group .}
    OPTIONAL{?cmpnd wdt:P1931 ?niosh_pocket_guide_id .}
    OPTIONAL{?cmpnd wdt:P995 ?nfpa_reactivity .}
    OPTIONAL{?cmpnd wdt:P940 ?ghs_precautionary_statements .}
    OPTIONAL{?cmpnd wdt:P993 ?nfpa_health .}
    OPTIONAL{?cmpnd wdt:P94	?nfpa_fire .}
    OPTIONAL{?cmpnd wdt:P2057 ?hmdb_id .}
    OPTIONAL{?cmpnd wdt:P2084 ?zinc_id .}
    OPTIONAL{?cmpnd wdt:P2062 ?hsdb_id .}
    OPTIONAL{?cmpnd wdt:P2796 ?3dmet_id .}
    OPTIONAL{?cmpnd wdt:P3013 ?surface_tension .}
    OPTIONAL{?cmpnd wdt:P2993 ?part_coeff_water_oct .}
  	OPTIONAL{?cmpnd wdt:P3078 ?standard_enthalpy_of_formation .}
  	OPTIONAL{?cmpnd wdt:P3071 ?standard_molar_entropy .}
  	OPTIONAL{?cmpnd wdt:P3070 ?dynamic_viscosity .} 
  	OPTIONAL{?cmpnd wdt:P2118 ?kinematic_viscosity .}
}
ORDER BY ?cmpnd
```


