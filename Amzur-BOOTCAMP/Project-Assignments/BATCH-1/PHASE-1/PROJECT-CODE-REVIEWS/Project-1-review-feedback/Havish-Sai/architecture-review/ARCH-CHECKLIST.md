````markdown
# Architecture Improvement Checklist

**Project:** `chatllm-milestone-3`
**Candidate:** Havish-Sai

---

This checklist summarizes the critical, recommended, and best-practice architectural improvements identified during the review.

### ✅ **Phase 1: Critical Security & Data Integrity Fixes**
*These issues must be addressed before the application can be considered for any type of deployment, including development/staging.*

-   [ ] **Sanitize `session_id` Input:** In `HistoryService`, validate the `session_id` to prevent path traversal attacks. Ensure it only contains a safe set of characters (e.g., alphanumeric, `-`, `_`).
-   [ ] **Encrypt Data at Rest:** In `HistoryService`, encrypt the content of chat messages before they are written to the JSON file. Use a strong encryption library like `cryptography.fernet`.
-   [ ] **Add API Error Handling:** In `LLMService`, wrap the `llm.ainvoke()` call in a `try...except` block to catch exceptions from the LLM provider APIs (e.g., `RateLimitError`, `AuthenticationError`) and return appropriate HTTP error codes.

### ✅ **Phase 2: Core Architectural Refactoring**
*These changes address fundamental architectural flaws and will significantly improve the scalability, reliability, and maintainability of the system.*

-   [ ] **Replace File-Based Persistence:**
    -   [ ] Choose a database solution (e.g., Redis for ephemeral, MongoDB for persistent).
    -   [ ] Create a new `DatabaseService` (or similar) to abstract all database interactions.
    -   [ ] Replace all calls to `HistoryService` with the new database service.
    -   [ ] Remove the old `HistoryService` and the `history/` directory.
-   [ ] **Implement a Caching Layer:**
    -   [ ] Set up a Redis instance.
    -   [ ] In the application's startup logic, configure LangChain's global LLM cache to use `RedisCache`.
-   [ ] **Adopt FastAPI Dependency Injection:**
    -   [ ] Create a `dependencies.py` module.
    -   [ ] Define provider functions (`get_settings`, `get_llm_service`, etc.).
    -   [ ] Remove global service instantiations from `main.py`.
    -   [ ] Use `Depends` in the API route functions to inject services.

### ✅ **Phase 3: Testing and Best Practices**
*These improvements will increase confidence in the application's correctness and align it with industry best practices.*

-   [ ] **Write API Integration Tests:**
    -   [ ] Create a `tests/api/` directory.
    -   [ ] Use FastAPI's `TestClient` to write tests for the `/api/v1/chat` endpoint.
    -   [ ] Use `app.dependency_overrides` to mock services and test endpoint logic in isolation.
    -   [ ] Add tests for success cases, error cases (e.g., invalid provider), and security checks.
-   [ ] **Refactor Tests to Use Pytest Fixtures:**
    -   [ ] Create a `tests/conftest.py` file.
    -   [ ] Define fixtures for creating test-specific `Settings`, mock services, and other shared resources.
    -   [ ] Update existing unit tests to use these fixtures.
-   [ ] **Abstract Frontend API Client:**
    -   [ ] In the frontend, create an `api_client.py`.
    -   [ ] Move the `requests.post` logic into a dedicated function or class in this new file.
    -   [ ] Call the abstracted function from `app.py`.
-   [ ] **Configure History Path:** Add the `history` directory path to the `Settings` model and use it in the `HistoryService` (if it is being kept temporarily).

---
**Reviewer's Note:** The candidate has built a strong architectural foundation. Completing Phase 1 is non-negotiable for security reasons. Completing Phase 2 will transform the application from a "proof-of-concept" to a "production-ready" architecture. Phase 3 represents professional polish and is essential for long-term maintainability.
````
