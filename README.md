# Component catalog

A concise description of each service in the RAG system.

---

## 1. Client / API Gateway

Entry point for all user requests. Handles authentication, rate limiting, and routing to the RAG Orchestrator Service.

---

## 2. RAG Orchestrator Service

Coordinates the entire retrieval-augmented generation workflow.

Responsibilities include:

- Receiving user queries  
- Calling the Vector Search Service  
- Fetching documents from the Document Store  
- Invoking the external LLM API  
- Assembling the final response  

This service sits on the critical path for every request.

---

## 3. Vector Search Service (single instance)

Executes vector similarity search using embeddings.

Key responsibilities:

- Receiving embedding queries from the Orchestrator  
- Querying the Vector Index DB  
- Returning top-K similar vectors  

Deployment note: single instance → limited throughput and potential latency hotspot.

---

## 4. Vector Index DB (single node, no replicas)

Stores vector embeddings and supports similarity search operations.

Characteristics:

- Single-node deployment  
- No replication or failover  
- High CPU usage under load  

Risk: this is a single point of failure and a major contributor to retrieval latency.

---

## 5. Document Store (single primary node)

Stores raw documents and metadata used for grounding LLM responses.

The Orchestrator fetches documents from this store after receiving vector search results.

Deployment note: single primary node → synchronous reads and limited throughput.

---

## 6. Redis Cache (single instance, no failover)

Caches frequent queries to reduce load on the Vector Search Service and Document Store.

Deployment note: single instance → no redundancy and potential failure point.

---

## 7. LLM API (external managed service)

External large language model endpoint used to generate final responses.

The Orchestrator sends retrieved context plus the user query to this service.

Latency is generally stable but depends on external provider performance.
