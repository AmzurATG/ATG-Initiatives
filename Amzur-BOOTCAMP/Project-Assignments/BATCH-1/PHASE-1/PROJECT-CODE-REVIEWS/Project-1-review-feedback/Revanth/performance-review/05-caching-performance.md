# Caching & Performance Strategy Review

## Project Information
- **Candidate Name:** Revanth
- **Project Title:** LLM Chatbot
- **Review Date:** 2023-10-27
- **Reviewer:** GitHub Copilot

---

### Cache Strategy Implementation
- **Missing**: The application has no caching layer. Every time a question is asked, even if it's identical to a previous one, a new request is sent to the OpenAI API.

### Performance Impact
- **High Latency**: This lack of caching means there are no performance benefits for repeated questions.
- **Increased Cost**: Every request incurs a cost from the OpenAI API. Caching is a primary strategy for reducing these costs.

### Cache Strategy Recommendations
- **Backend Caching**: A simple caching mechanism should be implemented on the backend.
    - **Level 1 (Simple)**: Use a Python dictionary as an in-memory cache. This is easy to implement but is not shared between server processes and is lost on restart.
    - **Level 2 (Robust)**: Use an external cache like Redis. This provides a shared cache that persists across restarts and can be scaled independently.
- **Cache Key Design**: The user's question string can serve as the cache key.
- **Cache Expiration**: An expiration time (TTL - Time To Live) should be set for cache entries to ensure that answers can be refreshed eventually.

**Caching Performance Scoring:**
- **POOR (2/10)**: The complete absence of a caching strategy is a major missed optimization opportunity for both performance and cost-efficiency.
