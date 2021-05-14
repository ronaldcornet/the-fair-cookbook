(fcb-interop-covid-metadata)=
# Metadata Profile Validation in RDF with Shape Expression: The Covid-19 sample metadata use case 

+++
<br/>

````{panels_fairplus}
:identifier_text: RX.X
:identifier_link: 'https://example.com'
:difficulty_level: 3
:recipe_type: hands_on
:reading_time_minutes: 30
:intended_audience: principal_investigator, data_manager, data_scientist  
:has_executable_code: yeah
:recipe_name: Metadata Profile Validation in RDF with Shape Expression, The Covid-19 sample metadata use case
```` 

## Main Objectives

The purpose of this recipe is to show how to create a metadata collection form complying with a community minimal information checklist (MIUViG), in the context of Covid-19 strain sequencing assays carried on patient collected samples.
In addition, the recipe includes the conversion of sample metadata to an RDF/Linked Data graph and checks its structure for conformance to requirement using the ShapeExpression specifications.
Finally, use queries expressed in SPARQL are shown to demonstrate potential data integration scenarios.


## Graphical Overview


```{figure} covid19-sample-metadata-profile-shex-use-case-mermaid.png
---
width: 800px
name: building a commmunity compliant metadata profile
alt: building a commmunity compliant metadata profile
---
Building a commmunity compliant metadata profile.
```



## Introduction

>:information_source: 
This recipe is adapted from work carried during the Elixir Covid-19 biohackathon, by the ontology and workflow tracks and presented [here](http://covid19.genenetwork.org/) and detailed in the [following manuscript](https://github.com/arvados/bh20-seq-resource/blob/master/paper/paper.md) while all the code and associated material is hosted on this [github repository](https://github.com/arvados/bh20-seq-resource).


>:information_source: 
[Robert Hoendorf](https:// "title"), [Jose Emilio Labra Gayo](https:// "title"),[Thomas Liener](https:// "title"), [Nuria Queralt Rosinach](https:// "title")
, [Tazro  Ohta](https:// "title"), [Philippe Rocca-Serra](https:// "title"), [Claus Weilland](https:// "title"), [Piotr Prins](https:// "title"),  [Danielle Welter](https:// "title"). Thomas Liener  and Danielle Welter acted as coordinator between the ontology track and the workflow track led by Piotr Prins. 



In this specific report, we focus solely on the specific task of creating `covid-19 virus sample metadata reporting profile`. The aim was to ensure that each sequencing file generated by the sequencing efforts came with sufficient descriptive metadata to allow basic correlation analysis.

Therefore, 6 essential steps were performed:
> * Listing essential sample attributes
> * Performing a semantic anchoring of these attributes
> * Defining a formal representation capturing those requirements
> * Expressing instance data in RDF/linked data format
> * Validating RDF instance data against requirements using a Shape Expression(SHEX)
> * Testing query cases by formulating SPARQL queries


The following sections detail each of these steps

## Defining the metadata fields

<!-- <div><img src="https://i.imgur.com/lwV1cPd.png" width="300" style="border:1px solid black"/></div>
<div><img src="https://i.imgur.com/1ahQLjy.png" width="300" style="border:1px solid black"/></div>  -->


```{figure} https://i.imgur.com/lwV1cPd.png
---
width: 300px
name: Defining the metadata fields
alt: Defining the metadata fields
---
Defining the metadata fields
```

```{figure} https://i.imgur.com/1ahQLjy.png
---
width: 300px
name: Defining the metadata fields-1
alt: Defining the metadata fields-1
---
Defining the metadata fields-1
```


<br>Based on the [Genome Standards Consortium](https://gensc.org/) metadata requirement profile for uncultivated viral sample, also known as the [Minimum Information About an Uncultivated Virus Genome (MIUViG)](https://github.com/GenomicsStandardsConsortium/mixs/blob/master/UViGs_MIxS.xlsx), the first step is to anchor the tags defined by GSC and approved by the those **International Nucleotide Sequence Database Collaboration** ([INSDC](http://www.insdc.org/)) tags to one (or more) semantic framework(s).



## Semantic anchoring of metadata element:

Several distinct to the following resources mappings have been made by the developers :
    
- [OBO Foundry](https://obofoundry.org)
- [Wikidata](https://wikidata.org)
- [schema.org](https://schema.org)

<!-- ![](https://i.imgur.com/Ro92a7D.png) -->
<!-- <div><img src="https://i.imgur.com/Ro92a7D.png" width="800" style="border:1px solid black"/></div> -->

```{figure} https://i.imgur.com/Ro92a7D.png
---
width: 800px
name: Semantic anchoring of metadata element
alt: Semantic anchoring of metadata element
---
Semantic anchoring of metadata element
```



<br>

However, for the final implementation, only the OBO related mappings have been used as show in the following figure.


<!-- <div><img src="https://i.imgur.com/Lc7FcPs.png" width="800" style="border:1px solid black"/></div> -->
<!-- ![](https://i.imgur.com/Lc7FcPs.png) -->

```{figure} https://i.imgur.com/Lc7FcPs.png
---
width: 800px
name: Semantic anchoring of metadata element - final
alt: Semantic anchoring of metadata element - final
---
Semantic anchoring of metadata element - final
```



<br>



## 1. metadata schema definition using SALAD schema language:

Quoting the project's documentation, "*[the Semantic Annotations for Linked Avro Data (SALAD)](https://github.com/common-workflow-language/schema_salad) is a schema language for describing JSON or YAML structured linked data documents. `SALAD schema` describes rules for preprocessing, structural validation, and hyperlink checking for documents described by a Salad schema. Salad supports rich data modeling with inheritance, template specialization, object identifiers, object references, documentation generation, code generation, and transformation to RDF. SALAD provides a bridge between document and record oriented data modeling and the Semantic Web."*

The SALAD schema is used extensively by the [Common Workflow Language(CWL)](https://github.com/common-workflow-language) for defining and specifying computational workflows. 
But in this example, we are using the SALAD schema to capture the annotation requirements in a [YAML](https://yaml.org) document, while also embedding the semantics constraints, which can then be used to  to build a web form (see below) but also support conversion to RDF/LinkedData.

 > :warning: This YAML document must be a UTF-8 text encoded, JSON-compatible subset of YAML in order to be processed by the SALAD schema processor.


Below is a partial view of the YAML defined metadata  form, showing how `host` information requirements have been defined:


```bash
$base: http://biohackathon.org/bh20-seq-schema
$namespaces:
  sch: https://schema.org/
  efo: http://www.ebi.ac.uk/efo/
  obo: http://purl.obolibrary.org/obo/
  sio: http://semanticscience.org/resource/
  edam: http://edamontology.org/
  evs: http://ncicb.nci.nih.gov/xml/owl/EVS/Thesaurus.owl#

$graph:

- name: hostSchema
  type: record
  fields:
    host_species:
        doc: Host species as defined in NCBITaxon, e.g. http://purl.obolibrary.org/obo/NCBITaxon_9606 for Homo sapiens
        type: string
        jsonldPredicate:
          _id: http://www.ebi.ac.uk/efo/EFO_0000532
          _type: "@id"
          noLinkCheck: true
    host_id:
        doc: Identifer for the host. If you submit multiple samples from the same host, use the same host_id for those samples
        type: string?
        jsonldPredicate:
          _id: http://semanticscience.org/resource/SIO_000115
    host_sex:
        doc: Sex of the host as defined in PATO, expect Male (http://purl.obolibrary.org/obo/PATO_0000384) or Female (http://purl.obolibrary.org/obo/PATO_0000383) or in Intersex (http://purl.obolibrary.org/obo/PATO_0001340)
        type: string?
        jsonldPredicate:
          _id: http://purl.obolibrary.org/obo/PATO_0000047
          _type: "@id"
          noLinkCheck: true
    host_age:
        doc: Age of the host as number (e.g. 50)
        type: int?
        jsonldPredicate:
          _id: http://purl.obolibrary.org/obo/PATO_0000011
    host_age_unit:
        doc: Unit of host age e.g. http://purl.obolibrary.org/obo/UO_0000036
        type: string?
        jsonldPredicate:
          _id: http://purl.obolibrary.org/obo/NCIT_C42574
          _type: "@id"
          noLinkCheck: true
    host_health_status:
        doc: A condition or state at a particular time, must be one of the following (obo:NCIT_C115935 obo:NCIT_C3833 obo:NCIT_C25269 obo:GENEPIO_0002020 obo:GENEPIO_0001849 obo:NCIT_C28554 obo:NCIT_C37987)
        type: string?
        jsonldPredicate:
          _id: http://purl.obolibrary.org/obo/NCIT_C25688
          _type: "@id"
          noLinkCheck: true
    host_treatment:
      doc: Process in which the act is intended to modify or alter host status
      type: string?
      jsonldPredicate:
          _id: http://www.ebi.ac.uk/efo/EFO_0000727
```

`source`: https://github.com/arvados/bh20-seq-resource/blob/master/bh20sequploader/bh20seq-schema.yml


### The corresponding metadata acquisition web form:

<!-- <div><img src="https://i.imgur.com/5eQN9hw.png" width="800" style="border:1px solid black"/></div> -->

```{figure} https://i.imgur.com/5eQN9hw.png
---
width: 800px
name: The corresponding metadata acquisition web form
alt: The corresponding metadata acquisition web form
---
```

<!-- ![](https://i.imgur.com/5eQN9hw.png) -->

<!-- <div><img src="https://i.imgur.com/RR5GSgi.png" width="800" style="border:1px solid black"/></div> -->

<!-- ![](https://i.imgur.com/RR5GSgi.png) -->


```{figure} https://i.imgur.com/RR5GSgi.png
---
width: 800px
name: The corresponding metadata acquisition web form 2
alt: The corresponding metadata acquisition web form 2
---
The corresponding metadata acquisition web form
```


## 2. Exemplar instance data:


When users submit information via the form (or by other programatic means), a instance YAML file is generated, which looks like this:


```bash
id: placeholder

host:
    host_id: XX1
    host_species: http://purl.obolibrary.org/obo/NCBITaxon_9606
    host_sex: http://purl.obolibrary.org/obo/PATO_0000384
    host_age: 20
    host_age_unit: http://purl.obolibrary.org/obo/UO_0000036
    host_health_status: http://purl.obolibrary.org/obo/NCIT_C25269
    host_treatment: Process in which the act is intended to modify or alter host status (Compounds)
    host_vaccination: [vaccines1,vaccine2]
    ethnicity: http://purl.obolibrary.org/obo/HANCESTRO_0010
    additional_host_information: Optional free text field for additional information

sample:
    sample_id: Id of the sample as defined by the submitter 
    collector_name: Name of the person that took the sample
    collecting_institution: Institute that was responsible of sampling  
    specimen_source: [http://purl.obolibrary.org/obo/NCIT_C155831,http://purl.obolibrary.org/obo/NCIT_C155835]
    collection_date: "2020-01-01"
    collection_location: http://www.wikidata.org/entity/Q148
    sample_storage_conditions: frozen specimen
    source_database_accession: [http://identifiers.org/insdc/LC522350.1#sequence]
    additional_collection_information: Optional free text field for additional information

virus:
    virus_species: http://purl.obolibrary.org/obo/NCBITaxon_2697049
    virus_strain: SARS-CoV-2/human/CHN/HS_8/2020

technology:
    sample_sequencing_technology: [http://www.ebi.ac.uk/efo/EFO_0009173,http://www.ebi.ac.uk/efo/EFO_0009173]
    sequence_assembly_method: Protocol used for assembly
    sequencing_coverage: [70.0, 100.0]
    additional_technology_information: Optional free text field for additional information

submitter:
    authors: [John Doe, Joe Boe, Jonny Oe]
    submitter_name: [John Doe]
    submitter_address: John Doe\'s address
    originating_lab: John Doe kitchen
    lab_address: John Doe\'s address
    provider_sample_id: XXX1
    submitter_sample_id: XXX2
    publication: PMID00001113
    submitter_orcid: [https://orcid.org/0000-0000-0000-0000,https://orcid.org/0000-0000-0000-0001]
    additional_submitter_information: Optional free text field for additional information

```

`source`: https://github.com/arvados/bh20-seq-resource/blob/master/example/maximum_metadata_example.yaml

## 3. Conversion from YAML to RDF:

Using the schema SALAD python package, the YAML instance file can be easily converted to RDF as shown in the code snippet below:

```bash
$ pip install schema_salad
```
Get JSON-LD context::
```bash
$ schema-salad-tool --print-jsonld-context myschema.yml mydocument.yml
```

Convert a document to JSON-LD::
```bash
$ schema-salad-tool --print-pre myschema.yml mydocument.yml > mydocument.jsonld

```


## 4. RDF graph validation with ShEx expression:

### 4.1 What is ShEx?

ShEx stands for `Shape Expression` and is a syntax for validating and describing RDF graphs. ShEx expressions can be used both to describe RDF and check the conformance of RDF data. The ShEx language  specification was published by the W3C Shape Expressions Community Group but it is not a W3C Standard nor is it on the W3C Standards Track.

It should be noted that the current W3C Technical Recommendations for RDF shape validation is the [SHACL specification](https://www.w3.org/TR/shacl/).

ShEx was selected owing to its simplicity, ease of use and availability of experts.


### 4.2 Why is this needed?


While defining a SALAD schema using YAML allows to list key entities and their attributes, it does not allow to check constraints. This has to be done on the RDF which needs to be checks for compliancee against a set of constraints which can be expressed using ShEx. Working with a ShEx expert ([Dr Jose Emilio Labra Gayo - (Oviedo Uni)](http://di002.edv.uniovi.es/~labra/), the following [Shape Expression syntax](https://shex.io/shex-semantics/) profile was developed and used to validate the RDF before persistence to the SPARQL endpoint.


```bash
PREFIX : <https://raw.githubusercontent.com/arvados/bh20-seq-resource/master/bh20sequploader/bh20seq-shex.rdf#>
PREFIX MainSchema: <http://biohackathon.org/bh20-seq-schema#MainSchema/>
PREFIX hostSchema: <http://biohackathon.org/bh20-seq-schema#hostSchema/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX obo: <http://purl.obolibrary.org/obo/>
PREFIX sio: <http://semanticscience.org/resource/>
PREFIX efo: <http://www.ebi.ac.uk/efo/>
PREFIX evs: <http://ncicb.nci.nih.gov/xml/owl/EVS/Thesaurus.owl#>
PREFIX edam: <http://edamontology.org/>
PREFIX wikidata: <http://www.wikidata.org/entity/>

:submissionShape {
  MainSchema:host   @:hostShape ;
  MainSchema:sample @:sampleShape ;
  MainSchema:submitter @:submitterShape ;
  MainSchema:technology @:technologyShape ;
  MainSchema:virus @:virusShape;
}

:hostShape  {
    efo:EFO_0000532 [ obo:NCBITaxon_~ ] ;
    sio:SIO_000115 xsd:string ?;
    obo:PATO_0000047 [ obo:PATO_0000384 obo:PATO_0000383 obo:PATO_0001340] ?;
    obo:PATO_0000011 xsd:integer ?;
    obo:NCIT_C42574 [ obo:UO_~ ] ?;
  obo:NCIT_C25688 [obo:NCIT_C115935 obo:NCIT_C3833 obo:NCIT_C25269 obo:GENEPIO_0002020 obo:GENEPIO_0001849 obo:NCIT_C28554 obo:NCIT_C37987 ] ? ;
    efo:EFO_0000727 xsd:string ?;
    obo:VO_0000002 xsd:string {0,10};
    sio:SIO_001167 xsd:string ?;
    sio:SIO_001014 [ obo:HANCESTRO_~ ] ? ; #ethnicity
}

:sampleShape  {
    sio:SIO_000115 xsd:string;
    evs:C25164 xsd:string;
    obo:GAZ_00000448 [wikidata:~] ;
    obo:OBI_0001895 xsd:string ?;
    obo:NCIT_C41206 xsd:string ?;
    obo:OBI_0001479 IRI {0,2};
    obo:OBI_0001472 xsd:string ?;
    sio:SIO_001167 xsd:string ?;
  edam:data_2091 IRI {0,3};
}

:submitterShape {
    obo:NCIT_C42781 xsd:string + ;
    sio:SIO_000116 xsd:string *;
    sio:SIO_000172 xsd:string ?;
    obo:NCIT_C37984 xsd:string ?;
    obo:OBI_0600047 xsd:string ?;
    obo:NCIT_C37900 xsd:string ?;
    efo:EFO_0001741 xsd:string ?;
    obo:NCIT_C19026 xsd:string ?;
    sio:SIO_000115 
    sio:SIO_001167 xsd:string ?;
}

:technologyShape {
    obo:OBI_0600047 IRI {0,3} ;
    efo:EFO_0002699 xsd:string ?;
    obo:FLU_0000848 xsd:double OR xsd:integer {0,3};
    sio:SIO_001167 xsd:string ?;
}

:virusShape{
  edam:data_1875 [ obo:NCBITaxon_~ ] ;
    sio:SIO_010055 xsd:string ?;
}
```

`source:` https://github.com/arvados/bh20-seq-resource/blob/master/bh20sequploader/bh20seq-shex.rdf

Using the [WESO](http://www.weso.es/) developed [RDF shape viewer](http://rdfshape.weso.es/),  Shape Expression can be rendered graphical. In the example below a [schema.org](https://schema.org) base shex expression in presented.

<!-- <div><img src="https://i.imgur.com/z2rriQu.png" link="http://rdfshape.weso.es/shExVisualize?activeSchemaTab=%23schemaTextArea&schema=PREFIX%20%3A%20%3Chttp%3A%2F%2Fwhatever%2F%3E%0APREFIX%20MainSchema%3A%20%3Chttp%3A%2F%2Fbiohackathon.org%2Fbh20-seq-schema%23MainSchema%2F%3E%0APREFIX%20hostSchema%3A%20%3Chttp%3A%2F%2Fbiohackathon.org%2Fbh20-seq-schema%23hostSchema%2F%3E%0APREFIX%20xsd%3A%20%20%3Chttp%3A%2F%2Fwww.w3.org%2F2001%2FXMLSchema%23%3E%0APREFIX%20efo%3A%20%20%20%3Chttp%3A%2F%2Fwww.ebi.ac.uk%2Fefo%2F%3E%0Aprefix%20my%3A%20%3Chttp%3A%2F%2Fexample.org%2F%3E%0Aprefix%20ex%3A%20%3Chttp%3A%2F%2Fexample.org%2F%3E%0Aprefix%20rdf%3A%20%3Chttp%3A%2F%2Fwww.w3.org%2F1999%2F02%2F22-rdf-syntax-ns%23%3E%0Aprefix%20sdo%3A%20%3Chttp%3A%2F%2Fschema.org%2F%3E%0Aprefix%20geo%3A%20%3Chttp%3A%2F%2Fwww.opengis.net%2Font%2Fgeosparql%23%3E%0Aprefix%20sdo%3A%20%3Chttp%3A%2F%2Fschema.org%2F%3E%0A%0A%3ASubmissionShape%20%7B%0A%20%20MainSchema%3Ahost%20%20%20%20%20%20%20%40%3AHost%20%3B%20%0A%20%20MainSchema%3Asubmitter%20%20%40%3ASubmitter%20%3B%0A%20%20MainSchema%3Asample%20%20%20%20%20%40%3ASample%20%3B%0A%20%20MainSchema%3Asubmitter%20%20%40%3ASubmitter%20%3B%0A%20%20MainSchema%3Atechnology%20%40%3ATechnology%20%3B%0A%7D%0A%20%20%0A%3ASubmitter%20%7B%0A%20%20%09sdo%3AinfectiousAgentClass%20xsd%3Astring%3B%0A%20%20%09sdo%3AinfectiousAgent%20xsd%3Astring%0A%7D%20%0A%20%20%0A%3AHost%20%7B%0A%20a%20%5B%20sdo%3APerson%20%20%0A%20%20%20%20%20sdo%3APatient%20%0A%20%20%20%20%20sdo%3AAnimal%20%20%20%23not%20defined%20yet%20in%20sdo%0A%20%5D%3B%20%0A%20sdo%3Aidentifier%20IRI%2B%3B%0A%20sdo%3Aname%20xsd%3Astring%3B%0A%20sdo%3Agender%20%5B%22female%22%20%22male%22%20%22other%22%20%22NA%22%5D%3B%0A%20sdo%3Aage%20xsd%3Ainteger%3B%0A%20sdo%3Aunit%20IRI%2B%3B%0A%20sdo%3AhealthCondition%20IRI%2B%3B%0A%20sdo%3Adrug%20%40%3ADrug%20%2B%3B%0A%20sdo%3AprimaryPrevention%20%40%3APrimaryPrevention%20%3B%0A%20sdo%3Acomment%20xsd%3Astring%20%2B%0A%7D%20%20%0A%20%0A%3ADrug%20IRI%0A%0A%3APrimaryPrevention%20%7B%0A%7D%0A%0A%3ASample%20%7B%0A%20%20%09sdo%3AdateCreated%20xsd%3ADate%3B%0A%20%20%09sdo%3AfromLocation%20IRI%2B%3B%0A%20%20%09sdo%3AfromLocation%20%40geo%3AGeoLocation%3B%20%23to%20defined%20or%20fetch%20from%20existing%0A%20%20%09sdo%3Aidentifier%20IRI%3B%0A%20%20%09sdo%3Asender%20IRI%0A%7D%20%0A%0A%3ASubmitter%20%7B%0A%20%20%09rdf%3Atype%20%5B%20sdo%3APerson%20%5D%3B%0A%20%20%09sdo%3Aidentifier%20IRI%3B%0A%20%20%09sdo%3Afullname%20xsd%3Astring%3B%0A%20%20%09sdo%3Arolename%20xsd%3Astring%3B%0A%20%20%7D%20%0A%0A%3ATechnology%20%7B%0A%20%20%09sdo%3AmeasurementTechnique%20IRI%2B%20%3B%0A%20%20%09sdo%3AmedicalDevice%20IRI%2B%3B%0A%7D%20%0A%0Ageo%3AGeoLocation%20%7B%7D&schemaEmbedded=false&schemaEngine=ShEx&schemaFormat=ShExC&schemaFormatTextArea=ShExC" width="800" style="border:1px solid black"></div>
 -->

```{figure} https://i.imgur.com/z2rriQu.png
---
width: 1200px
name: WESO SHape Expression Viewer
alt: WESO SHape Expression Viewer
---
WESO SHape Expression Viewer
```


<!-- ![](https://i.imgur.com/z2rriQu.png) -->

There is a blog focusing mainly on the sequence analysis but there is a section on metadata validation.

## 5. SPARQL queries available here:

http://covid19.genenetwork.org/blog?id=using-covid-19-pubseq-part1

### 5.1. The SPARQL endpoint

The following endpoint during the Elixir Covid-19 Biohackthon and metadata information converted from the YAML definition to RDF turtle format was loaded in the following SPARQL Endpoint.

http://sparql.genenetwork.org/sparql/

The collection of metadata in rdf format is available for download: https://collections.lugli.arvadosapi.com/c=lugli-4zz18-z513nlpqm03hpca/mergedmetadata.ttl

### 5.2. Exploring the metadata described the FASTQ sequence files

Limiting search to metadata add http://covid-19.genenetwork.org/graph/metadata.ttl in the top input box. Now you can find a predicate for submitter that looks like http://biohackathon.org/bh20-seq-schema#MainSchema/submitter.


```bash
PREFIX pubseq: <http://biohackathon.org/bh20-seq-schema#MainSchema/>
PREFIX sio: <http://semanticscience.org/resource/>
select distinct ?sample ?p ?o
{
   ?sample sio:SIO_000115 "MT326090.1" .
   ?sample ?p ?o .
}
```


---

## Conclusions

> In this recipe, we have presented how to implement a minimal medata profile and validate data entry with a specific technology stack: namely using RDF and Shape Express standard. 
> Other approaches are possible and we provide details in a dedicated recipe where [JSON schema]() and [JSON-LD]() technologies are used. 
> This recipe tackles an important aspect of the FAIR principles, shining the light on the need to provide sufficient descriptive metadata to associate with an assay data file to allow its correct interpretation.
> The recipe therefore provides a piece of the jigsaw to establish a FAIR datasets.
> There are some caveats or improvements which could be made. For instance, the devised shex expression and the associated instance RDF graph  could be assigned a persistent identifiers (PID).
> Another improvement could be a better integration with repositories such FAIRsharing or the main sequence data submission systems such as INSDC deposition pipelines.
>
> ### What should I read next?
> * [How to validate a FASTQ sequencing file?]()
> * [How to express Minimal Metadata checklist in machine readable format]()
> * [How to validate metadata with JSON Schema?]()
> * [How to perform data integration with SPARQL?]()


## FAIRification Objectives, Inputs and Outputs

| Actions.Objectives.Tasks  | Input | Output  |
| :------------- | :------------- | :------------- |
| [semantic markup]()|[text]()  | [URI]()|
| [constraint validation]()  | [text]()  | [DOI]()  |
||file||

## Table of Data Standards

| Data Formats  | Terminologies | Models  |
| :------------- | :------------- | :------------- |
| [YAML](https://yaml.org)  | [EFO](https://fairsharing.org/FAIRsharing.1gr4tz)  |  [MIxS](https://fairsharing.org/bsg-s000518/) |
| [RDF](https://www.w3.org/TR/rdf11-concepts/)|[SIO](https://fairsharing.org/FAIRsharing.dpkb5f)|[MIUVIG]()|
| [SPARQL 1.1](https://www.w3.org/TR/sparql11-query/)|[schema.org](https://fairsharing.org/FAIRsharing.hzdzq8)||
| [Shape Expression Syntax (SHEX)](https://shex.io/shex-semantics/)|[EDAM](https://fairsharing.org/FAIRsharing.a6r7zs)||
| |[OBO foundry](https://fairsharing.org/biodbcore-001083/)||
| |[Wikidata](https://fairsharing.org/FAIRsharing.PB6595)||

## Tools
| Tool Name| capability|
|:--|:--|
| [Semantic Annotation for Linked Arvado Data (SALAD)](https://www.commonwl.org/draft-3/SchemaSalad.html)  | conversion from  YAML to RDF |
|[WESO shExVisualize](http://rdfshape.weso.es/shExVisualize) | Shape expression syntax visualization |
| [Virtuoso](https://virtuoso.openlinksw.com/) | RDF triple store |




___ 

## Bibliographic reference


[1]. Avro - http://avro.apache.org
[2]. metaschema - https://github.com/common-workflow-language/schema_salad/blob/main/schema_salad/metaschema/metaschema.yml
[3]. schema salad - http://www.commonwl.org/v1.0/SchemaSalad.html
[4]. https://www.w3.org/RDF/
[5]. https://shex.io/shex-semantics/


___

## Authors

| Name | Affiliation  | [Orcid](https://orcid.org) | [CrediT role](https://casrai.org/credit/)  |
| :------------- | :------------- | :------------- |:------------- |
| <div class="firstCol"><a target="_blank" href='https://github.com/proccaserra'><img class='avatar-style' src='https://avatars.githubusercontent.com/proccaserra'></img><div class="d-block">Philippe Rocca-Serra</div></a></div>| University of Oxford |<a target="_blank" href='https://orcid.org/0000-0001-9853-5668'><i class='fab fa-orcid fa-2x text--orange'></i></a> | Writing - Original Draft |
| <div class="firstCol"><a target="_blank" href='https://github.com/daniwelter'><img class='avatar-style' src='https://avatars.githubusercontent.com/daniwelter'></img><div class="d-block">Danielle Welter</div></a></div>| LCSB, University of Luxembourg|<a target="_blank" href='https://orcid.org/0000-0003-1058-2668'><i class='fab fa-orcid fa-2x text--orange'></i></a> | Writing – Review & Editing|
|<div class="firstCol"><a target="_blank" href='https://github.com/robertgiessmann'><img class='avatar-style' src='https://avatars.githubusercontent.com/robertgiessmann'></img><div class="d-block">Robert Giessmann</div></a> </div>|Bayer AG|<a target="_blank" href='https://orcid.org/0000-0002-0254-1500'><i class='fab fa-orcid fa-2x text--orange'></i></a>|Writing – Review & Editing|

___

## License

This page is released under the Creative Commons 4.0 BY license.

<a href="https://creativecommons.org/licenses/by/4.0/"><img src="https://mirrors.creativecommons.org/presskit/buttons/80x15/png/by.png" height="20"/></a>