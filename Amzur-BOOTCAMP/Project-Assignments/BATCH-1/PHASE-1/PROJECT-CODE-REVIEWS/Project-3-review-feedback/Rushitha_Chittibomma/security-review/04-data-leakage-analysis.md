# Data Leakage Security Analysis

## Project Information
- **Candidate Name:** Rushitha Chittibomma
- **Project Title:** Smart Knowledge Repository (Web Content Analyzer)
- **Review Date:** September 17, 2023
- **Reviewer:** GitHub Copilot
- **Data Leakage Risk Grade:** D (3/10)

---

## Executive Summary

The Smart Knowledge Repository (Web Content Analyzer) project demonstrates significant vulnerabilities to data leakage across multiple components. The application lacks proper data sanitization, implements insufficient logging practices, and features several unprotected data export mechanisms. These issues create a high risk for inadvertent exposure of sensitive information including API keys, PII contained in scraped content, and potentially confidential analysis results. The absence of redaction mechanisms and insecure storage practices further exacerbate these risks. Immediate remediation is required to prevent potentially serious data exposure incidents.

---

## Data Leakage Risk Assessment

### 1. API Key Exposure
**Risk Level: Critical (9/10)**

The application's handling of the OpenAI API key presents a critical security vulnerability:

#### Issues Identified:
- API key stored as plain text in environment variables
- No secrets management solution implemented
- Insecure error handling could leak the key through verbose logs
- Docker setup passes environment variable directly without secure management

**Code Example:**
```python
# backend/ai_service.py - Insecure API key handling
def __init__(self):
    # Get API key from environment variable
    api_key = os.getenv("OPENAI_API_KEY")
    if not api_key:
        raise ValueError("OPENAI_API_KEY environment variable is missing.")

    # Configure OpenAI
    openai.api_key = api_key
```

**Impact:**
If the application crashes or produces debug output, there's a risk the API key could be included in logs. Additionally, any container orchestration logs might capture the key. This exposure could lead to unauthorized usage, financial loss, and possible access to any data stored in the OpenAI account.

**Recommendation:**
Implement a secure secrets management solution:

```python
import keyring
from cryptography.fernet import Fernet

class SecureCredentialManager:
    def __init__(self):
        self.encryption_key = self._get_or_create_encryption_key()
        self.cipher = Fernet(self.encryption_key)
        
    def _get_or_create_encryption_key(self):
        # Retrieve key from secure storage or create new one
        key = keyring.get_password("web_content_analyzer", "encryption_key")
        if not key:
            key = Fernet.generate_key().decode()
            keyring.set_password("web_content_analyzer", "encryption_key", key)
        return key.encode()
    
    def get_api_key(self, service_name):
        # Get encrypted key from environment
        encrypted_key = os.getenv(f"{service_name.upper()}_API_KEY")
        if not encrypted_key:
            raise ValueError(f"{service_name} API key not configured")
        
        # Decrypt the key
        try:
            return self.cipher.decrypt(encrypted_key.encode()).decode()
        except Exception:
            raise ValueError("Failed to decrypt API key")

# Usage
credential_manager = SecureCredentialManager()
openai.api_key = credential_manager.get_api_key("openai")
```

### 2. Unfiltered Error Messages
**Risk Level: High (8/10)**

The application extensively uses print statements for error handling and logs potentially sensitive information:

#### Issues Identified:
- Verbose exception handling reveals implementation details
- Error messages may include sensitive content from scraped websites
- API responses may include raw error messages
- No filtering of potentially sensitive information in logs

**Code Example:**
```python
# backend/ai_service.py - Leaks potentially sensitive information in errors
except json.JSONDecodeError as e:
    print(f"JSON parsing error: {e}")
    print(f"Raw response: {analysis_result}")
    # Return default structure if JSON parsing fails
    return self.default_analysis.copy()
except Exception as e:
    print(f"Error in AI analysis: {str(e)}")
    return self.default_analysis.copy()
```

**Impact:**
Verbose error messages could leak internal system details that assist attackers in exploitation. Additionally, if scraped content contains PII or other sensitive data, this could be exposed through error messages in logs, creating potential privacy violations.

**Recommendation:**
Implement structured logging with redaction:

```python
import logging
import re
from functools import wraps

# Configure logger
logger = logging.getLogger("web_analyzer")
logger.setLevel(logging.INFO)

# Create handler with proper formatting
handler = logging.StreamHandler()
handler.setFormatter(logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s'))
logger.addHandler(handler)

# Data redaction patterns
REDACTION_PATTERNS = [
    (r'sk-[a-zA-Z0-9]{30,}', '[REDACTED_API_KEY]'),  # OpenAI API key pattern
    (r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b', '[REDACTED_EMAIL]'),  # Email pattern
    (r'\b(?:\d{3}-\d{2}-\d{4}|\d{9})\b', '[REDACTED_SSN]'),  # SSN pattern
    (r'\b\d{13,16}\b', '[REDACTED_CARD_NUMBER]')  # Credit card pattern
]

def redact_sensitive_data(text):
    """Redact sensitive information from strings"""
    if not isinstance(text, str):
        return text
    
    redacted = text
    for pattern, replacement in REDACTION_PATTERNS:
        redacted = re.sub(pattern, replacement, redacted)
    return redacted

def safe_logging(func):
    """Decorator to ensure safe logging of function calls"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        try:
            return func(*args, **kwargs)
        except Exception as e:
            # Redact any sensitive data in the exception message
            safe_error = str(redact_sensitive_data(str(e)))
            logger.error(f"Error in {func.__name__}: {safe_error}")
            # Return a safe response
            return {"status": "error", "message": "An internal error occurred"}
    return wrapper

# Usage example
@safe_logging
def analyze_text(text, prompt=None):
    # Implementation
```

### 3. Insecure Data Export
**Risk Level: High (7/10)**

The application allows unprotected data exports without encryption or access controls:

#### Issues Identified:
- PDF, JSON, and CSV exports created without encryption
- No access control for exported files
- Exports may contain sensitive data from web content
- No sanitization of exported content
- No audit trail for exports

**Code Example:**
```python
# backend/export_service.py - Unprotected exports
def to_json(self, data: Dict) -> str:
    """
    Export analysis results to JSON format.
    
    Args:
        data (Dict): Analysis results to export
        
    Returns:
        str: JSON string
    """
    return json.dumps(data, indent=2)

# streamlit_app.py - Direct download without protection
st.download_button(
    label="Export as JSON",
    data=json.dumps(result, indent=2),
    file_name="analysis_report.json",
    mime="application/json"
)
```

**Impact:**
Exports could contain sensitive information from scraped websites, including PII, copyrighted material, or confidential information. Without encryption or access controls, this creates data leakage risks if the exported files are shared, lost, or intercepted.

**Recommendation:**
Implement encryption and data sanitization for exports:

```python
from cryptography.fernet import Fernet
import hashlib

class SecureExportService:
    def __init__(self):
        self.timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        
    def generate_export_key(self, user_id):
        """Generate a unique encryption key for each export"""
        key_material = f"{user_id}:{self.timestamp}:{os.urandom(16).hex()}"
        key_bytes = hashlib.sha256(key_material.encode()).digest()
        return base64.urlsafe_b64encode(key_bytes)
        
    def encrypt_export(self, data, user_id):
        """Encrypt export data"""
        key = self.generate_export_key(user_id)
        cipher = Fernet(key)
        encrypted_data = cipher.encrypt(json.dumps(data).encode())
        
        return {
            "encrypted_data": encrypted_data,
            "key": key.decode(),  # Would be securely transmitted to user
            "timestamp": self.timestamp
        }
    
    def sanitize_for_export(self, data):
        """Remove or redact sensitive information before export"""
        sanitized = copy.deepcopy(data)
        
        # Remove potentially sensitive fields
        if "content" in sanitized:
            # Keep metadata but remove full content
            if "main_content" in sanitized["content"]:
                content_length = len(sanitized["content"]["main_content"])
                sanitized["content"]["content_length"] = content_length
                sanitized["content"]["main_content"] = "[Content redacted for security]"
        
        # Add export metadata
        sanitized["export_metadata"] = {
            "timestamp": self.timestamp,
            "export_type": "sanitized",
            "data_usage_notice": "This data may be subject to privacy restrictions."
        }
        
        return sanitized
```

### 4. Web Content Processing Risks
**Risk Level: Medium (6/10)**

The application processes arbitrary web content without sufficient filtering or sanitization:

#### Issues Identified:
- No scanning for PII in web content
- No validation of content against policy requirements
- Potential for scraping restricted or sensitive content
- No content filtering based on classifications

**Code Example:**
```python
# backend/web_scraper.py - Insufficient content handling
def fetch_page(self, url: str) -> Optional[Dict]:
    """
    Fetches a webpage and returns its content.
    
    Args:
        url (str): The URL to fetch
        
    Returns:
        Dict containing status, content, and error message if any
    """
    try:
        # Validate URL format and security
        if not validate_url(url):
            return {"status": "error", "error": "Invalid URL format"}
        
        if not check_url_security(url):
            return {"status": "error", "error": "URL failed security check"}

        # Fetch the page
        response = self.session.get(url, timeout=self.timeout)
        response.raise_for_status()

        return {
            "status": "success",
            "content": response.text,
            "status_code": response.status_code,
            "headers": dict(response.headers)
        }
```

**Impact:**
The application could inadvertently scrape and process sensitive information including PII, copyrighted content, or confidential information, creating legal and privacy risks. Without content filtering, this data could be incorporated into analysis results and exports.

**Recommendation:**
Implement content scanning and classification:

```python
import re
from nltk.tokenize import word_tokenize
from profanity_filter import ProfanityFilter

class ContentSecurityScanner:
    def __init__(self):
        self.pf = ProfanityFilter()
        self.pii_patterns = {
            'email': r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b',
            'ssn': r'\b(?:\d{3}-\d{2}-\d{4}|\d{9})\b',
            'phone': r'\b(?:\+\d{1,2}\s)?\(?\d{3}\)?[\s.-]\d{3}[\s.-]\d{4}\b',
            'credit_card': r'\b(?:\d{4}[- ]){3}\d{4}|\d{16}\b'
        }
        
    def scan_for_pii(self, content):
        """Scan content for PII and return findings"""
        findings = {}
        for pii_type, pattern in self.pii_patterns.items():
            matches = re.findall(pattern, content)
            if matches:
                findings[pii_type] = len(matches)
        
        return {
            "has_pii": len(findings) > 0,
            "pii_types": findings,
            "severity": "high" if findings else "low"
        }
    
    def scan_for_sensitive_content(self, content):
        """Scan for sensitive content types"""
        results = {
            "has_profanity": self.pf.is_profane(content),
            "pii_scan": self.scan_for_pii(content)
        }
        
        return {
            "is_sensitive": results["has_profanity"] or results["pii_scan"]["has_pii"],
            "scan_results": results
        }
    
    def redact_sensitive_content(self, content):
        """Redact sensitive information from content"""
        # Redact PII
        redacted = content
        for pii_type, pattern in self.pii_patterns.items():
            redacted = re.sub(pattern, f"[REDACTED_{pii_type.upper()}]", redacted)
        
        # Clean profanity
        redacted = self.pf.censor(redacted)
        
        return redacted
```

### 5. Insecure CORS Configuration
**Risk Level: Medium (6/10)**

The application uses a permissive CORS policy allowing requests from any origin:

#### Issues Identified:
- Wide-open CORS policy with `allow_origins=["*"]`
- All HTTP methods and headers permitted
- Missing CSRF protection
- No HTTPS enforcement for secure connections

**Code Example:**
```python
# backend/api.py - Insecure CORS configuration
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)
```

**Impact:**
The permissive CORS policy enables any website to make requests to the API, increasing the risk of CSRF attacks and unauthorized data access. This could lead to data leakage if a malicious site tricks a user's browser into making authenticated requests to the API.

**Recommendation:**
Implement a restrictive CORS policy:

```python
from fastapi.middleware.cors import CORSMiddleware
from fastapi.middleware.trustedhost import TrustedHostMiddleware
import os

# Define allowed origins based on environment
if os.getenv("ENVIRONMENT") == "production":
    allowed_origins = [
        "https://yourproductiondomain.com",
        "https://api.yourproductiondomain.com"
    ]
elif os.getenv("ENVIRONMENT") == "staging":
    allowed_origins = [
        "https://staging.yourproductiondomain.com",
        "https://api-staging.yourproductiondomain.com"
    ]
else:
    # Development environment
    allowed_origins = [
        "http://localhost:3000",
        "http://localhost:8000",
        "http://localhost:8001",
        "http://localhost:8501"
    ]

# Apply strict CORS policy
app.add_middleware(
    CORSMiddleware,
    allow_origins=allowed_origins,
    allow_credentials=True,
    allow_methods=["GET", "POST", "OPTIONS"],
    allow_headers=["Authorization", "Content-Type"]
)

# Add trusted host validation
app.add_middleware(
    TrustedHostMiddleware, 
    allowed_hosts=["localhost", "127.0.0.1"] + 
                  [origin.replace("https://", "").replace("http://", "") 
                   for origin in allowed_origins]
)
```

---

## Recommendations Summary

### Critical Fixes (Immediate Priority)

1. **Secure API Key Management:**
   - Implement an encrypted secrets management solution
   - Remove plain text API keys from environment variables
   - Add key rotation mechanisms
   - Use a service like AWS Secrets Manager or HashiCorp Vault

2. **Fix Error Handling and Logging:**
   - Implement proper structured logging
   - Add data redaction for sensitive information
   - Replace print statements with secure logging
   - Configure appropriate log levels by environment

### High Priority Fixes (Within 1 Month)

3. **Secure Data Exports:**
   - Add encryption for all exported data
   - Implement data sanitization before export
   - Add access controls and audit logs for exports
   - Create time-limited download links

4. **Web Content Security:**
   - Implement PII detection and redaction
   - Add content classification mechanisms
   - Create policies for handling sensitive content
   - Add consent and attribution mechanisms for scraped content

5. **Fix CORS Configuration:**
   - Restrict allowed origins to specific domains
   - Limit allowed HTTP methods and headers
   - Implement CSRF protection
   - Enforce HTTPS for all connections

### Medium Priority Fixes (Within 3 Months)

6. **Implement Data Minimization:**
   - Create data retention policies
   - Add mechanisms to automatically clean up old data
   - Implement configurable content filtering

7. **Add Privacy Controls:**
   - Create a privacy policy
   - Add consent mechanisms for data processing
   - Implement data subject rights handling

---

## Conclusion

The Smart Knowledge Repository project demonstrates significant data leakage risks that require immediate attention. The most critical issues involve insecure API key management, unfiltered error messages, and unprotected data exports. These vulnerabilities could lead to the exposure of sensitive information, unauthorized access to services, and potential privacy violations.

By implementing the recommended security measures, particularly around credential management, secure logging, and data sanitization, the application can significantly reduce its data leakage risk profile. Additionally, adding proper content scanning and classification will help prevent inadvertent processing of sensitive information from scraped websites.

The overall data leakage risk grade of D (3/10) reflects the need for substantial improvements to bring the application to an acceptable security level. With the recommended changes implemented, the application could achieve a significantly higher security rating and provide users with appropriate data protection assurances.
