# Example Annotated Deliverable

---

## Paragraph 1 — Primary Performance Bottleneck
The primary performance bottleneck in this RAG system is the **Vector Search Service**, which consistently shows the highest p95 latency across the metrics dataset (162–172 ms) and elevated CPU usage (78–81%). This aligns with the architecture diagram, where the Vector Search Service sits directly on the retrieval path and must query the Vector Index DB synchronously for every request. Because the service is deployed as a **single instance**, it cannot horizontally scale to absorb increased query volume, causing latency to propagate upstream to the RAG Orchestrator. The Orchestrator’s own elevated latency (210–225 ms) reflects this dependency, confirming that retrieval delays in vector search directly impact end‑to‑end response time.

**Annotation:**  
- Identifies the bottleneck using metrics  
- Connects latency to architecture  
- Explains dependency chain  
- Uses evidence from both artifacts  

---

## Paragraph 2 — Single Point of Failure
The system’s most critical single point of failure is the **Vector Index DB**, which is deployed as a **single node with no replicas**, as shown in the architecture diagram. All vector similarity queries depend on this database, and the metrics indicate sustained high CPU utilization (92–94%), suggesting limited headroom before saturation. If this node becomes unavailable, the Vector Search Service cannot perform retrieval, causing the entire RAG pipeline to fail. While other components such as the Redis Cache and Document Store also operate in single‑instance configurations, the Vector Index DB is uniquely positioned on the critical retrieval path, making it the most impactful failure point in the system.

**Annotation:**  
- Identifies the SPOF  
- Uses deployment notes from the diagram  
- References CPU pressure as supporting evidence  
- Explains system‑wide impact  
