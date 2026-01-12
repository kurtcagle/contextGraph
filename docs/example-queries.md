# Example SPARQL Queries for Meeting Transcript Ontology

## Query 1: Find All Decisions with Vote Outcomes

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX dcterms: <http://purl.org/dc/terms/>

SELECT ?decision ?title ?outcome ?votedAt
WHERE {
  ?decision a mto:Decision ;
            dcterms:title ?title ;
            mto:hasVote ?vote .
  
  ?vote mto:outcome ?outcome ;
        mto:votedAt ?votedAt .
}
ORDER BY DESC(?votedAt)
```

## Query 2: Find All Utterances by a Specific Speaker

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT ?utterance ?timestamp ?text
WHERE {
  ?speaker foaf:mbox "sarah.johnson@example.com" .
  
  ?utterance a mto:Utterance ;
             mto:speaker ?speaker ;
             mto:timestamp ?timestamp ;
             rdf:value ?text .
}
ORDER BY ?timestamp
```

## Query 3: Find All Proposals and Their Outcomes

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX dcterms: <http://purl.org/dc/terms/>

SELECT ?utterance ?speaker ?proposalText ?decision ?outcome
WHERE {
  ?utterance a mto:Utterance ;
             mto:speaker ?speakerNode ;
             mto:intent ?intent ;
             rdf:value ?proposalText .
  
  ?speakerNode foaf:name ?speaker .
  
  ?intent mto:intentType "proposal" .
  
  ?decision prov:wasDerivedFrom ?utterance ;
            mto:decisionType ?outcome .
}
```

## Query 4: Track Discussion Flow for an Agenda Item

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX dcterms: <http://purl.org/dc/terms/>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT ?seqNum ?speaker ?timestamp ?intent ?text
WHERE {
  ?agendaItem dcterms:identifier "agenda-1-ai-integration" .
  
  ?utterance mto:relatedToAgenda ?agendaItem ;
             mto:sequenceNumber ?seqNum ;
             mto:speaker ?speakerNode ;
             mto:timestamp ?timestamp ;
             mto:intent ?intentNode ;
             rdf:value ?text .
  
  ?speakerNode foaf:name ?speaker .
  ?intentNode mto:intentType ?intent .
}
ORDER BY ?seqNum
```

## Query 5: Find All Entity Mentions with Wikidata Links

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX wd: <http://www.wikidata.org/entity/>

SELECT ?entity ?label ?utterance ?speaker ?confidence
WHERE {
  ?mention a mto:EntityMention ;
           mto:entityUri ?entity ;
           mto:entityLabel ?label ;
           mto:mentionedIn ?utterance ;
           mto:confidence ?confidence .
  
  ?utterance mto:speaker ?speakerNode .
  ?speakerNode foaf:name ?speaker .
  
  FILTER(STRSTARTS(STR(?entity), STR(wd:)))
}
ORDER BY DESC(?confidence)
```

## Query 6: Calculate Meeting Participation Statistics

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT ?speaker (COUNT(?utterance) AS ?utteranceCount)
WHERE {
  ?meeting a mto:Meeting ;
           dcterms:identifier "meeting-20260111-product-strategy" ;
           mto:hasUtterance ?utterance .
  
  ?utterance mto:speaker ?speakerNode .
  ?speakerNode foaf:name ?speaker .
}
GROUP BY ?speaker
ORDER BY DESC(?utteranceCount)
```

## Query 7: Find All Tabled or Deferred Decisions

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX dcterms: <http://purl.org/dc/terms/>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT ?decision ?title ?type ?proposer ?proposedAt
WHERE {
  ?decision a mto:Decision ;
            dcterms:title ?title ;
            mto:decisionType ?type ;
            mto:proposedBy ?proposerNode ;
            mto:proposedAt ?proposedAt .
  
  ?proposerNode foaf:name ?proposer .
  
  FILTER(?type IN ("tabled", "deferred"))
}
ORDER BY ?proposedAt
```

## Query 8: Find Action Items and Their Assignees

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX dcterms: <http://purl.org/dc/terms/>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT ?actionTitle ?assignee ?priority ?status ?dueDate
WHERE {
  ?action a mto:ActionItem ;
          dcterms:title ?actionTitle ;
          mto:assignedTo ?assigneeNode ;
          mto:priority ?priority ;
          mto:status ?status .
  
  OPTIONAL { ?action mto:dueDate ?dueDate }
  
  ?assigneeNode foaf:name ?assignee .
}
ORDER BY ?priority DESC(?dueDate)
```

## Query 9: Analyze Sentiment Distribution by Intent Type

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>

SELECT ?intentType ?sentiment (COUNT(*) AS ?count)
WHERE {
  ?utterance a mto:Utterance ;
             mto:intent ?intent ;
             mto:sentiment ?sentiment .
  
  ?intent mto:intentType ?intentType .
}
GROUP BY ?intentType ?sentiment
ORDER BY ?intentType ?sentiment
```

## Query 10: Find All Objections and Their Responses

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT ?objector ?objectionText ?responder ?responseText
WHERE {
  ?objection mto:intent ?objIntent ;
             mto:speaker ?objectorNode ;
             rdf:value ?objectionText .
  
  ?objIntent mto:intentType "objection" .
  
  ?objectorNode foaf:name ?objector .
  
  ?response mto:responseToUtterance ?objection ;
            mto:speaker ?responderNode ;
            rdf:value ?responseText .
  
  ?responderNode foaf:name ?responder .
}
```

## Query 11: RDF-Star Query for Decision Provenance

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX prov: <http://www.w3.org/ns/prov#>
PREFIX dcterms: <http://purl.org/dc/terms/>

SELECT ?decision ?decisionType ?attributedTo ?created ?confidence
WHERE {
  ?decision a mto:Decision .
  
  <<?decision mto:decisionType ?decisionType>>
    prov:wasAttributedTo ?attributedToNode ;
    dcterms:created ?created ;
    mto:confidence ?confidence .
  
  ?attributedToNode foaf:name ?attributedTo .
}
```

## Query 12: Find Meetings by Date Range

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX dcterms: <http://purl.org/dc/terms/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT ?meeting ?title ?startTime ?duration
WHERE {
  ?meeting a mto:Meeting ;
           dcterms:title ?title ;
           mto:actualStartTime ?startTime ;
           mto:duration ?duration .
  
  FILTER(?startTime >= "2026-01-01T00:00:00"^^xsd:dateTime &&
         ?startTime <= "2026-01-31T23:59:59"^^xsd:dateTime)
}
ORDER BY ?startTime
```

## Query 13: Generate Decision Timeline

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX dcterms: <http://purl.org/dc/terms/>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT ?decidedAt ?title ?type ?proposer ?agendaTitle
WHERE {
  ?decision a mto:Decision ;
            dcterms:title ?title ;
            mto:decisionType ?type ;
            mto:decidedAt ?decidedAt ;
            mto:proposedBy ?proposerNode ;
            mto:relatedAgendaItem ?agenda .
  
  ?proposerNode foaf:name ?proposer .
  ?agenda dcterms:title ?agendaTitle .
}
ORDER BY ?decidedAt
```

## Query 14: Calculate Average Meeting Duration

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT (AVG(xsd:dayTimeDuration(?duration)) AS ?avgDuration)
WHERE {
  ?meeting a mto:Meeting ;
           mto:duration ?duration .
}
```

## Query 15: Find Most Active Discussants

```sparql
PREFIX mto: <http://example.org/meeting-transcript/ontology#>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT ?participant (COUNT(DISTINCT ?discussion) AS ?discussionCount)
WHERE {
  ?discussion a mto:Discussion ;
              mto:participants ?participantNode .
  
  ?participantNode foaf:name ?participant .
}
GROUP BY ?participant
ORDER BY DESC(?discussionCount)
```
