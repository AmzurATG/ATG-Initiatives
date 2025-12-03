# Data Protection & Privacy Security Review

**Report Date:** 2025-09-09
**Project:** Smart Knowledge Repository
**Name:** Revanth Yalamanchili
**Reviewer:** GitHub Copilot, Data Protection Expert

---

## 1. Executive Summary

**Overall Score: 5/10 (Needs Improvement)**

This review assesses the application's data protection and privacy practices. The application handles Personally Identifiable Information (PII) and a critical API secret. While the management of the API secret in transit and via the Streamlit secrets management system is excellent, the handling of PII at rest is a significant weakness.

The primary findings are that **PII is stored unencrypted on disk**, and the **lack of authentication exposes a data export feature**, creating a data leakage risk. The application does not currently meet the standard data protection principles required for handling PII.

---

## 2. Data Protection Assessment

### Sensitive Data Identification
-   **Personal Identifiable Information (PII):** **YES**. The `profiles.db` database contains the `name`, `role`, and `bio` of company leadership. This constitutes PII.
-   **Financial Data:** No.
-   **Authentication Credentials:** No (as there is no authentication).
-   **Business Sensitive Information:** The `GOOGLE_API_KEY` is a critical business secret.

### Data at Rest Security
-   **Risk:** **HIGH**
-   **Score: 3/10 (Poor)**
-   **Analysis:**
    -   **Database Encryption:** The `profiles.db` SQLite file is stored unencrypted on the server's filesystem. If an attacker gains access to the server, they can simply copy this file and have access to all the PII within it.
    -   **FAISS Index:** Similarly, the `profiles.faiss` index is unencrypted. While it doesn't store the raw text, the vector embeddings could potentially be reverse-engineered or used for other malicious purposes.
    -   **Configuration Files:** The `config.yaml` file for the (recommended) `streamlit-authenticator` would contain hashed passwords. Filesystem security is crucial to protect this.
-   **Recommendation:**
    -   **Filesystem-Level Encryption (Recommended):** The easiest and most comprehensive solution is to ensure the application is deployed on a host with full-disk encryption (e.g., AWS EBS with encryption enabled, Azure Disk Encryption). This protects all application files, including the database and index, from being read if the physical disk is compromised.
    -   **Application-Level Database Encryption (Advanced):** For higher security, use a library like `sqlcipher` via a Python wrapper (`pysqlcipher3`) to encrypt the SQLite database file itself with a password. This adds complexity but protects the data even if the file is exfiltrated from an unencrypted filesystem.

### Data in Transit Security
-   **Risk:** **LOW**
-   **Score: 8/10 (Good)**
-   **Analysis:**
    -   **API Communication:** The application communicates with the Google Gemini API over HTTPS, which is secure.
    -   **HTTPS Enforcement:** It is assumed that in a production environment, the Streamlit application itself would be deployed behind a reverse proxy (like Nginx or Caddy) that enforces HTTPS for all client connections.
-   **Recommendation:** Ensure any production deployment includes HTTPS enforcement.

### Data Exposure Prevention
-   **Risk:** **HIGH**
-   **Score: 4/10 (Needs Improvement)**
-   **Analysis:**
    -   **API Response Data Filtering:** The `get_all_profiles_as_dicts` method in the repository and the "Export to JSON" feature in the admin tab are designed to export all data.
    -   **Information Leakage via Unauthenticated Admin Panel:** **CRITICAL FINDING**. Because the admin panel is not protected by authentication, any user can access the "Export to JSON" button and download the entire database of PII. This is a significant data leakage vulnerability.
    -   **Error Message Data Leakage:** As noted in other reviews, broad `except Exception` clauses could potentially leak stack traces or other internal application details in error messages if the app is not in production mode.
    -   **Logging Data Exposure:** Using `print()` for logging is risky. If a Pydantic model were ever printed in an error, it could expose all of its data in the logs. A structured logger should be configured to redact sensitive fields.
-   **Recommendation:**
    -   **Implement Authentication (Critical):** The data export feature in the admin panel **must** be protected by authentication and authorization, as recommended in the `01-authentication-security.md` review. This is the highest priority fix to prevent data leakage.
    -   **Implement Redaction in Logging:** When implementing a structured logger, use filters or formatters to redact sensitive PII fields if they are ever logged.

### Privacy Compliance Analysis
-   **Risk:** **MEDIUM**
-   **Score: 4/10 (Needs Improvement)**
-   **Analysis:**
    -   **GDPR/CCPA:** The data subjects (Amzur leadership) are likely identifiable. If any are EU or California residents, privacy regulations could apply. The current application does not have any mechanisms for handling data subject rights (e.g., the right to be forgotten).
    -   **Data Retention:** There are no data retention policies. Data is stored indefinitely until manually deleted or overwritten.
    -   **User Consent:** The data is scraped from a public website. While this is often permissible, the ethics and potential terms of service of the source website should be considered.
    -   **Data Deletion:** The application provides a `delete_profile` function, which is good. This provides the technical capability to handle a "right to be forgotten" request.
-   **Recommendation:**
    -   For a production application handling PII, a basic privacy policy should be created and made accessible to users.
    -   The data deletion functionality should be exposed in the admin UI to an authorized administrator to handle removal requests.
