#### Some queries to explore chemical compounds in Wikidata

##### Get all Wikidata properties related to chemistry
[execute](http://tinyurl.com/y8xb5nsk)
```sparql
SELECT ?property ?propertyLabel  WHERE {
  {?property wdt:P31 wd:Q21294996 .} UNION
  {?property wdt:P31 wd:Q19833835 .}
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en" . }
}
```

##### Get all Wikidata properties related to chemistry plus the direct claims
[execute](http://tinyurl.com/ya9qukww)
```sparql
SELECT * WHERE {
  {?property wdt:P31 wd:Q21294996 .} UNION
  {?property wdt:P31 wd:Q19833835 .}
  ?property wikibase:directClaim ?p .
}
```

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

##### Above query slightly modified to return target proteins
[Execute](http://tinyurl.com/juymuqn)
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

SELECT DISTINCT ?compound ?compoundLabel  ?iuphar_id ?protein ?proteinLabel ?uniprot WHERE {
    ?compound wdt:P31 wd:Q11173 .
    ?compound wdt:P129 ?protein .
  	?protein wdt:P352 ?uniprot . 
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
    OPTIONAL{?cmpnd wdt:P1108 ?electronegativity .}
    OPTIONAL{?cmpnd wdt:P2240 ?median_lethal_dose .}
    OPTIONAL{?cmpnd wdt:P2712 ?median_lethal_concentration .}
    OPTIONAL{?cmpnd wdt:P4147 ?conjugate_acid .}
    OPTIONAL{?cmpnd wdt:P4149 ?conjugate_base .}
}
ORDER BY ?cmpnd
```

Extended version of the above query, which should aggregate all items which return more than one value per statement. Unformtunately, that does not work with query.wikidata.org, because the query string is too long. But it can be shortened as desired. 

```sparql
SELECT 
	?cmpnd 
	(GROUP_CONCAT(DISTINCT(?idlh_1 separator="|") as ?idlh)
	(GROUP_CONCAT(DISTINCT(?solubility_1); separator="|") as ?solubility)
	(GROUP_CONCAT(DISTINCT(?time_weighted_average_exposure_limit_1); separator="|") as ?time_weighted_average_exposure_limit)
    (GROUP_CONCAT(DISTINCT(?mass_1); separator="|") as ?mass)
	(GROUP_CONCAT(DISTINCT(?vapor_pressure_1); separator="|") as ?vapor_pressure)
	(GROUP_CONCAT(DISTINCT(?melting_point_1); separator="|") as ?melting_point)
    (GROUP_CONCAT(DISTINCT(?chemSpider_1); separator="|") as ?chemSpider)
    (GROUP_CONCAT(DISTINCT(?pubchem_cid_1); separator="|") as ?pubchem_cid)
    (GROUP_CONCAT(DISTINCT(?unii_1); separator="|") as ?unii)
    (GROUP_CONCAT(DISTINCT(?rtecs_number_1); separator="|") as ?rtecs_number)
    (GROUP_CONCAT(DISTINCT(?cause_of_1); separator="|") as ?cause_of)
    (GROUP_CONCAT(DISTINCT(?mesh_id_1); separator="|") as ?mesh_id)
    (GROUP_CONCAT(DISTINCT(?kegg_id_1); separator="|") as ?kegg_id)
    (GROUP_CONCAT(DISTINCT(?mesh_code_1); separator="|") as ?mesh_code)
    (GROUP_CONCAT(DISTINCT(?zvg_number_1); separator="|") as ?zvg_number)
    (GROUP_CONCAT(DISTINCT(?chebi_1); separator="|") as ?chebi)
    (GROUP_CONCAT(DISTINCT(?drugbank_1); separator="|") as ?drugbank)
    (GROUP_CONCAT(DISTINCT(?who_inn_1); separator="|") as ?who_inn)
    (GROUP_CONCAT(DISTINCT(?surechembl_1); separator="|") as ?surechembl)
    (GROUP_CONCAT(DISTINCT(?umls_cui_1); separator="|") as ?umls_cui)
    (GROUP_CONCAT(DISTINCT(?route_of_administration_1); separator="|") as ?route_of_administration)
    (GROUP_CONCAT(DISTINCT(?chembl_1); separator="|") as ?chembl)
    (GROUP_CONCAT(DISTINCT(?iuphar_1); separator="|") as ?iuphar)
    (GROUP_CONCAT(DISTINCT(?cas_1); separator="|") as ?cas)
    (GROUP_CONCAT(DISTINCT(?csmiles_1); separator="|") as ?csmiles)
    (GROUP_CONCAT(DISTINCT(?inchi_1); separator="|") as ?inchi)
    (GROUP_CONCAT(DISTINCT(?inchi_key_1); separator="|") as ?inchi_key)
    (GROUP_CONCAT(DISTINCT(?chemical_formula_1); separator="|") as ?chemical_formula)
    (GROUP_CONCAT(DISTINCT(?atc_code_1); separator="|") as ?atc_code)
	(GROUP_CONCAT(DISTINCT(?ismiles_1); separator="|") as ?ismiles)
    (GROUP_CONCAT(DISTINCT(?pubchem_sid_1); separator="|") as ?pubchem_sid)
    
	(GROUP_CONCAT(DISTINCT(?density_1); separator="|") as ?density)
    (GROUP_CONCAT(DISTINCT(?pka_1); separator="|") as ?pka)
    (GROUP_CONCAT(DISTINCT(?gnd_id_1); separator="|") as ?gnd_id)
    (GROUP_CONCAT(DISTINCT(?point_group_1); separator="|") as ?point_group)
    (GROUP_CONCAT(DISTINCT(?crystal_system_1); separator="|") as ?crystal_system)
    (GROUP_CONCAT(DISTINCT(?speed_of_sound_1); separator="|") as ?speed_of_sound)
    (GROUP_CONCAT(DISTINCT(?boiling_point_1); separator="|") as ?boiling_point)
    (GROUP_CONCAT(DISTINCT(?ionization_energy_1); separator="|") as ?ionization_energy)
    (GROUP_CONCAT(DISTINCT(?lower_flammable_limit_1); separator="|") as ?lower_flammable_limit)
    (GROUP_CONCAT(DISTINCT(?flash_point_1); separator="|") as ?flash_point)
    (GROUP_CONCAT(DISTINCT(?upper_flammable_limit_1); separator="|") as ?upper_flammable_limit)
    (GROUP_CONCAT(DISTINCT(?ndl_id_1); separator="|") as ?ndl_id)
    (GROUP_CONCAT(DISTINCT(?einecs_number_1); separator="|") as ?einecs_number)
    (GROUP_CONCAT(DISTINCT(?lcauth_id_1); separator="|") as ?lcauth_id)
    (GROUP_CONCAT(DISTINCT(?e_number_1); separator="|") as ?e_number)
    (GROUP_CONCAT(DISTINCT(?un_number_1); separator="|") as ?un_number)
    (GROUP_CONCAT(DISTINCT(?space_group_1); separator="|") as ?space_group)
    (GROUP_CONCAT(DISTINCT(?kemler_code_1); separator="|") as ?kemler_code)
    (GROUP_CONCAT(DISTINCT(?ghs_hazard_statement_1); separator="|") as ?ghs_hazard_statement)
    (GROUP_CONCAT(DISTINCT(?un_class_1); separator="|") as ?un_class)
    (GROUP_CONCAT(DISTINCT(?un_packaging_group_1); separator="|") as ?un_packaging_group)
    (GROUP_CONCAT(DISTINCT(?niosh_pocket_guide_id_1); separator="|") as ?niosh_pocket_guide_id)
    (GROUP_CONCAT(DISTINCT(?nfpa_reactivity_1); separator="|") as ?nfpa_reactivity)
    (GROUP_CONCAT(DISTINCT(?ghs_precautionary_statements_1); separator="|") as ?ghs_precautionary_statements)
    (GROUP_CONCAT(DISTINCT(?nfpa_health_1); separator="|") as ?nfpa_health)
    (GROUP_CONCAT(DISTINCT(?nfpa_fire_1); separator="|") as ?nfpa_fire)
    (GROUP_CONCAT(DISTINCT(?hmdb_id_1); separator="|") as ?hmdb_id)
    (GROUP_CONCAT(DISTINCT(?zinc_id_1); separator="|") as ?zinc_id)
    (GROUP_CONCAT(DISTINCT(?hsdb_id_1); separator="|") as ?hsdb_id)
    (GROUP_CONCAT(DISTINCT(?3dmet_id_1); separator="|") as ?3dmet_id)
    (GROUP_CONCAT(DISTINCT(?surface_tension_1); separator="|") as ?surface_tension)
    (GROUP_CONCAT(DISTINCT(?part_coeff_water_oct_1); separator="|") as ?part_coeff_water_oct)
    (GROUP_CONCAT(DISTINCT(?standard_enthalpy_of_formation_1); separator="|") as ?standard_enthalpy_of_formation)
  	(GROUP_CONCAT(DISTINCT(?standard_molar_entropy_1); separator="|") as ?standard_molar_entropy)
  	(GROUP_CONCAT(DISTINCT(?dynamic_viscosity_1); separator="|") as ?dynamic_viscosity)
	(GROUP_CONCAT(DISTINCT(?kinematic_viscosity_1); separator="|") as ?kinematic_viscosity)

WHERE {
    {?cmpnd wdt:P279 wd:Q11173 .} UNION
    {?cmpnd wdt:P31 wd:Q11173 .} UNION
    {?cmpnd wdt:P662 ?cid .}
   
   	OPTIONAL{?cmpnd wdt:P2129 ?idlh_1 .
    OPTIONAL{?cmpnd wdt:P2177 ?solubility_1 .} 
    OPTIONAL{?cmpnd wdt:P2404 ?time_weighted_average_exposure_limit_1 .} 
    OPTIONAL{?cmpnd wdt:P2067 ?mass_1.} 
    OPTIONAL{?cmpnd wdt:P2119 ?vapor_pressure_1 .} 
    OPTIONAL{?cmpnd wdt:P2101 ?melting_point_1 .} 
    OPTIONAL{?cmpnd wdt:P661 ?chemSpider_1 .} 
    OPTIONAL{?cmpnd wdt:P662 ?pubchem_cid_1 .} 
    OPTIONAL{?cmpnd wdt:P652 ?unii_1 .} 
    OPTIONAL{?cmpnd wdt:P657 ?rtecs_number_1 .} 
    OPTIONAL{?cmpnd wdt:P1542 ?cause_of_1 .} 
    OPTIONAL{?cmpnd wdt:P486 ?mesh_id_1 .} 
    OPTIONAL{?cmpnd wdt:P665 ?kegg_id_1 .} 
    OPTIONAL{?cmpnd wdt:P672 ?mesh_code_1 .} 
    OPTIONAL{?cmpnd wdt:P679 ?zvg_number_1 .} 
    OPTIONAL{?cmpnd wdt:P683 ?chebi_1 .} 
    OPTIONAL{?cmpnd wdt:P715 ?drugbank_1 .} 
    OPTIONAL{?cmpnd wdt:P2275 ?who_inn_1 .} 
    OPTIONAL{?cmpnd wdt:P2877 ?surechembl_1 .}
    OPTIONAL{?cmpnd wdt:P2892 ?umls_cui_1 .}
    OPTIONAL{?cmpnd wdt:P636 ?route_of_administration_1 .}
    OPTIONAL{?cmpnd wdt:P592 ?chembl_1 .}
    OPTIONAL{?cmpnd wdt:P595 ?iuphar_1 .}
    OPTIONAL{?cmpnd wdt:P231 ?cas_1 .}
    OPTIONAL{?cmpnd wdt:P233 ?csmiles_1 .}
    OPTIONAL{?cmpnd wdt:P234 ?inchi_1 .}
    OPTIONAL{?cmpnd wdt:P235 ?inchi_key_1 .}
    OPTIONAL{?cmpnd wdt:P274 ?chemical_formula_1 .}
    OPTIONAL{?cmpnd wdt:P267 ?atc_code_1 .}
    OPTIONAL{?cmpnd wdt:P2017 ?ismiles_1 .}
    OPTIONAL{?cmpnd wdt:P2153 ?pubchem_sid_1 .}

    OPTIONAL{?cmpnd wdt:P2054 ?density_1 .}
    OPTIONAL{?cmpnd wdt:P1117 ?pka_1 .}
    OPTIONAL{?cmpnd wdt:P227 ?gnd_id_1 .}
    OPTIONAL{?cmpnd wdt:P589 ?point_group_1 .}
    OPTIONAL{?cmpnd wdt:P556 ?crystal_system_1 .}
    OPTIONAL{?cmpnd wdt:P2075 ?speed_of_sound_1 .}
    OPTIONAL{?cmpnd wdt:P2102 ?boiling_point_1 .}
    OPTIONAL{?cmpnd wdt:P2260 ?ionization_energy_1 .}
    OPTIONAL{?cmpnd wdt:P2202 ?lower_flammable_limit_1 .}
    OPTIONAL{?cmpnd wdt:P2128 ?flash_point_1 .}
    OPTIONAL{?cmpnd wdt:P2203 ?upper_flammable_limit_1 .}
    OPTIONAL{?cmpnd wdt:P349 ?ndl_id_1 .}
    OPTIONAL{?cmpnd wdt:P232 ?einecs_number_1 .}
    OPTIONAL{?cmpnd wdt:P244 ?lcauth_id_1 .}
    OPTIONAL{?cmpnd wdt:P628 ?e_number_1 .}
    OPTIONAL{?cmpnd wdt:P695 ?un_number_1 .}
    OPTIONAL{?cmpnd wdt:P690 ?space_group_1 .}
    OPTIONAL{?cmpnd wdt:P700 ?kemler_code_1 .}
    OPTIONAL{?cmpnd wdt:P728 ?ghs_hazard_statement_1 .}
    OPTIONAL{?cmpnd wdt:P874 ?un_class_1 .}
    OPTIONAL{?cmpnd wdt:P876 ?un_packaging_group_1 .}
    OPTIONAL{?cmpnd wdt:P1931 ?niosh_pocket_guide_id_1 .}
    OPTIONAL{?cmpnd wdt:P995 ?nfpa_reactivity_1 .}
    OPTIONAL{?cmpnd wdt:P940 ?ghs_precautionary_statements_1 .}
    OPTIONAL{?cmpnd wdt:P993 ?nfpa_health_1 .}
    OPTIONAL{?cmpnd wdt:P94	?nfpa_fire_1 .}
    OPTIONAL{?cmpnd wdt:P2057 ?hmdb_id_1 .}
    OPTIONAL{?cmpnd wdt:P2084 ?zinc_id_1 .}
    OPTIONAL{?cmpnd wdt:P2062 ?hsdb_id_1 .}
    OPTIONAL{?cmpnd wdt:P2796 ?3dmet_id_1 .}
    OPTIONAL{?cmpnd wdt:P3013 ?surface_tension_1 .}
    OPTIONAL{?cmpnd wdt:P2993 ?part_coeff_water_oct_1 .}
    OPTIONAL{?cmpnd wdt:P3078 ?standard_enthalpy_of_formation_1 .}
    OPTIONAL{?cmpnd wdt:P3071 ?standard_molar_entropy_1 .}
    OPTIONAL{?cmpnd wdt:P3070 ?dynamic_viscosity_1 .}
    OPTIONAL{?cmpnd wdt:P2118  ?kinematic_viscosity_1 .}
}
GROUP BY ?cmpnd
```

#### Get all chemcial compound Wikidata items and a set of basic identifiers and the links to English Wikipedia
[Execute](http://tinyurl.com/zk492ly)

```sparql
SELECT DISTINCT * WHERE {
    {?cmpnd wdt:P279 wd:Q11173 .} UNION
    {?cmpnd wdt:P31 wd:Q11173 .} UNION
    {?cmpnd wdt:P662 [] .} UNION
    {?cmpnd wdt:P231 [] .} UNION
   	{?cmpnd wdt:P235 [] .}
     
    ?article schema:about ?cmpnd .
    ?article schema:inLanguage "en" .
    FILTER (SUBSTR(str(?article), 1, 25) = "https://en.wikipedia.org/") 
  
    OPTIONAL{?cmpnd wdt:P2067 ?mass.} 
    OPTIONAL{?cmpnd wdt:P661 ?chemSpider .} 
    OPTIONAL{?cmpnd wdt:P662 ?pubchem_cid .} 
    OPTIONAL{?cmpnd wdt:P652 ?unii .} 
    OPTIONAL{?cmpnd wdt:P486 ?mesh_id .} 
    OPTIONAL{?cmpnd wdt:P665 ?kegg_id .}  
    OPTIONAL{?cmpnd wdt:P683 ?chebi .} 
    OPTIONAL{?cmpnd wdt:P715 ?drugbank .} 
    OPTIONAL{?cmpnd wdt:P2275 ?who_inn .} 
    OPTIONAL{?cmpnd wdt:P592 ?chembl .}
    OPTIONAL{?cmpnd wdt:P595 ?iuphar .}
    OPTIONAL{?cmpnd wdt:P231 ?cas .}
    OPTIONAL{?cmpnd wdt:P233 ?csmiles .}
    OPTIONAL{?cmpnd wdt:P234 ?inchi .}
    OPTIONAL{?cmpnd wdt:P235 ?inchi_key .}
    OPTIONAL{?cmpnd wdt:P274 ?chemical_formula .}
    OPTIONAL{?cmpnd wdt:P267 ?atc_code .}
    OPTIONAL{?cmpnd wdt:P2017 ?ismiles .}
}
ORDER BY ?cmpnd
```

##### A different way of performing the same task as above (getting all values for all chemical compounds). Retrieve all properties which are ['related to chemistry.'](https://www.wikidata.org/wiki/Q21294996)
[Execute](http://tinyurl.com/yay6br62)
```sparql
SELECT ?cmpnd ?value ?p WHERE {
  ?property wdt:P31 wd:Q21294996 .
  ?property wikibase:directClaim ?p .
  
  {?cmpnd wdt:P279 wd:Q11173 .} UNION
  {?cmpnd wdt:P31 wd:Q11173 .}
    
  ?cmpnd ?p ?value . 
}
GROUP BY ?cmpnd ?p ?value
#LIMIT 10000
```

##### Get all Wikidata items with a novalue PubChem ID:
[Execute](http://tinyurl.com/jlbz8wu)
```sparql
SELECT * WHERE {
	?c p:P662 ?cid .
	?cid rdf:type <http://www.wikidata.org/prop/novalue/P662>
}
```

#### Maintenance queries:
##### Get all compounds with canoncial SMILES but without InChI key
[Execute](http://tinyurl.com/zomqfp9)


```sparql
SELECT * WHERE {
	{?cmpnd wdt:P233 ?csmiles .} MINUS
  	{?cmpnd wdt:P235 ?ikey .} 
    
  OPTIONAL{
  	?cmpnd wdt:P662 ?cid .
  }
}
```
##### Get all databases in Wikidata
[Execute](http://tinyurl.com/zqpapv4)
```sparql
SELECT DISTINCT ?db ?wd_prop WHERE {
	{?db wdt:P31 wd:Q2881060 . } UNION  # chemical database
    {?db wdt:P31 wd:Q4117139 } UNION    # biological database
  	{?db wdt:P31 wd:Q8513}				# database (general)
  	
  	OPTIONAL {
      ?db wdt:P1687 ?wd_prop .
    }
}
```

##### Get a certain subset of chemical identifiers
[Execute](http://tinyurl.com/hgzb5x8)
```sparql
SELECT ?cmpnd ?cid
    (GROUP_CONCAT(DISTINCT(?ikey); separator="|") as ?ikey)
	(GROUP_CONCAT(DISTINCT(?csid_1); separator="|") as ?csi)
	(GROUP_CONCAT(DISTINCT(?chembl_1); separator="|") as ?chembl)
	(GROUP_CONCAT(DISTINCT(?unii); separator="|") as ?unii)
WHERE {
    ?cmpnd wdt:P662 ?cid .
    OPTIONAL { ?cmpnd wdt:P235 ?ikey . }
  	OPTIONAL { ?cmpnd wdt:P661 ?csid_1 . } # chemspider
  	OPTIONAL { ?cmpnd wdt:P592 ?chembl_1 . } # chembl
  	OPTIONAL { ?cmpnd wdt:P652 ?unii . } # unii
  
}
GROUP BY ?cmpnd ?cid

OFFSET 0
LIMIT 5000
```

##### Filter for certain strings in labels
[Execute](http://tinyurl.com/gwhj6yx)
```sparql
SELECT ?c ?cLabel ?chebi WHERE {
	?c wdt:P683 ?chebi .
  	
  	SERVICE wikibase:label {
    	bd:serviceParam wikibase:language "en" . 
  }    
}
GROUP BY ?c ?cLabel ?chebi
HAVING (contains(?cLabel, "->"))
```

##### Get chemical compounds which have a PDB ID and also return their protein 'targets', based on equal PDB IDs.
[Execute](http://tinyurl.com/zj78lvj)
```sparql
SELECT * WHERE { 
  ?c wdt:P638 ?pdb. 
  ?c wdt:P31 wd:Q11173 . 
  ?x wdt:P638 ?pdb .
  ?x wdt:P279 wd:Q8054 .
  FILTER (?c != ?x)
}
```
##### Get all names for all chemcial compounds in Wikidata
[Execute](http://tinyurl.com/z4nmtwn)
```sparql
SELECT ?compound ?label ?who_name (GROUP_CONCAT(DISTINCT(?alias); separator="|") AS ?aliases) WHERE { 
  	{?compound wdt:P31 wd:Q11173 .} UNION  # chemical compound
  	{?compound wdt:P31 wd:Q12140 .} UNION  # pharmaceutical drug
  	{?compound wdt:P31 wd:Q79529 .} UNION  # chemical substance
	{?compound wdt:P2275 ?who_name FILTER (LANG(?who_name) = "en") .}
  
  OPTIONAL {
    ?compound rdfs:label ?label FILTER (LANG(?label) = "en") .
  }
  OPTIONAL {
  	?compound skos:altLabel ?alias FILTER (LANG(?alias) = "en") .
  }
}
GROUP BY ?compound ?label ?who_name #?aliases
OFFSET 100000
LIMIT 100000
```

##### Get all human proteins which have a PDB ID and get all chemical compound which have the same PDB ID. 
This means that the chemical compound is a small molecule ligand of the PDB structure (and therefore also the protein). In some cases, the small molecule is acutally not a ligand but covalently bound.
[Execute](http://tinyurl.com/l6bh8hv)

```sparql
SELECT distinct ?x ?y WHERE {
  ?x wdt:P703 wd:Q15978631.
  ?x wdt:P638 ?pdb.
  ?y wdt:P31 wd:Q11173.
  ?y wdt:P638 ?pdb .
}
```
##### Get all componds out of a list of QIDs, also get all InChI keys which start with certain letters, return a bunch of chem identifiers.
[Execute](http://tinyurl.com/mat2ghv)

```sparql
 SELECT DISTINCT * WHERE {
   {
     SELECT * WHERE {
   		VALUES ?c { wd:Q423111 wd:Q28276886 }
    	?c wdt:P31 wd:Q11173 . 
   }} UNION
   {
     SELECT * WHERE {
   		?c wdt:P235 ?ikey . FILTER (STRSTARTS(?ikey, 'BSY'))
   }}
   OPTIONAL { ?c wdt:P662 ?cid . }
   OPTIONAL { ?c wdt:P661 ?csid . }
   OPTIONAL { ?c wdt:P235 ?ikey . }
   SERVICE wikibase:label { bd:serviceParam wikibase:language "en" . }  
}
```
##### Get all chemical compounds which have a PDB Id, get the human proteins which share the same PDB ID and return that. This essentially gives a list of all human protein crystal structures with their ligands
[Execute](http://tinyurl.com/lgfwynj)

```sparql
SELECT DISTINCT ?compound ?compoundLabel ?pdb ?proteinLabel ?protein WHERE {
  ?protein wdt:P279 wd:Q8054.
  ?protein wdt:P703 wd:Q15978631.
  ?protein wdt:P638 ?pdb .
  
  ?compound wdt:P638 ?pdb .
  ?compound wdt:P31 wd:Q11173 .
  
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en" . } 
}
```

##### Get all Wikidata items referenced with Guide To Pharmacology
[Execute](http://tinyurl.com/y9xwore5)

```sparql
SELECT DISTINCT ?item ?itemLabel WHERE {
  {?ref pr:P248 wd:Q17091219 .} UNION
  {?ref pr:P595 ?gtpl . }
  ?wds prov:wasDerivedFrom ?ref .
  ?item ?p ?wds .
  
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
```

##### Get compounds which are ATPase inhibitors (subclass of aka is_a)
[Execute](http://tinyurl.com/y7xgcmmx)

```sparql
SELECT ?compound ?compoundLabel ?term ?termLabel WHERE {
  ?term wdt:P683 '78928' .
  ?compound wdt:P279 ?term .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
```
##### Get all compounds with their conjugate base in Wikidata
[Execute](http://tinyurl.com/yc2as6vm)
```sparql
SELECT ?compound ?compoundLabel ?conjugate_base ?conjugate_baseLabel WHERE {
  ?compound wdt:P4149 ?conjugate_base .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
```

##### Get all compounds with their conjugate acid in Wikidata
[Execute](http://tinyurl.com/yatxore7)
```sparql
SELECT ?compound ?compoundLabel ?conjugate_acid ?conjugate_acidLabel WHERE {
  ?compound wdt:P4147 ?conjugate_acid .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }

}
```
##### Get all Wikipedia categories in Wikidata which have a chemical compound or a chemcial substance as their main subject
[Execute](http://tinyurl.com/y997amh5)
```sparql
SELECT ?main_topic ?main_topicLabel ?cat ?catLabel WHERE {
  ?cat wdt:P301 ?main_topic .
  {?main_topic wdt:P31 wd:Q11173 . } UNION
  {?main_topic wdt:P31 wd:Q79529 .}
  
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
```

##### Get several properties and all English aliases for [imatinib](http://www.wikidata.org/entity/Q177094)
[Execute](http://tinyurl.com/ybs8jqsf)

For some reason, this query returns a low number of properties without a value.


```sparql
SELECT ?compound ?compoundLabel ?prop ?id ?idLabel WHERE {
  VALUES ?compound {wd:Q177094}
  VALUES ?prop {wdt:P274 wdt:P231 wdt:P662 wdt:P661 wdt:P592 wdt:P3780 wdt:P232 skos:altLabel}
  OPTIONAL {?compound ?prop ?id filter (isIRI(?id) ||  (lang(?id) = "en" || lang(?id) = "") ) .  }

  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". } 
}
```

##### Get all compounds which have been modified after a certain date
[Execute](http://tinyurl.com/y8hpeuoy)

```sparql
SELECT * WHERE {
  ?c wdt:P235 ?ikey .
  ?c schema:dateModified ?date FILTER (?date > "2018-03-05T14:45:13"^^xsd:dateTime).
}
```
