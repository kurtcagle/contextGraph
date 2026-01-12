# Quick Start Guide

This guide will help you get started with the Meeting Transcript Ontology in under 5 minutes.

## Prerequisites

You'll need one of the following setups:

**Option 1: Python**
- Python 3.7+
- RDFLib library: `pip install rdflib`

**Option 2: Node.js**
- Node.js 14+
- N3.js library: `npm install n3`

**Option 3: SPARQL Endpoint**
- Apache Jena Fuseki, GraphDB, or any SPARQL 1.1 compliant triple store

## Quick Start with Python

### 1. Install Dependencies

```bash
pip install rdflib
```

### 2. Load and Query the Ontology

```python
from rdflib import Graph, Namespace
from rdflib.namespace import RDF, DCTERMS

# Create a new graph
g = Graph()

# Load the ontology
g.parse("ontology/meeting-transcript-ontology.ttl", format="turtle")

# Load example data
g.parse("examples/meeting-transcript-rdf.ttl", format="turtle")

# Define namespace
MTO = Namespace("http://example.org/meeting-transcript/ontology#")
g.bind("mto", MTO)

# Query for all decisions
print("Decisions in the meeting:")
for decision in g.subjects(RDF.type, MTO.Decision):
    title = g.value(decision, DCTERMS.title)
    dec_type = g.value(decision, MTO.decisionType)
    print(f"  - {title}: {dec_type}")
```

### 3. Run a SPARQL Query

```python
query = """
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX dcterms: <http://purl.org/dc/terms/>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT ?speaker ?count
WHERE {
  SELECT ?speaker (COUNT(?utterance) AS ?count)
  WHERE {
    ?utterance a mto:Utterance ;
               mto:speaker ?speakerNode .
    ?speakerNode foaf:name ?speaker .
  }
  GROUP BY ?speaker
}
ORDER BY DESC(?count)
"""

print("\nSpeaker participation:")
for row in g.query(query):
    print(f"  {row.speaker}: {row.count} utterances")
```

## Quick Start with Node.js

### 1. Install Dependencies

```bash
npm install n3
```

### 2. Load and Query the Ontology

```javascript
const fs = require('fs');
const N3 = require('n3');

// Create a new store
const store = new N3.Store();
const parser = new N3.Parser();

// Load ontology
const ontology = fs.readFileSync('ontology/meeting-transcript-ontology.ttl', 'utf8');
const instances = fs.readFileSync('examples/meeting-transcript-rdf.ttl', 'utf8');

// Parse and add to store
parser.parse(ontology, (error, quad, prefixes) => {
  if (quad) store.addQuad(quad);
});

parser.parse(instances, (error, quad, prefixes) => {
  if (quad) store.addQuad(quad);
  else {
    // Query decisions
    const decisions = store.getQuads(
      null,
      'http://www.w3.org/1999/02/22-rdf-syntax-ns#type',
      'http://example.org/meeting-transcript/ontology#Decision'
    );
    
    console.log(`Found ${decisions.length} decisions`);
    
    decisions.forEach(quad => {
      const title = store.getQuads(
        quad.subject,
        'http://purl.org/dc/terms/title',
        null
      )[0];
      
      if (title) {
        console.log(`  - ${title.object.value}`);
      }
    });
  }
});
```

## Quick Start with Apache Jena Fuseki

### 1. Install Fuseki

Download from: https://jena.apache.org/download/

### 2. Start Fuseki

```bash
./fuseki-server --mem /meetings
```

### 3. Load Data via HTTP

```bash
# Load ontology
curl -X POST -H "Content-Type: text/turtle" \
  --data-binary @ontology/meeting-transcript-ontology.ttl \
  http://localhost:3030/meetings/data

# Load instance data
curl -X POST -H "Content-Type: text/turtle" \
  --data-binary @examples/meeting-transcript-rdf.ttl \
  http://localhost:3030/meetings/data
```

### 4. Query via Web Interface

Open http://localhost:3030 in your browser and navigate to the query interface.

Try this query:

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX dcterms: <http://purl.org/dc/terms/>

SELECT ?title ?type
WHERE {
  ?decision a mto:Decision ;
            dcterms:title ?title ;
            mto:decisionType ?type .
}
```

## Common Tasks

### Validating Data with SHACL

Using Apache Jena:

```bash
shacl validate --shapes=ontology/meeting-transcript-ontology.ttl \
               --data=examples/meeting-transcript-rdf.ttl
```

Using Python (pySHACL):

```python
from pyshacl import validate

# Load shapes and data
r = validate(
    data_graph="examples/meeting-transcript-rdf.ttl",
    shacl_graph="ontology/meeting-transcript-ontology.ttl",
    inference='rdfs',
    abort_on_first=False
)

conforms, results_graph, results_text = r
print(f"Conforms: {conforms}")
print(results_text)
```

### Converting Your Own Transcripts

The basic workflow:

1. **Parse the transcript** - Extract speaker, timestamp, and text
2. **Create participants** - Map speakers to email addresses
3. **Generate utterances** - Create `mto:Utterance` instances with timestamps
4. **Classify intents** - Use NLP to determine intent types
5. **Extract entities** - Link to Wikidata or other knowledge bases
6. **Identify decisions** - Look for voting patterns and decision language
7. **Link relationships** - Connect utterances to agenda items and decisions

See `docs/conversion-pipeline.md` for a detailed guide (coming soon).

### Extending the Ontology

To add custom properties:

```turtle
@prefix mto: <http://example.org/meeting-transcript/ontology#> .
@prefix myco: <http://example.com/my-company/ontology#> .

# Add a custom property to Meeting
myco:hasRecordingUrl a rdf:Property ;
    rdfs:label "has recording URL" ;
    rdfs:domain mto:Meeting ;
    rdfs:range xsd:anyURI .

# Use it in your data
mt:meeting-20260111 myco:hasRecordingUrl 
    "https://example.com/recordings/meeting-20260111.mp4"^^xsd:anyURI .
```

## Next Steps

- Explore the [Example Queries](example-queries.md) for more SPARQL examples
- Read the [Blog Post](https://example.com/blog) on Context Graphs and Transcriptions
- Check out the full ontology documentation in the `ontology/` directory
- Join the discussion in GitHub Issues

## Troubleshooting

**Q: My SHACL validation is failing**
A: Make sure you have both `xsd:dateTime` values properly formatted with timezone information.

**Q: RDF-Star queries aren't working**
A: Ensure your triple store supports RDF-Star (RDF 1.1). Try Apache Jena 4.0+ or GraphDB.

**Q: Entity extraction seems incomplete**
A: The example uses manual entity linking. Consider integrating with NLP services like spaCy, DBpedia Spotlight, or OpenAI for automated extraction.

## Resources

- [W3C SHACL Specification](https://www.w3.org/TR/shacl/)
- [RDFLib Documentation](https://rdflib.readthedocs.io/)
- [Apache Jena Documentation](https://jena.apache.org/documentation/)
- [SPARQL 1.1 Query Language](https://www.w3.org/TR/sparql11-query/)
