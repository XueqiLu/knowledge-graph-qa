# Knowledge Graph–Based Question Answering System (Neo4j)

This project explores the construction of a **knowledge-graph-based question answering (KGQA) system** using **Neo4j**.  
The goal is to transform structured medical data into a graph representation and support question answering through
entity recognition, intent classification, and graph queries.

The project is organized into two main stages:

1. **Knowledge graph construction** – ingest structured data into Neo4j with a well-defined schema.
2. **Question answering** – interpret natural-language queries and retrieve answers from the graph.

Each stage is implemented in a separate Jupyter notebook to ensure clarity, modularity, and reproducibility.


---

## Knowledge Graph Construction (Neo4j)

The notebook `import_data.ipynb` implements the data ingestion pipeline that constructs the medical knowledge graph in Neo4j.

### Workflow overview

1. **Data preprocessing**
   - Remove unused columns from the raw dataset.
   - Normalize textual fields to remove presentation artifacts.
   - Split multi-valued cells using common Chinese delimiters.
   - Deduplicate entity names and relationship pairs in memory.

2. **Node creation**
   - Disease nodes are created using `MERGE` with full attributes.
   - Other entity nodes are created as name-only nodes and merged by `(label, name)` to ensure uniqueness.
   - Complications are modeled as **Disease → Disease** relationships.

3. **Relationship creation**
   - Relationships are added using `MERGE` to guarantee idempotency.
   - Endpoint nodes are matched or merged by `(label, name)` before linking.
   - Re-running the notebook does not create duplicate nodes or relationships.


---

## Question Answering over the Knowledge Graph

The second stage of the project focuses on **question answering over the constructed graph** and is implemented in
`question_answering.ipynb`.

Given a Chinese natural-language question, the system:
1) extracts medical-related keywords using Jieba with custom vocabularies;
2) maps keywords to entity labels and candidate diseases in a Neo4j knowledge graph;
3) identifies the user’s intent (e.g. symptom, department, treatment);
4) queries the graph and returns a template-based natural-language answer.

Ambiguous questions (multiple diseases) and non-medical questions are explicitly detected
and handled with clarification or out-of-scope responses, instead of forcing an answer.

Intent Classification

Two intent classification approaches were explored during development:
- a zero-shot intent classifier based on a pre-trained language model;
- a supervised Chinese BERT intent classifier trained on a small synthetic dataset.

The supervised model was trained using automatically generated question variants (approximately 15 questions per intent) to improve in-domain accuracy.
Due to computational constraints, only the inference pipeline is included in this repository.

System validation (qualitative)

The system was manually tested on a small set of representative medical queries to verify:
- correct entity recognition
- correct intent-to-query mapping
- correct Cypher execution on the Neo4j graph

While no large-scale quantitative benchmark is reported, these tests confirm that the end-to-end pipeline functions as designed.



