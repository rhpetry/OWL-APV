# OWL-APV

OWL-APV is the canonical repository for the OWL vocabulary of the **Annotation Property Verificator (APV)** methodology. The vocabulary enables ontology authors to declare annotation-quality requirements—such as documentation coverage, multilingual coverage, value lengths, URI patterns, and annotation formats—in a human-readable and machine-processable form. This repository distributes the vocabulary itself; it does not contain an executable validator or software library.

## What is APV?

Annotation Property Verificator is a methodology developed at the Federal University of Rio Grande do Sul (UFRGS) to safeguard consistency in ontology annotation properties.

In OWL, an annotation property associates descriptive metadata with a resource. Common examples include `rdfs:label`, `skos:definition`, and `skos:example`. APV lets ontology authors describe quality requirements for these annotations inside the ontology being governed.

The methodology has two complementary parts:

1. The APV OWL vocabulary, distributed here as [`APV.rdf`](APV.rdf), which defines the available constraints.
2. Separately maintained validation applications that read those declarations and check whether an ontology conforms to them: the browser-based [Web-APV](WEB_APV_REPOSITORY_URL) and the command-line [CLI-APV](CLI_APV_REPOSITORY_URL).

APV constraints are declarative. The vocabulary expresses the requirements but does not enforce them by itself; enforcement requires a compatible APV validation engine.

## Motivation

Consistent annotations support:

- Clear and complete ontology documentation.
- Predictable multilingual coverage.
- Better discovery and interpretation of ontology entities.
- Easier long-term maintenance.
- Reliable automated processing of labels, definitions, examples, and other metadata.

Without explicit requirements, similar entities may be documented unevenly, annotations may omit required languages, and values may follow incompatible formatting conventions. APV makes such expectations explicit and available to both people and validation tools.

## Repository contents

| Artifact | Description |
|---|---|
| [`APV.rdf`](APV.rdf) | The APV OWL vocabulary serialized as RDF/XML. |
| `README.md` | Documentation for understanding and applying the vocabulary. |

This repository does not contain the APV validator, an API, or an application package.

## Ontology identity

| Field | Value |
|---|---|
| Title | Annotation Property Verificator |
| Ontology IRI | `http://www.inf.ufrgs.br/ontologies/APV` |
| Namespace | `http://www.inf.ufrgs.br/ontologies/APV#` |
| Suggested prefix | `apv` |
| Serialization | RDF/XML |
| Main artifact | `APV.rdf` |
| Creator | Rafael Humann Petry |
| Contributors | Nicolau O. Santos, Haroldo R. S. Silva, Mara Abel, and Joao C. Netto |
| Institution | Federal University of Rio Grande do Sul (UFRGS) |

## Constraint model

APV constraints are OWL annotation properties arranged according to where they apply:

- **Ontology-header constraints** annotate an `owl:Ontology` and establish ontology-wide requirements.
- **Annotation-property constraints** annotate an `owl:AnnotationProperty` and govern values expressed through that property.
- **Class-level constraints** annotate an `owl:Class` and establish requirements for instances of that class.

The vocabulary also defines organizational parent properties:

| Property | Role |
|---|---|
| `apv:AnnotationPropertyVerificatorAnnotation` | Root annotation property for APV methodology constraints. |
| `apv:OntologyHeaderAnnotation` | Parent for constraints placed on an ontology header. |
| `apv:AnnotationPropertyDefinitionAnnotation` | Parent for constraints placed on an `owl:AnnotationProperty`. |
| `apv:ClassDefinitionAnnotation` | Parent for constraints placed on an `owl:Class`. |

These parent properties organize the vocabulary hierarchy. They are not presented as independent validation checks.

## Constraint reference

| Exact term | Applied to | Accepted value | Purpose |
|---|---|---|---|
| `apv:ClassURIFormationRule` | `owl:Ontology` header | `apv:regularExpression` | Requires class URIs to follow a declared regular-expression pattern. |
| `apv:RelationURIFormationRule` | `owl:Ontology` header | `apv:regularExpression` | Requires relation—or object-property—URIs to follow a declared regular-expression pattern. |
| `apv:InstanceURIFormationRule` | `owl:Ontology` header | `apv:regularExpression` | Requires instance URIs to follow a declared regular-expression pattern. |
| `apv:GlobalMinimumLanguageCoverage` | `owl:Ontology` header | `apv:languageCoverage` | Declares the language tags required for annotation properties expressed as RDF plain literals. |
| `apv:ClassMinAnnotationCoverage` | `owl:Ontology` header | `apv:annotationCoverage` | Declares mandatory annotation properties and minimum cardinalities for classes. |
| `apv:RelationMinAnnotationCoverage` | `owl:Ontology` header | `apv:annotationCoverage` | Declares mandatory annotation properties and minimum cardinalities for relations (object properties). |
| `apv:InstanceMinAnnotationCoverage` | `owl:Ontology` header | `apv:annotationCoverage` | Declares mandatory annotation properties and minimum cardinalities for instances. |
| `apv:MinAnnotationLength` | `owl:AnnotationProperty` | `apv:length` | Sets the minimum character length of values expressed through the annotated property. |
| `apv:MaxAnnotationLength` | `owl:AnnotationProperty` | `apv:length` | Sets the maximum character length of values expressed through the annotated property. |
| `apv:AnnotationRegularExpression` | `owl:AnnotationProperty` | `apv:regularExpression` | Requires values expressed through the annotated property to match a regular expression. |
| `apv:InstanceOfMinAnnotationCoverage` | `owl:Class` | `apv:annotationCoverage` | Declares mandatory annotations and minimum cardinalities for instances of a particular class. |

Ontology authors choose which constraints and values are appropriate for their own quality requirements. The examples below are illustrative configurations, not mandatory APV defaults.

## Value syntax

APV defines four custom datatypes for constraint values.

### Annotation coverage: `apv:annotationCoverage`

Coverage values contain whitespace-separated annotation-property identifiers:

```text
rdfs:label skos:definition skos:example
```

A required minimum cardinality uses the form `number^property`:

```text
rdfs:label skos:definition 2^skos:example
```

Here, at least one `rdfs:label`, at least one `skos:definition`, and at least two `skos:example` annotations are required. A property without an explicit cardinality defaults to a minimum of one.

The ontology describes these frequencies per language tag.

### Language coverage: `apv:languageCoverage`

Language coverage is a whitespace-separated sequence of IANA language tags:

```text
en-US es pt-BR
```

### Length: `apv:length`

Length constraints use positive integer values:

```text
20
```

The ontology also permits an empty value to represent no restriction.

### Regular expressions: `apv:regularExpression`

Regular expressions are used by URI formation constraints and `apv:AnnotationRegularExpression`:

```text
MyOntoClass_[0-9]{5}
```

The interpretation and evaluation of the pattern are performed by the compatible validation engine.

## Usage

The examples in this section use Turtle only to improve readability. The vocabulary distributed by this repository is serialized as RDF/XML.

### Using APV in Protégé

1. Download or clone this repository.
2. Open `APV.rdf` in Protégé, or reference/import the vocabulary from the ontology being configured.
3. Register the `apv` prefix as `http://www.inf.ufrgs.br/ontologies/APV#` under **Active Ontology → Ontology Prefixes** if Protégé does not add it automatically.
4. Add ontology-wide APV constraints to the annotations under **Active Ontology**.
5. Add property-specific constraints to the relevant `owl:AnnotationProperty`.
6. Add `apv:InstanceOfMinAnnotationCoverage` to the relevant `owl:Class`.
7. Save the ontology and evaluate it with a compatible APV validator.

### Referencing or importing the vocabulary

Use the exact APV namespace in prefix declarations:

```turtle
@prefix apv:  <http://www.inf.ufrgs.br/ontologies/APV#> .
@prefix owl:  <http://www.w3.org/2002/07/owl#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .
```

An ontology can use APV property IRIs directly without asserting an OWL import. Importing the vocabulary with `owl:imports` additionally makes its axioms and term declarations part of the importing ontology’s import closure. Whether to import it or only reference its IRIs depends on the ontology’s dependency-management requirements.

If an import is used, it refers to the ontology IRI without the namespace fragment:

```turtle
<https://example.org/my-ontology>
    a owl:Ontology ;
    owl:imports <http://www.inf.ufrgs.br/ontologies/APV> .
```

### Declaring ontology-level constraints

The following example configures a class URI pattern, language coverage, and minimum class annotation coverage:

```turtle
@prefix apv:  <http://www.inf.ufrgs.br/ontologies/APV#> .
@prefix owl:  <http://www.w3.org/2002/07/owl#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .

<https://example.org/my-ontology>
    a owl:Ontology ;
    apv:ClassURIFormationRule "MyOntoClass_[0-9]{5}" ;
    apv:GlobalMinimumLanguageCoverage "en-US es pt-BR" ;
    apv:ClassMinAnnotationCoverage
        "rdfs:label skos:prefLabel skos:definition 2^skos:example" .
```

Other ontology-header constraints can be added in the same way.

### Declaring annotation-property constraints

Constraints placed on an annotation property govern values expressed through that property:

```turtle
@prefix apv:  <http://www.inf.ufrgs.br/ontologies/APV#> .
@prefix owl:  <http://www.w3.org/2002/07/owl#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .

rdfs:label
    a owl:AnnotationProperty ;
    apv:MinAnnotationLength 20 ;
    apv:MaxAnnotationLength 400 ;
    apv:AnnotationRegularExpression "[a-zA-ZÀ-ú.;,]*" .
```

### Declaring class-specific instance constraints

`apv:InstanceOfMinAnnotationCoverage` applies annotation requirements to every instance of the annotated class:

```turtle
@prefix apv:           <http://www.inf.ufrgs.br/ontologies/APV#> .
@prefix owl:           <http://www.w3.org/2002/07/owl#> .
@prefix my:            <https://example.org/ontology#> .
@prefix myAnnotations: <https://example.org/annotations#> .

my:MyClass
    a owl:Class ;
    apv:InstanceOfMinAnnotationCoverage
        "myAnnotations:llmFriendlyDescription" .
```

## Validation and related applications

APV declarations encode quality requirements; they do not execute validation. A compatible validation engine must discover the declarations, examine the governed ontology entities and annotations, and report any violations.

Two verification applications are maintained separately from this ontology repository:

- [Web-APV](WEB_APV_REPOSITORY_URL) is the browser-based companion validator that discovers APV constraints and reports violations.
- [CLI-APV](CLI_APV_REPOSITORY_URL) is the command-line companion validator.

These applications consume the APV vocabulary; neither is included in OWL-APV.

## Opening and inspecting APV.rdf

`APV.rdf` is an RDF/XML document. It can be:

- Opened directly in Protégé.
- Inspected with an RDF/XML-aware editor.
- Loaded into an RDF or OWL tool that supports RDF/XML.
- Converted temporarily to another RDF serialization for inspection, while retaining the exact ontology and term IRIs.

The declarations, descriptions, examples, hierarchy, and datatype definitions in `APV.rdf` are the authoritative source for the vocabulary.

## Contributing

Contributions should preserve the ontology IRI, namespace capitalization, vocabulary term names, hierarchy, and established semantics. Proposed changes should be checked against `APV.rdf` and should keep descriptions and examples consistent with the corresponding OWL declarations.

When proposing a new constraint or changing an existing one, document:

- The intended target, such as `owl:Ontology`, `owl:AnnotationProperty`, or `owl:Class`.
- Its accepted value format.
- Its validation semantics.
- A concise example.
- Its relationship to the existing APV property hierarchy.

## Citation

A publication DOI or finalized bibliographic citation is not currently provided in this repository. This section will be updated when citation information becomes available.

## License

License information has not yet been provided.

## Authors and acknowledgements

**Creator:** Rafael Humann Petry

**Contributors:** Nicolau O. Santos, Haroldo R. S. Silva, Mara Abel, and Joao C. Netto

Annotation Property Verificator was developed at the **Federal University of Rio Grande do Sul (UFRGS)**.
