# Haystack
## Research Haystack, architecture and integrations for RAG applications
![Screenshot 2025-03-24 at 5 15 52 PM](https://github.com/user-attachments/assets/825f642d-5547-405f-a222-10b9461794ab)
## Overview and Architecture
Open source framework for building LLM applications
### Key Features
- **Scalability**: Efficiently handles large volumes of data.
- **Flexibility**: Supports multiple backends and can be easily integrated into existing systems.
- **Extensibility**: Modular design allows for easy customization and extension.

## Building blocks: Components -> Pipelines
Components - pre-built or custom classes for NLP and LLM Application tasks
 - document retrieval, text generation, embeddings etc
### Pipelines
Pipelines define the flow of data through a series and/or branched components to perform specific tasks.
- **Extractive Question Answering**: Combines a retriever and a reader to find answers in documents.
- **Document Search**: Uses a retriever to search for relevant documents.
- **Generative Question Answering**: Uses a generative model to create answers from documents.

### Common components for RAG applications
| Component  | Description                                                                                                                                  | Example Use                                                                                          |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Retrievers | Retrieve relevant documents/text from a vector database (Document Store object in Haystack)                                                  | [Retrievers API](https://docs.haystack.deepset.ai/reference/retrievers-api)                           |
| Readers    | Takes a query and a set of documents as input and returns Extracted Answers by selecting a span of text within a document                    | [Readers API](https://docs.haystack.deepset.ai/reference/readers-api)                                 |
| Generators | Generate the text LLM response to a prompt.                                                                                                   | [Generators API](https://docs.haystack.deepset.ai/reference/generators-api)                           |
### Document Store object

### Retrievers

Retrievers are responsible for fetching relevant documents from the Document Store based on a query. Types of retrievers include:
- **Sparse Retrievers**: Use traditional keyword-based methods (e.g., BM25).
- **Dense Retrievers**: Use embeddings to find semantically similar documents (e.g., EmbeddingRetriever).

### Readers

Readers extract answers from the documents retrieved by the retriever. They are typically used in question answering pipelines and can be based on models like BERT.

### Pipelines

Pipelines define the flow of data through various components to perform specific tasks. Common types of pipelines include:
- **Extractive Question Answering**: Combines a retriever and a reader to find answers in documents.
- **Document Search**: Uses a retriever to search for relevant documents.
- **Generative Question Answering**: Uses a generative model to create answers from documents.

## Example Pipelines

### Extractive QA Pipeline

1. **Retriever**: Fetches relevant documents from the Document Store.
2. **Reader**: Extracts precise answers from the retrieved documents.

### Document Search Pipeline

1. **Retriever**: Searches for documents based on the query.

### Custom Pipelines

Haystack allows the creation of custom pipelines by combining various components to fit specific use cases.
