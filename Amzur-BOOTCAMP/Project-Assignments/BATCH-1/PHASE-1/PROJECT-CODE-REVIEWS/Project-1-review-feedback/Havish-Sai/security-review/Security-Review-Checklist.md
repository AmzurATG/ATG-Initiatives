````markdown
# Security Review Checklist & Remediation Plan

**Project:** `chatllm-milestone-3`
**Candidate:** Havish-Sai
**Review Date:** August 21, 2025

---

## Phase 1: Critical Security Fixes (Immediate Priority)

This phase addresses vulnerabilities that pose an immediate and severe risk to the application's integrity and safety.

-   [ ] **Implement Prompt Injection Defense:**
    -   **Vulnerability:** Critical (10/10) - The application is fully open to prompt injection.
    -   **Task:** Create a security utility function that validates and sanitizes all user-provided prompts before they are sent to the LLM service.
    -   **Recommendation:** The function should check for malicious substrings (e.g., "ignore instructions"), enforce length limits, and potentially use a simpler model as a guardrail.
    -   **File(s) to Modify:** `api/v1/chat.py`, create `utils/security.py`.
    -   **Status:** `NOT STARTED`

-   [ ] **Implement User Authentication:**
    -   **Vulnerability:** Critical (10/10) - The API is public and unauthenticated.
    -   **Task:** Integrate a standard authentication system.
    -   **Recommendation:** Use FastAPI's `OAuth2PasswordBearer` and `passlib` to create user registration and login endpoints that issue JWTs. Protect the `/chat` endpoint, requiring a valid JWT for access.
    -   **File(s) to Modify:** `main.py`, `api/v1/chat.py`, create `services/user_service.py`, `models/user.py`.
    -   **Status:** `NOT STARTED`

-   [ ] **Encrypt Chat History at Rest:**
    -   **Vulnerability:** High (8/10) - Chat logs are stored in plain text.
    -   **Task:** Encrypt the content of history files.
    -   **Recommendation:** Use the `cryptography` library to encrypt the JSON data before writing it to the file system and decrypt it upon reading. Store the encryption key as a secure environment variable.
    -   **File(s) to Modify:** `services/history_service.py`, `config.py`.
    -   **Status:** `NOT STARTED`

---

## Phase 2: Hardening and Best Practices (Medium Priority)

This phase focuses on strengthening the application against abuse and implementing defense-in-depth.

-   [ ] **Implement Global, IP-Based Rate Limiting:**
    -   **Vulnerability:** High (7/10) - The current rate limiter is per-session and insufficient.
    -   **Task:** Replace the existing rate limiter with a more robust, global solution.
    -   **Recommendation:** Use a library like `slowapi` to implement a global rate limit based on the client's IP address. For scalability, this should use a Redis backend.
    -   **File(s) to Modify:** `main.py`, `api/v1/chat.py`, `utils/rate_limiter.py`.
    -   **Status:** `NOT STARTED`

-   [ ] **Add Security Headers (CSP, etc.):**
    -   **Vulnerability:** Medium (6/10) - Missing key security headers.
    -   **Task:** Implement middleware to add security headers to all responses.
    -   **Recommendation:** Add a strong Content Security Policy (CSP), `X-Content-Type-Options: nosniff`, and `Strict-Transport-Security`.
    -   **File(s) to Modify:** `main.py`.
    -   **Status:** `NOT STARTED`

-   [ ] **Sanitize HTML Output on Frontend:**
    -   **Vulnerability:** Medium (6/10) - Potential for XSS from LLM responses.
    -   **Task:** Explicitly sanitize all content received from the backend before rendering it.
    -   **Recommendation:** In the Streamlit app, use the `bleach` library to clean the LLM response, allowing only a safe list of HTML tags if formatting is desired.
    -   **File(s) to Modify:** `app.py`.
    -   **Status:** `NOT STARTED`

---

## Phase 3: Production Readiness (Long-Term Improvement)

This phase addresses security concerns related to deployment and long-term maintenance.

-   [ ] **Create a Secure Production Deployment Guide:**
    -   **Vulnerability:** High (7/10) - Lack of production guidance.
    -   **Task:** Create a `DEPLOYMENT.md` file.
    -   **Recommendation:** The guide must include instructions for using a production server like Gunicorn, setting up a reverse proxy like Nginx, and enabling HTTPS with TLS/SSL certificates.
    -   **File(s) to Modify:** Create `DEPLOYMENT.md`.
    -   **Status:** `NOT STARTED`

-   [ ] **Pin Dependencies and Enable Vulnerability Scanning:**
    -   **Vulnerability:** Medium (5/10) - Unpinned dependencies.
    -   **Task:** Create a lock file and set up automated scanning.
    -   **Recommendation:** Generate a `requirements.lock.txt` using `pip freeze`. Enable Dependabot on the GitHub repository to get automatic alerts for vulnerable dependencies.
    -   **File(s) to Modify:** `requirements.txt`, create `requirements.lock.txt`.
    -   **Status:** `NOT STARTED`

-   [ ] **Adopt a Production Secrets Management Strategy:**
    -   **Vulnerability:** Medium (6/10) - Use of `.env` file for secrets.
    -   **Task:** Plan for a more secure way to handle secrets in production.
    -   **Recommendation:** Refactor the application to fetch secrets from a service like AWS Secrets Manager, Azure Key Vault, or HashiCorp Vault at startup.
    -   **File(s) to Modify:** `config.py`.
    -   **Status:** `NOT STARTED`
````
