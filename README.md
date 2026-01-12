# Meeting Transcript Ontology

A comprehensive SHACL 1.2 ontology for converting meeting transcriptions into structured RDF knowledge graphs, enabling organizational decision tracking and analysis.

## Overview

This ontology provides a semantic framework for transforming unstructured meeting transcripts into rich, queryable RDF data. It captures not just *what* was said, but *who* said it, *when*, the *intent* behind statements, and how discussions lead to decisions. By integrating with knowledge bases like Wikidata, it enables sophisticated entity linking and taxonomic classification.

The system is designed to support organizational intelligence initiatives, compliance tracking, knowledge management, and decision provenance—answering questions like "How did we arrive at this decision?" or "What discussions have we had about AI ethics?"

## Use Cases

- **Decision Provenance**: Track how organizational decisions evolve from proposals through discussion to formal votes
- **Meeting Analytics**: Analyze discussion patterns, participant engagement, and consensus-building
- **Compliance & Governance**: Maintain auditable records of decision-making processes
- **Knowledge Management**: Extract and link entities mentioned in meetings to organizational knowledge graphs
- **Context Graphs**: Build rich contextual networks showing relationships between people, decisions, topics, and time

## Features

### Core Capabilities

- **Speaker Identification**: Uses email addresses as primary identifiers for participants
- **Temporal Tracking**: Dual time representation (absolute `xsd:dateTime` + relative duration from meeting start)
- **Intent Classification**: 13 intent types including proposals, objections, amendments, motions, and votes
- **Decision Modeling**: Comprehensive tracking of proposals, discussions, voting records, and outcomes
- **Entity Extraction**: Integration with Wikidata taxonomy for linking mentioned entities
- **RDF-Star Support**: Rich provenance and confidence annotations on statements
- **Location Handling**: Support for both virtual (Teams, Google Meet) and physical meeting locations

### SHACL Components

The ontology includes:

- **11 Core Classes**: Meeting, Participant, Utterance, Decision, Vote, Discussion, AgendaItem, ActionItem, Intent, EntityMention, and Location types
- **6 SHACL Rules**: Automated computation of durations, relative times, vote tallies, and derived properties
- **4 Validation Constraints**: Data quality checks for temporal consistency and decision provenance
- **Comprehensive Property Definitions**: 40+ properties with cardinality constraints and datatypes

### Intent Types Supported

```turtle
"question" | "answer" | "proposal" | "support" | "objection" | 
"clarification" | "information" | "procedural" | "motion" | 
"amendment" | "vote-call" | "acknowledgment" | "summary"
```

### Decision Types

```turtle
"approved" | "rejected" | "tabled" | "deferred" | "amended" | "withdrawn"
```

## Architecture

The ontology follows semantic web best practices:

```
Meeting
├── Participants (identified by email)
├── Location (Virtual or Physical)
├── Agenda Items
│   └── Related Decisions
├── Utterances
│   ├── Speaker
│   ├── Timestamp & Relative Time
│   ├── Intent Classification
│   └── Entity Mentions (Wikidata links)
├── Decisions
│   ├── Proposal & Rationale
│   ├── Discussion Thread
│   ├── Vote Record
│   │   └── Individual Votes
│   └── Action Items
└── Transcript
```

## Files in This Repository

- **`meeting-transcript-ontology.ttl`**: The complete SHACL 1.2 ontology definition
- **`sample-meeting-transcript.txt`**: A realistic meeting transcript demonstrating various decision scenarios
- **`meeting-transcript-rdf.ttl`**: RDF instance data converted from the sample transcript
- **`meeting-transcript-graph.mmd`**: Mermaid diagram visualizing the knowledge graph structure
- **`meeting-transcript-graph.html`**: Interactive HTML visualization (open in browser)
- **`original-prompt.txt`**: Original project requirements and specifications

## Example Usage

### Sample RDF Triple

```turtle
mt:decision-ai-adoption a mto:Decision ;
    dcterms:title "Adopt Claude for Customer Support Automation" ;
    mto:decisionType "approved" ;
    mto:proposedBy mt:participant-michael ;
    mto:decidedAt "2026-01-11T10:11:00-08:00"^^xsd:dateTime ;
    mto:hasVote mt:vote-ai-adoption ;
    prov:wasDerivedFrom mt:utterance-002, mt:utterance-006 ;
    mto:rationale "Claude provides superior response quality at reasonable cost..." .
```

### Sample RDF-Star Annotation

```turtle
<<mt:decision-ai-adoption mto:decisionType "approved">> 
    prov:wasAttributedTo mt:participant-sarah ;
    dcterms:created "2026-01-11T10:11:00-08:00"^^xsd:dateTime ;
    mto:confidence 1.0 ;
    mto:sourceUtterance mt:utterance-006 .
```

### Visual Knowledge Graph

A comprehensive Mermaid diagram showing the complete knowledge graph structure is available in `docs/meeting-transcript-graph.mmd`. You can view it interactively by opening `docs/meeting-transcript-graph.html` in your browser, or render it in any Mermaid-compatible viewer (GitHub, GitLab, VS Code, etc.).

### SPARQL Query Example

Find all approved decisions with unanimous votes:

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>

SELECT ?decision ?title ?votedAt
WHERE {
  ?decision a mto:Decision ;
            dcterms:title ?title ;
            mto:decisionType "approved" ;
            mto:hasVote ?vote .
  
  ?vote mto:votedAt ?votedAt ;
        mto:votesFor ?for ;
        mto:votesAgainst ?against ;
        mto:votesAbstain ?abstain .
  
  FILTER(?against = 0 && ?abstain = 0)
}
ORDER BY DESC(?votedAt)
```

## Integration Points

### Wikidata Entity Linking

The ontology uses `mto:mentionsEntity` to link utterances to Wikidata concepts:

```turtle
mt:utterance-002 mto:mentionsEntity wd:Q126418099,  # Claude (AI assistant)
                                     wd:Q108266254 . # Anthropic
```

### Provenance with PROV-O

Leverages W3C PROV ontology for decision provenance:

```turtle
mt:decision-budget prov:wasDerivedFrom mt:utterance-011, mt:utterance-013 .
```

### FOAF for People

Uses Friend of a Friend (FOAF) vocabulary for participant identification:

```turtle
mt:participant-sarah foaf:mbox "sarah.johnson@example.com" ;
                     foaf:name "Sarah Johnson" .
```

## Technical Requirements

- **RDF 1.1**: Full support for RDF triples and RDF-Star quoted triples
- **SHACL 1.2**: Core constraints, rules, and SPARQL-based validation
- **Turtle Syntax**: Primary serialization format
- **Triple Store**: Compatible with any SPARQL 1.1 compliant store (Apache Jena Fuseki, GraphDB, Stardog, etc.)

## Implementation Suggestions

### Python with RDFLib

```python
from rdflib import Graph, Namespace
from rdflib.namespace import RDF, RDFS

# Load the ontology
g = Graph()
g.parse("meeting-transcript-ontology.ttl", format="turtle")

# Load instance data
g.parse("meeting-transcript-rdf.ttl", format="turtle")

# Query for decisions
MTO = Namespace("http://example.org/meeting-transcript/ontology#")
g.bind("mto", MTO)

for decision in g.subjects(RDF.type, MTO.Decision):
    print(f"Decision: {g.value(decision, DCTERMS.title)}")
```

### Node.js with N3.js

```javascript
const N3 = require('n3');
const fs = require('fs');

const parser = new N3.Parser();
const store = new N3.Store();

const ontology = fs.readFileSync('meeting-transcript-ontology.ttl', 'utf8');

parser.parse(ontology, (error, quad, prefixes) => {
  if (quad) store.addQuad(quad);
  else {
    // Query the store
    const decisions = store.getQuads(null, 'http://www.w3.org/1999/02/22-rdf-syntax-ns#type', 
                                     'http://example.org/meeting-transcript/ontology#Decision');
    console.log(`Found ${decisions.length} decisions`);
  }
});
```

## Validation

The ontology includes built-in SHACL validation rules:

```bash
# Using Apache Jena's shacl tool
shacl validate --shapes=meeting-transcript-ontology.ttl --data=meeting-transcript-rdf.ttl
```

## Extending the Ontology

The ontology is designed to be extended. Common extensions:

1. **Domain-Specific Intents**: Add industry-specific intent types
2. **Custom Metrics**: Add properties for measuring meeting effectiveness
3. **Integration with Calendar Systems**: Link to external calendar events
4. **Audio/Video References**: Add properties linking to recording timestamps
5. **Sentiment Analysis**: Expand sentiment classification beyond basic categories

## Related Work

This ontology builds upon and integrates with:

- [W3C SHACL](https://www.w3.org/TR/shacl/) - Shapes Constraint Language
- [W3C PROV-O](https://www.w3.org/TR/prov-o/) - Provenance Ontology
- [FOAF](http://xmlns.com/foaf/spec/) - Friend of a Friend
- [Schema.org](https://schema.org) - Event and Person modeling
- [Wikidata](https://www.wikidata.org) - Entity linking and taxonomy

## Blog Post Context

This ontology is featured in an upcoming blog post on **Context Graphs and Transcriptions**, exploring how semantic technologies can transform unstructured meeting data into actionable organizational intelligence. The post demonstrates:

- Building context graphs from conversational data
- Tracing decision lineage through organizations
- Integrating meeting data with knowledge graphs
- Using SHACL rules for automated knowledge extraction

## License

MIT License - See LICENSE file for details

## Contributing

Contributions welcome! Areas of interest:

- Additional SHACL validation rules
- Performance optimization for large meeting datasets
- Integration with transcription services (Teams, Zoom, etc.)
- Visualization tools for decision graphs
- Machine learning pipelines for intent classification

## Contact

For questions or collaboration opportunities, please open an issue in this repository.

## Citation

If you use this ontology in academic work, please cite:

```bibtex
@software{meeting_transcript_ontology,
  title = {Meeting Transcript Ontology: A SHACL-based Framework for Decision Tracking},
  author = {Cagle, Kurt},
  year = {2026},
  url = {https://github.com/[your-username]/meeting-transcript-ontology}
}
```

---

**Keywords**: SHACL, RDF, Semantic Web, Meeting Intelligence, Decision Tracking, Knowledge Graphs, Context Graphs, Organizational Intelligence, Provenance, RDF-Star
