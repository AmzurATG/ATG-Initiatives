
# Scalability & Load Performance Review

**Project:** Smart Knowledge Repository
**Name:** Revanth Yalamanchili
**Report Date:** September 9, 2025

**Prepared by:** GitHub Copilot, Scalability Architect & Load Testing Expert

## 1. Executive Summary

This report evaluates the scalability and load handling capabilities of the "Smart Knowledge Repository" application. The current architecture, while functional for single-user, low-traffic scenarios, presents significant challenges for scaling. The application's stateful components, reliance on a single-file database (SQLite), and local file-based vector index (FAISS) are major architectural bottlenecks that prevent both horizontal and vertical scaling.

The overall scalability readiness is **CRITICAL**, as the application in its current state cannot handle even a 2x load increase without significant performance degradation and potential data consistency issues.

| Category | Score | Priority |
| --- | --- | --- |
| **Horizontal Scaling Assessment** | 2/10 | **CRITICAL** |
| **Vertical Scaling Analysis** | 3/10 | **HIGH** |
| **Load Testing & Stress Analysis** | N/A | **CRITICAL** |
| **Database Scaling Strategy** | 1/10 | **CRITICAL** |
| **Auto-scaling & Elasticity** | 1/10 | **CRITICAL** |

---

## 2. Scalability Performance Framework

### 2.1. Horizontal Scaling Assessment

**Score: 2/10 - CRITICAL**

Horizontal scaling is fundamentally limited by the current architecture.

- **Stateless Application Design:** The application is **stateful**. Streamlit's session state, the local SQLite database, and the FAISS index stored as a local file (`profiles.faiss`) all tie a user session to a single server instance. Multiple instances would have inconsistent and isolated data.
- **Session Management:** Streamlit's session management is not designed for multi-server deployments without a shared backend for session state.
- **Load Balancer Readiness:** A load balancer could be placed in front of the app, but it would lead to inconsistent user experiences as requests for the same user could be routed to different instances with different states.
- **Database Connection Scaling:** SQLite is a serverless, file-based database. It does not support concurrent write access from multiple processes effectively, making it unsuitable for a scaled application.

### 2.2. Vertical Scaling Analysis

**Score: 3/10 - HIGH**

While vertical scaling (increasing CPU/memory) is technically possible, it offers limited benefits and hits a hard ceiling quickly.

- **Resource Utilization Efficiency:** The application is primarily single-threaded due to Python's GIL and the nature of Streamlit's execution model. It cannot effectively utilize multiple CPU cores on a larger server.
- **CPU and Memory Scaling Limits:**
    - **CPU:** Scaling is limited. CPU-bound tasks like embedding generation will run faster on a more powerful single core, but overall throughput won't increase significantly.
    - **Memory:** The entire FAISS index is loaded into memory. As the number of profiles grows, memory consumption will increase linearly, creating a hard limit for vertical scaling. A 1 million vector index could easily consume several gigabytes of RAM.
- **Resource Bottleneck Identification:** The primary bottlenecks are the single-threaded nature of Python/Streamlit, the file I/O for SQLite, and the memory required for the FAISS index.

### 2.3. Load Testing & Stress Analysis

**Score: N/A - CRITICAL**

No load testing framework is implemented. Based on the architectural analysis, the application would fail under even minimal load.

- **Performance Under Increased Load:** It is projected that response times would increase dramatically with just a few concurrent users, especially during scraping or chat interactions.
- **Breaking Point Identification:** The breaking point is likely very low (2-5 concurrent users), where file locks on the SQLite database and high CPU usage for embedding calculations would cause requests to time out.
- **Resource Exhaustion Scenarios:**
    1. **Memory:** The FAISS index size will exhaust available RAM.
    2. **CPU:** Concurrent chat requests will lead to CPU contention as multiple processes try to perform vector searches and LLM calls.
    3. **Disk I/O:** Concurrent writes/reads to the SQLite database will create a major bottleneck.

### 2.4. Database Scaling Strategy

**Score: 1/10 - CRITICAL**

The current database, SQLite, has no viable scaling strategy.

- **Read Replica/Sharding:** Not supported by SQLite.
- **Connection Pooling:** Not applicable. SQLite connections are lightweight file handles, but concurrent writes are the core issue.
- **Query Performance Under Load:** Performance will degrade rapidly due to file locking. Write operations will block all other read/write operations, effectively serializing all database access.

### 2.5. Auto-scaling & Elasticity

**Score: 1/10 - CRITICAL**

Auto-scaling is not feasible with the current architecture.

- **Cloud Auto-scaling Configuration:** The stateful nature of the application makes it incompatible with standard auto-scaling policies. A new instance would be "blank," having no access to the existing SQLite database or FAISS index.
- **Scaling Trigger Optimization:** Even if auto-scaling were configured (e.g., based on CPU), it would not solve the underlying state management problem.

---

## 3. Scalability Implementation Recommendations

The application requires a fundamental architectural redesign to become scalable.

### Priority 1: Decouple State from the Application (CRITICAL)

1.  **Replace SQLite with a Production Database:**
    - **Recommendation:** Migrate from SQLite to **PostgreSQL** or **MySQL**. These are robust, scalable, and can be managed as separate services.
    - **Action:**
        - Update `src/database/repository.py` to use `SQLAlchemy` with a PostgreSQL driver (`psycopg2-binary`).
        - Externalize database connection settings (e.g., via environment variables).

2.  **Externalize the Vector Index:**
    - **Recommendation:** Replace the local FAISS index with a managed vector database service like **Pinecone**, **Weaviate**, or use a database with vector capabilities like **PostgreSQL with pgvector**.
    - **Action:**
        - Refactor `create_index.py` and `src/search/vector_search.py` to interact with the chosen vector database service via its API. This removes the need to load a large index file into memory.

### Priority 2: Achieve Statelessness (HIGH)

1.  **Refactor Application to be Stateless:**
    - **Recommendation:** Ensure that no user or session data is stored on the local filesystem or in the memory of a single application instance. With an external database and vector store, the application becomes inherently more stateless.
    - **Action:** Ensure all data persistence happens through the new database and vector search services.

### Priority 3: Implement a Scalable Deployment Strategy (MEDIUM)

1.  **Containerize the Application:**
    - **Recommendation:** Use Docker to containerize the Streamlit application. This makes deployments consistent and manageable.
2.  **Deploy with a Container Orchestrator:**
    - **Recommendation:** Use a service like **AWS ECS**, **Google Cloud Run**, or **Kubernetes** to manage and auto-scale the containerized application.
    - **Action:** Configure auto-scaling rules based on CPU/memory usage or request count. With a stateless architecture, new instances can be spun up or down without data loss or inconsistency.

### Priority 4: Introduce Load Testing (MEDIUM)

1.  **Implement a Load Testing Framework:**
    - **Recommendation:** Use tools like **Locust** or **JMeter** to simulate concurrent users and measure performance under load.
    - **Action:**
        - Create test scripts that simulate key user journeys (chatting, searching).
        - Run tests against the newly architected application to find performance bottlenecks and validate scaling.

## 4. Conclusion

The "Smart Knowledge Repository" is currently a prototype and is not scalable. The architectural choices of SQLite and a local FAISS index are significant blockers. To prepare the application for any level of multi-user load, a foundational shift to a stateless architecture with managed, external services for the database and vector index is **essential and critical**. Without these changes, the application cannot be scaled.
