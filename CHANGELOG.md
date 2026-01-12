# Changelog

All notable changes to the Meeting Transcript Ontology will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-01-11

### Added
- Initial release of Meeting Transcript Ontology
- Core ontology with 11 classes:
  - Meeting
  - Participant  
  - Utterance
  - Intent
  - Decision
  - Vote
  - IndividualVote
  - Discussion
  - AgendaItem
  - ActionItem
  - EntityMention
  - VirtualLocation
  - PhysicalLocation
  - Transcript
- 40+ properties with SHACL constraints
- 6 SHACL rules for automated inference:
  - Calculate meeting duration
  - Calculate relative time for utterances
  - Infer discussion participants
  - Calculate vote totals
  - Determine vote outcome
  - Infer agenda item status
- 4 validation constraints:
  - Minimum utterances per meeting
  - Decision provenance requirement
  - Timestamp ordering validation
  - Utterance time bounds checking
- RDF-Star annotation patterns for provenance and confidence
- 13 intent types for utterance classification
- 6 decision types for outcome tracking
- Integration with Wikidata for entity linking
- Support for FOAF, PROV-O, Schema.org vocabularies
- Example meeting transcript with realistic scenarios
- Complete RDF instance data demonstrating all features
- Comprehensive documentation:
  - README with architecture overview
  - Quick start guide
  - 15 example SPARQL queries
  - Mermaid knowledge graph visualization
  - Interactive HTML graph viewer
  - Setup instructions for GitHub
  - Original project requirements
- MIT License
- Python and Node.js example code
- Support for both virtual and physical meeting locations
- Email-based participant identification
- Dual temporal tracking (absolute + relative time)
- Vote tracking with individual vote records
- Action item management
- Sentiment analysis support

### Technical Details
- SHACL 1.2 compliance
- RDF 1.1 with RDF-Star support
- Turtle serialization format
- Compatible with SPARQL 1.1
- Designed for Apache Jena, GraphDB, Stardog, AllegroGraph

### Documentation
- Full README.md with use cases and examples
- Quick start guide for Python, Node.js, and Fuseki
- 15 example SPARQL queries covering common scenarios
- GitHub repository setup instructions
- Original project prompt documentation

## [Unreleased]

### Planned Features
- Audio/video timestamp linking
- Enhanced sentiment analysis with fine-grained emotions
- Integration with common transcription services (Teams, Zoom, Google Meet)
- Visualization examples using D3.js or similar
- Python conversion pipeline for automated transcript processing
- Node.js conversion toolkit
- Extended entity types beyond Wikidata
- Meeting series and recurrence patterns
- Privacy and access control patterns
- Multi-language support
- Named entity recognition examples
- Integration with calendar systems (iCal, Google Calendar)
- Topic modeling integration
- Consensus measurement algorithms

### Future Considerations
- Integration with specific LLM APIs for automated analysis
- Real-time streaming support for live meetings
- Differential privacy patterns for sensitive meetings
- Federated query patterns across multiple organizations
- Version control for decision amendments
- Cross-meeting relationship tracking
- Organizational structure integration

## Contributing

See [SETUP_INSTRUCTIONS.md](SETUP_INSTRUCTIONS.md) for how to set up the repository and contribute.

## Version History

- **v1.0.0** (2026-01-11): Initial stable release
