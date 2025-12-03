```markdown
# Performance Review: Memory Usage Analysis

**Project:** `llm-chatbot-feature-2`
**Candidate:** Hanuman Guntuku
**Review Date:** August 21, 2025

---

## 1. Memory Usage Analysis

### Finding 1: Unbounded In-Memory Objects
-   **Observation**: The application uses in-memory Python objects to store state that can grow indefinitely.
-   **Analysis**:
    -   **Rate Limiter (`rate_limiter.py`)**: The `requests` dictionary stores a timestamp and count for every unique IP address that has ever made a request. While the timestamp is updated, the entry for an IP address is never removed. Over a long period, this dictionary could grow very large, consuming significant memory.
    -   **Streamlit Session State (`frontend/app.py`)**: The `st.session_state.messages` list stores the entire chat history for a user's session. For a very long conversation, this list could grow to consume a considerable amount of memory for that specific user's session.
    -   **File-Based Storage**: As noted in the database review, loading the entire `chat_history.json` or `user_feedback.json` into memory for processing would be a major memory burden if those files become large.

### Finding 2: No Caching Strategy for Repeated Requests
-   **Observation**: The `LLMService` processes every request by making a fresh API call to the external provider, even if the same prompt is sent multiple times.
-   **Analysis**: While caching LLM responses can be complex (due to the "temperature" or variability of responses), a simple caching mechanism for identical, deterministic prompts could reduce both latency and cost. The lack of any caching means that every single request consumes memory and CPU for processing and network I/O.
-   **Impact**: Higher-than-necessary resource consumption and operational cost.

### Finding 3: Efficient Dependency Usage
-   **Observation**: The project uses a minimal and well-chosen set of dependencies (`fastapi`, `pydantic`, `streamlit`, `requests`, etc.).
-   **Analysis**: The developer has not included large, unnecessary libraries. FastAPI and Pydantic, in particular, are known for their low memory footprint compared to other web frameworks. This demonstrates good judgment in selecting lightweight and efficient tools.
-   **Impact**: This is a positive finding that helps keep the baseline memory usage of the application low.

---

## 2. Recommendations

### Recommendation 1: Implement a Time-Bound Eviction Policy for the Rate Limiter
-   **Suggestion**: Modify the rate limiter to periodically clean up old entries from the `requests` dictionary.
-   **Justification**: An IP address that made a request a week ago does not need to be kept in the rate limiter's memory. A simple Time-to-Live (TTL) policy should be implemented.
-   **Example**:
    ```python
    # In rate_limiter.py, a periodic cleanup task could be run
    
    CLEANUP_INTERVAL = 3600 # 1 hour in seconds
    
    def cleanup_old_entries():
        now = time.time()
        expired_ips = [
            ip for ip, (ts, _) in requests.items() 
            if now - ts > CLEANUP_INTERVAL
        ]
        for ip in expired_ips:
            del requests[ip]
    
    # This cleanup would need to be triggered, e.g., in a background thread.
    # A simpler, albeit less precise, method is to clean on-the-fly.
    # When a request comes in, iterate over a small sample of the dict and remove old entries.
    ```
-   **Benefit**: Prevents unbounded memory growth of the rate limiter, ensuring long-term stability.

### Recommendation 2: Introduce a Caching Layer
-   **Suggestion**: Implement a caching mechanism for the `LLMService`.
-   **Justification**: A cache would store the results of expensive operations (like LLM API calls) and return the cached result when the same input occurs again.
-   **Example (using `functools.lru_cache`)**:
    ```python
    # In services/llm_service.py
    from functools import lru_cache
    
    class LLMService:
        @lru_cache(maxsize=128) # Least Recently Used cache
        def get_response(self, user_input: str, provider: LLMProvider):
            # ... existing logic ...
    ```
-   **Benefit**:
    -   **Reduced Latency**: Repeated queries will be near-instantaneous.
    -   **Reduced Cost**: Fewer calls are made to the paid external APIs.
    -   **Lower Memory/CPU**: Less processing is required for cached responses.
    The `lru_cache` is a simple in-memory cache. For a multi-instance deployment, a shared cache like Redis would be necessary.

### Recommendation 3: Paginate or Limit Chat History
-   **Suggestion**: In the frontend, limit the amount of chat history stored and displayed in the session state.
-   **Justification**: Storing an entire, multi-hour conversation in memory is not scalable. A more robust solution would only keep the last N messages in the session and load older messages from persistent storage on demand (e.g., with a "Load more" button).
-   **Benefit**: Keeps the memory usage per user session predictable and low.

---

## 3. Performance Score (Memory)

-   **Score**: **6.5/10**
-   **Rationale**: The application is reasonably memory-efficient for simple use cases due to its lightweight framework choices. However, it has clear vulnerabilities related to unbounded memory growth in the rate limiter and session state. The lack of any caching strategy also represents a missed opportunity for significant performance optimization. The score reflects that while the baseline is good, the design lacks the robustness needed to ensure stable memory performance over time and under load.
```
