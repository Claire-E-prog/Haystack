# Haystack
## Research Haystack, architecture and integrations for RAG applications
![Screenshot 2025-03-24 at 5 15 52 PM](https://github.com/user-attachments/assets/825f642d-5547-405f-a222-10b9461794ab)
## Overview and Architecture
Open source framework for building LLM applications
### Key Features
- **Scalability**: Efficiently handles large volumes of data.
- **Flexibility**: Supports multiple backends and can be easily integrated into existing systems.
- **Extensibility**: Modular design allows for easy customization and extension.
## Building blocks: 

### Components 
Pre-built (or custom) classes for creating modular or combined (in pipelines) for NLP and LLM tasks
 - document retrieval, text generation, embeddings etc.

**Example components**

| Component      | Description                                                                                                           | Example Use                                                                                          |
|----------------|-----------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Retrievers     | Retrieve relevant documents/text from a vector database (Document Store object in Haystack)                           | [Retrievers API](https://docs.haystack.deepset.ai/reference/retrievers-api)                           |
| Readers        | Takes a query and a set of documents as input and returns extracted answers from a retrieved document                 | [Readers API](https://docs.haystack.deepset.ai/reference/readers-api)                                 |
| Generators     | Generate the an LLM response to a prompt.                                                                             | [Generators API](https://docs.haystack.deepset.ai/reference/generators-api)                           |
| Document Store | Store and manage documents for retrieval and processing by other components.                                          | [Document Store API](https://docs.haystack.deepset.ai/reference/document-store-api)                   |

### Pipelines
Directed graphs of Haystack components and integrations
- preprocessing, indexing, querying etc.
<img src="https://github.com/user-attachments/assets/c9b046a9-3dc1-409f-a0b8-532ff7def1d2" alt="Screenshot 2025-03-25 at 7 46 09 AM" width="400" />

### Document Stores
Pre-built/custom component to facilitate storing snd retrieving Document objects that can be indexed and searched. Supports most vector databases and vector compatible databases (PostgreSQL)
![image](https://github.com/user-attachments/assets/de0a2a72-cef0-4982-9a0d-429c75c097a8)

# Methods for indexing and retrieving job-related data 

### Pipeline Templates:
  ![image](https://github.com/user-attachments/assets/5f0b85e5-1976-4272-934e-66b9c102e05d)
  ![Screenshot 2025-03-26 at 2 36 26 PM](https://github.com/user-attachments/assets/5aad4dc6-94cf-4b24-b989-a6fbb3783f21)
  ![Screenshot 2025-03-26 at 2 37 17 PM](https://github.com/user-attachments/assets/ad4536ea-0b24-4a58-aabb-43d14d8a6bac)

### Build Pipeline (No template):
**Dependencies**
```python
from haystack import Document, Pipeline
from haystack.document_stores.in_memory import InMemoryDocumentStore
from haystack.components.embedders import SentenceTransformersTextEmbedder
from haystack.components.retrievers.in_memory import InMemoryEmbeddingRetriever
```
**Components**
```python
document_store = InMemoryDocumentStore(embedding_similarity_function="cosine")
text_embedder = SentenceTransformersTextEmbedder()
retriever = InMemoryEmbeddingRetriever(document_store=document_store)
```
**Pipeline**
```python
query_pipeline = Pipeline()
```
**Add components**
```python
query_pipeline.add_component("component_name", component_type)

# Here is an example of how you'd add the components initialized in step 2 above:
query_pipeline.add_component("text_embedder", text_embedder)
query_pipeline.add_component("retriever", retriever)
```
**Connect components**
```python
# Syntax
# This is the syntax to connect components. Here you're connecting output1 of component1 to input1 of component2:
pipeline.connect("component1.output1", "component2.input1")

# If both components have only one output and input, you can just pass their names:
pipeline.connect("component1", "component2")

# If one of the components has only one output but the other has multiple inputs,
# you can pass just the name of the component with a single output, but for the component with
# multiple inputs, you must specify which input you want to connect

# Here, component1 has only one output, but component2 has mulitiple inputs:
pipeline.connect("component1", "component2.input1")

# And here's how it should look like for the semantic document search pipeline we're using as an example:
pipeline.connect("text_embedder.embedding", "retriever.query_embedding")
# Because the InMemoryEmbeddingRetriever only has one input, this is also correct:
pipeline.connect("text_embedder.embedding", "retriever")
# Example:
# Imagine this pipeline has four components: text_embedder, retriever, prompt_builder and llm.
# Here's how you would connect them into a pipeline:

query_pipeline.connect("text_embedder.embedding", "retriever")
query_pipeline.connect("retriever","prompt_builder.documents")
query_pipeline.connect("prompt_builder", "llm")
```
# Handling embeddings and vector search in PostgreSQL

**pgvector and Pgvector-haystack**

Using PostgreSQL with Pgvector extension enables vector search in postgreSQL. Haystack supports Pgvector for PostgreSQL integration via [Pgvector-haystack](https://docs.haystack.deepset.ai/reference/integrations-pgvector) and Document Store

**Supported Retrievers:**
[PgvectorEmbeddingRetriever](https://docs.haystack.deepset.ai/docs/pgvectorembeddingretriever)
[PgvectorKeywordRetriever](https://docs.haystack.deepset.ai/docs/pgvectorembeddingretriever)

## Setting up a document store and retriever with pgvector-haystack

```python
from haystack_integrations.document_stores.pgvector import PgvectorDocumentStore
from haystack import Document

document_store = PgvectorDocumentStore(
    embedding_dimension=768,
    vector_function="cosine_similarity",
    recreate_table=True, # overwrites existing table
    search_strategy="hnsw", # approximate nearest neighbor search strategy
)

document_store.write_documents([
    Document(content="This is first", embedding=[0.1]*768),
    Document(content="This is second", embedding=[0.3]*768)
    ])
print(document_store.count_documents())
```
# How to use generative models effectively with job data

Depends on what questions we want the chatbot to answer…

## (Assuming) Trend Related Questions

**User**: *"What new AI roles have emerged in the past year?"*  
**Chatbot**: *"Emerging AI roles are 'AI Prompt Engineer', 'LLM Developer', and 'AI Ethics Specialist' with demand for AI Prompt Engineers increasing 150% from previous years."*

## Traditional RAG (Retrieve and Generate)

1. Generate document/s summarizing insights derived from job posting data analysis (monthly).
2. Store summaries in a vector store.
3. Build an index/retrieval pipeline using summaries.
4. Chatbot uses retrieved documents to answer job market questions.

**Example**
[Extractive QA Pipeline] (https://colab.research.google.com/github/deepset-ai/haystack-tutorials/blob/main/tutorials/34_Extractive_QA_Pipeline.ipynb)

## Text to SQL Chatbot

1. Create a custom SQL query component that generates SQL queries based on natural language user input.
2. Query tabular jobs data (skills, titles, month).
3. Chatbot summarizes query results.

**Example**
[SQL Chat](https://colab.research.google.com/github/deepset-ai/haystack-cookbook/blob/main/notebooks/chat_with_SQL_3_ways.ipynb#scrollTo=GGLuOzn9IPqd)

# Compatibility of Haystack with airflow/How haystack can be used to automate retrieval and parsing workflows
# Differences between Haystack and LangChain/Performance considerations and ease of use compared to LangChain/Advantages and Limitations of Haystack for this project
# Job Data
1. **Best practices for normalizing skills and job titles**
2. **Techniques for optimizing search relevance in job postings**
  





