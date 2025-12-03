# Data Protection & Privacy Security Review

This document assesses the application's data protection and privacy measures, focusing on how it handles sensitive data both at rest and in transit.

---

### Sensitive Data Identification
- **Personal Identifiable Information (PII)**: The application does not explicitly ask for PII like names or email addresses. However, users could voluntarily enter PII into the chat. The chat history, therefore, must be treated as potentially containing sensitive PII.
- **Authentication Credentials**: The primary credential is the `GEMINI_API_KEY`, which is handled correctly via environment variables. There are no user passwords.
- **Business Sensitive Information**: The content of conversations could be business-sensitive if the chatbot is used for work-related purposes.

**Assessment**: The most sensitive user-related data is the conversation history itself.
**Score: N/A**

---

### Data at Rest Security
- **Database Encryption**: **CRITICAL VULNERABILITY**. The `chat_history.db` SQLite file is stored on the filesystem as plain text. Anyone with read access to the file can view all conversation histories for all users. This is the most significant data protection failure.
- **File Storage Security**: The application does not handle file uploads or other file storage beyond the database.
- **Configuration File Security**: The use of a `.env` file, which is correctly excluded from Git, is a secure method for handling the API key.

**Assessment**: The lack of encryption for data at rest is a critical vulnerability that makes all conversation data vulnerable to theft if the server's filesystem is compromised.
**Score: 2/10 (Critical)**

---

### Data in Transit Security
- **HTTPS Enforcement**: The application relies on the `requests` library to make calls to the Google Gemini API. These calls are made over HTTPS, which is the industry standard for protecting data in transit. This is handled correctly.
- **Internal Communication**: All communication within the application (e.g., between the controller and services) happens in memory on the server and is not exposed to the network.

**Assessment**: Data in transit to and from the external LLM API is appropriately secured using HTTPS.
**Score: 10/10 (Excellent)**

---

### Data Exposure Prevention
- **API Response Data Filtering**: The application's "API" is the Streamlit interface. It correctly displays only the current user's chat history based on their `session_id`.
- **Error Message Data Leakage**: As noted in the code quality review, error messages are generally generic and do not leak sensitive information like stack traces or configuration details to the user.
- **Log File Data Exposure**: The `logs/app.log` file logs user inputs and AI responses. This means the logs contain the full, unencrypted conversation histories. If the log files are not properly secured with strict file permissions, they become another vector for data exfiltration.

**Assessment**: The application avoids leaking data through its direct UI, but the logs present a significant data exposure risk if not properly managed.
**Score: 5/10 (Mediocre)**

---

## Overall Data Protection Score: 4/10 (Needs Improvement)

### Summary & Recommendations

The application correctly handles security for data in transit and for configuration secrets. However, it fails critically in protecting data at rest. The unencrypted database and logging of sensitive conversations represent major privacy and data protection liabilities.

**Recommendations:**

1.  **Implement Encryption at Rest**:
    *   **Action**: Encrypt the entire SQLite database file.
    *   **High-Priority Option**: Use a library like `sqlcipher` (via `pysqlcipher3` in Python) which provides a transparent, encrypted version of SQLite. The application would need to be configured with a database encryption key (stored securely as another environment variable) to read and write to the database.
    *   **Alternative**: Encrypt the contents of the chat messages themselves within the application before writing them to the database, and decrypt them after reading. This is more complex to manage but provides granular control.

2.  **Sanitize or Secure Logs**:
    *   **Action**: Prevent sensitive information from being exposed in logs.
    *   **Option A (Best for Privacy)**: Do not log the content of user prompts and AI responses. Log metadata instead (e.g., "User with session_id X sent a message of Y characters").
    *   **Option B (If logs are required for debugging)**: Ensure the log files (`logs/app.log`) have very strict file permissions (e.g., only readable by the application user). Implement log rotation and a retention policy to automatically delete old logs.

3.  **Implement a Data Retention Policy**:
    *   **Action**: Automatically delete old chat histories to minimize the amount of data stored.
    *   **Implementation**: Create a scheduled job (e.g., a cron job or a background task) that runs periodically and deletes records from the `chat_history` table that are older than a defined period (e.g., 30 days). This is a key principle of "privacy by design."
