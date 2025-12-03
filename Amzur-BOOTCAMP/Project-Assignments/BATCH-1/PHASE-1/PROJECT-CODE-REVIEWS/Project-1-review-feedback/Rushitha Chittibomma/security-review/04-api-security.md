**Candidate:** Rushitha Chittibomma  
**Reviewer:** AI Code Reviewer  
**Date:** August 22, 2025  
**Project:** Chatbot Milestone 2  

# API Security Review for Rushitha Chittibomma

**Overall Score: POOR (3/10)**

This review assesses the security of the application's use of APIs. The primary focus is on the interaction with the external Google Gemini API, as the application itself does not expose a REST API. The main weaknesses are in secret management and the lack of protection against resource abuse.

---

### 1. Endpoint Security Analysis
- **Assessment**: **NOT APPLICABLE**. The application is a Streamlit web app and does not define its own REST API endpoints (e.g., with FastAPI or Flask).

---

### 2. Rate Limiting & DoS Protection
- **Assessment**: **CRITICAL FLAW**.
- **Vulnerability**: There is no rate-limiting implemented anywhere in the application. A malicious or buggy client could send a very large number of requests in a short period.
- **Impact**:
    - **Denial of Service (DoS)**: A simple script could repeatedly send prompts, exhausting the Google Gemini API's rate limits for the given API key. This would make the service unavailable for all other legitimate users.
    - **Financial Cost**: Since API calls cost money, a DoS attack would directly translate to a high bill. An attacker could intentionally drive up costs.
- **Risk Rating**: **CRITICAL (9/10)**. Lack of rate limiting is a major vulnerability for any application that consumes a paid external API.

- **Recommendation**:
    - Implement rate limiting on the server side. A simple way to do this in a Streamlit application is to track request timestamps in the user's session state.
    - **Example Implementation (in `app.py`)**:
        ```python
        import time

        # At the start of the app
        if 'last_request_time' not in st.session_state:
            st.session_state.last_request_time = 0

        # When processing a prompt
        if prompt := st.chat_input("..."):
            current_time = time.time()
            # Limit to 1 request every 3 seconds
            if current_time - st.session_state.last_request_time < 3:
                st.error("You are sending messages too quickly. Please wait a moment.")
                return

            st.session_state.last_request_time = current_time
            # ... proceed with sending prompt to LLM ...
        ```
    - For a more robust, global solution (not just per-user), a tool like a reverse proxy (e.g., Nginx) or a dedicated rate-limiting library would be needed.

---

### 3. API Authentication Security
- **Assessment**: **MAJOR WEAKNESS**.
- **Vulnerability**: The Google Gemini API key is retrieved from an environment variable (`os.getenv("GEMINI_API_KEY")`) in `config.py`.
- **Impact**:
    - **Exposure in Development**: Developers often store environment variables in `.env` files or shell history, which can be accidentally committed to version control.
    - **Insecure for Production**: In a production environment, storing secrets in environment variables is not considered a best practice. If an attacker gains shell access to the server, they can often easily read the environment variables of the running process.
- **Risk Rating**: **HIGH (7/10)**. While better than hardcoding, this method of secret management is not sufficiently secure for production.

- **Recommendation**:
    - **Use a Secrets Management Service**: For any real deployment, the API key should be stored in a dedicated secrets manager (e.g., HashiCorp Vault, AWS Secrets Manager, Google Secret Manager). The application would be granted an identity (e.g., an IAM role) that gives it permission to retrieve the secret at runtime. This prevents the secret from ever being stored on the server's file system or in an environment variable.

---

### 4. Error Handling & Information Disclosure
- **Assessment**: **MAJOR WEAKNESS**.
- **Vulnerability**: As noted in other reports, when the API call in `llm_service.py` fails, the raw exception from the `google-generativeai` library is displayed to the user.
- **Impact**: This can leak specific information about why the API call failed (e.g., "Permission Denied," "API Key Invalid," "Billing Not Enabled"). This information is valuable to an attacker, confirming whether an API key is valid or what kind of access it has.
- **Risk Rating**: **HIGH (7/10)**.

- **Recommendation**:
    - **Abstract API Errors**: Catch specific exceptions from the API library and translate them into generic, user-facing error messages. Log the detailed error on the server for debugging. (See `03-data-protection-security.md` for a code example).

---

### Summary & Recommendations

- **Finding 1**: **No Rate Limiting**. The application is vulnerable to denial-of-service attacks and financial abuse by exhausting API quotas.
- **Finding 2**: **Insecure API Key Storage**. The API key is stored in an environment variable, which is not secure enough for production.
- **Finding 3**: **API Error Leakage**. Internal API error details are leaked to the user.

- **Priority Recommendations**:
    1.  **Implement Rate Limiting (Highest Priority)**: Add session-based rate limiting immediately to prevent basic abuse.
    2.  **Improve Error Handling**: Stop leaking specific API error messages to the user interface.
    3.  **Plan for Secure Secrets Management**: While likely out of scope for a bootcamp project, acknowledge that a production version of this app would require a proper secrets management solution.
