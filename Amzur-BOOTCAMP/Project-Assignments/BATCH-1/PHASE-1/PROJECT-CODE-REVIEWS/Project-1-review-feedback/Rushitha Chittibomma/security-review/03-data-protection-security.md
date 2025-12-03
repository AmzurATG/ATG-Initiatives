**Candidate:** Rushitha Chittibomma  
**Reviewer:** AI Code Reviewer  
**Date:** August 22, 2025  
**Project:** Chatbot Milestone 2  

# Data Protection & Privacy Security Review for Rushitha Chittibomma

**Overall Score: POOR (3/10)**

This review assesses how the application protects data, both in transit and at rest. While data in transit is handled securely by default, the application has **major weaknesses** in how it stores data at rest, exposing user conversations and potentially sensitive information.

---

### 1. Sensitive Data Identification
- **Personal Identifiable Information (PII)**: The application does not explicitly ask for PII, but users could easily volunteer it in the course of a conversation (e.g., "My name is John Doe and my email is john.doe@example.com"). The application has no mechanism to identify or protect this PII.
- **Authentication Credentials**: Not applicable, as there is no authentication.
- **User Behavioral Data**: The entire chat history is a record of user behavior. This data is being stored.

---

### 2. Data at Rest Security
- **Assessment**: **CRITICAL FLAW**.
- **Vulnerability**: The `ChatManager` saves the entire conversation history to a plain text JSON file (`chat_history.json`).
- **Impact**: Anyone with file system access to the server where the application is running can read all conversations from all users. This is a major privacy violation. The data is not encrypted, and there are no access controls beyond basic file permissions. If this server were compromised, all chat data would be stolen.
- **Risk Rating**: **CRITICAL (9/10)**. Storing user data in plain text is a fundamental security failure.

- **Recommendation**:
    1.  **Stop Storing Chats by Default**: For a simple demo, the easiest and most secure option is to not save chats to disk at all. The history can be ephemeral and exist only in the Streamlit session.
    2.  **Use a Secure Database**: If persistence is required, the correct solution is to use a database (even a simple one like SQLite) where access can be properly controlled.
    3.  **Encrypt Sensitive Data**: If file-based storage must be used, the file should be encrypted. The application would need to manage an encryption key (securely, using a secrets manager) to encrypt the data before writing it to disk and decrypt it after reading.

---

### 3. Data in Transit Security
- **Assessment**: **SATISFACTORY**.
- **Details**: The communication between the user's browser and the Streamlit server, and between the server and the Google Gemini API, is handled by underlying libraries that use HTTPS. This is a standard and secure practice. Data is encrypted while it is traveling over the network.

---

### 4. Data Exposure Prevention
- **Assessment**: **MAJOR WEAKNESS**.
- **Vulnerability**: As noted in the baseline analysis, the application leaks internal error information directly to the user.
- **Impact**: When the `LLMService` fails, the raw exception is shown to the user (`st.error(f"An error occurred: {e}")`). This can leak stack traces, library names, file paths, and other internal details that could help an attacker understand the system and plan an attack.
- **Risk Rating**: **HIGH (7/10)**. Information leakage provides a roadmap for attackers.

- **Recommendation**:
    - **Implement Generic Error Messages**: User-facing errors should always be generic and non-specific.
        **Before**: `st.error(f"An error occurred: {e}")`
        **After**: `st.error("An unexpected error occurred while communicating with the AI service. Please try again later.")`
    - **Log Detailed Errors**: The actual exception (`e`) should be logged to a secure, server-side-only location (e.g., a log file, a logging service) for developers to debug.

---

### Summary & Recommendations

- **Finding 1**: **Plaintext Storage of User Data**. User chat histories are stored in an insecure, unencrypted JSON file. This is a critical privacy and data protection failure.
- **Finding 2**: **Information Leakage Through Errors**. Internal system errors are exposed to the user, revealing implementation details.

- **Priority Recommendations**:
    1.  **Disable Chat Logging or Secure It (Highest Priority)**: Immediately refactor the `ChatManager` to either stop saving files to disk entirely or implement a secure storage mechanism (like a database or encrypted files). For a bootcamp project, the simplest secure option is to make chats ephemeral.
    2.  **Sanitize Error Messages**: Change all `st.error` calls that display raw exception information to show only generic, user-friendly messages. Implement server-side logging to capture the technical details for debugging.
