## Module 1: Introduction
This module offers a basic explanation of LLM and RAG, and implements a simple RAG pipeline to answer questions about the FAQ documents from the DTC Zoomcamp courses.

This includes: 
* Index Zoomcamp FAQ documents
   * DE Zoomcamp: https://docs.google.com/document/d/19bnYs80DwuUimHM65UV3sylsCn2j1vziPOwzBwQrebw/edit
   * ML Zoomcamp: https://docs.google.com/document/d/1LpPanc33QJJ6BSsyxVg-pWNMplal84TdZtq10naIhD8/edit
   * MLOps Zoomcamp: https://docs.google.com/document/d/12TlBfhIiKtyBv8RnsoJR6F72bkPDGEvPOItJIxaEzE0/edit
* Create a Q&A system for answering questions about these documents

### 1.1 Introduction to LLM and RAG
* LLM
* RAG
* RAG architecture
* Course outcome

### 1.2 Preparing the Environment

### 1.3 Retrieval
* Using the search engine built in the [build-your-own-search-engine workshop](https://github.com/alexeygrigorev/build-your-own-search-engine): [minsearch](https://github.com/alexeygrigorev/minsearch)
* Indexing the documents
* Peforming the search

### 1.4 Generation with OpenAI
* Invoking OpenAI API
* Building the prompt
* Getting the answer

Note: If you can't use a service, an LLM can be run locally. Refer to Module 2 for more details, particularly Module 2.7: "Ollama - Running LLMs on a CPU" - it works with the OpenAI API. To make the example from 1.4 work locally, you just need to change a few lines of code.

### 1.4.2 OpenAI API Alternatives

### 1.5 Cleaned RAG flow

* Cleaning the code 
* Making the code modular

### 1.6 Searching with ElasticSearch
* Run ElasticSearch with Docker
* Index the documents
* Replace MinSearch with ElasticSearch

Running ElasticSearch: Run ElasticSearch with Docker

```bash
docker run -it \
    --name elasticsearch \
    -p 9200:9200 \
    -p 9300:9300 \
    -e "discovery.type=single-node" \
    -e "xpack.security.enabled=false" \
    docker.elastic.co/elasticsearch/elasticsearch:8.4.3
```

Index settings:
```bash
index_settings = {
    "settings": {
        "number_of_shards": 1,
        "number_of_replicas": 0
    },
    "mappings": {
        "properties": {
            "text": {"type": "text"},
            "section": {"type": "text"},
            "question": {"type": "text"},
            "course": {"type": "keyword"} 
        }
    }
}
```

Query:
```bash
{
    "size": 5,
    "query": {
        "bool": {
            "must": {
                "multi_match": {
                    "query": query,
                    "fields": ["question^3", "text", "section"],
                    "type": "best_fields"
                }
            },
            "filter": {
                "term": {
                    "course": "data-engineering-zoomcamp"
                }
            }
        }
    }
}
```