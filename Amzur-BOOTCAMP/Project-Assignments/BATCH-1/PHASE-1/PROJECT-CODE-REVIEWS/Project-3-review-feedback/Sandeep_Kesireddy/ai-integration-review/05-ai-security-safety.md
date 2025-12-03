# Smart Knowledge Repository - AI Security & Safety Implementation Review

## Executive Summary

This review assesses the AI security and safety measures implemented in the Smart Knowledge Repository project. The analysis reveals significant gaps in AI security practices, with particular concerns around input validation, content moderation, data privacy, and access control. The application demonstrates a minimal security foundation, lacking essential safeguards against prompt injection, content safety threats, and unauthorized access.

**Overall AI Security & Safety Implementation Score: 2/10 (CRITICAL)**

### Key Findings

1. **Complete Absence of Authentication and Authorization**: The application has no protection for API endpoints, allowing unrestricted access to sensitive AI operations.

2. **No Input Sanitization for AI Prompts**: User queries are passed directly to AI services without validation, creating prompt injection vulnerabilities.

3. **Missing Content Moderation**: No filtering or safety checks exist for AI-generated content, increasing the risk of harmful outputs.

4. **Poor Data Privacy Protection**: User queries and scraped content may contain PII, which is stored without anonymization or protection.

5. **Insecure API Key Management**: OpenAI API keys are stored in environment variables without proper secrets management.

6. **Absent Rate Limiting**: No protection against API abuse, creating both security and cost exposure.

7. **Minimal Error Handling**: Error messages may leak sensitive information about AI implementation.

8. **Insufficient Logging**: Lack of security-focused audit logging for AI operations and access.

This review identifies critical security vulnerabilities requiring immediate attention to prevent potential security breaches, data leaks, and misuse of AI capabilities.

## Detailed Security Assessment

### Input Security & Prompt Injection Prevention

**Score: 2/10 (CRITICAL)**

The application lacks fundamental protections against prompt injection and demonstrates minimal input sanitization:

#### Key Findings:

1. **No Input Validation for AI Prompts**:
   - User queries are passed directly to OpenAI without sanitization
   - No prompt injection detection or prevention mechanisms
   - No input filtering for malicious content

```python
# backend/app/services/embedding_service.py - retrieve_and_generate function
def retrieve_and_generate(self, query: str, top_k: int = 3, document_ids: Optional[List[str]] = None) -> Tuple[str, List[str], List[dict]]:
    # No validation or sanitization of user query before processing
    logger.info("[retrieve_and_generate] Enter: query='%s', top_k=%d", query, top_k)
    # Direct processing of user query without sanitization
    q_embs = self._encode_texts([query])
```

2. **Vulnerable Prompt Construction**:
   - Direct incorporation of user input in prompts
   - No context boundary enforcement
   - No escape sequence detection or neutralization

```python
# backend/app/services/llm_service.py - generate_answer function
def generate_answer(query: str, context: str, max_tokens: int = 256, temperature: float = 0.2) -> str:
    if openai_client:
        # Prompt directly incorporates user query without sanitization
        prompt = f"Context:\n{context}\n\nQuestion: {query}\nAnswer:"
        try:
            response = openai_client.chat.completions.create(
                model=MODEL,
                messages=[{"role": "system", "content": "You are a helpful web content analyst."},
                          {"role": "user", "content": prompt}],
```

3. **Missing Input Boundaries**:
   - No input length restrictions
   - No validation of input complexity or structure
   - No restrictions on special characters or escape sequences

#### Recommendations:

1. **Implement Input Sanitization**:

```python
def sanitize_prompt_input(input_text: str) -> str:
    """Sanitize user input for AI prompts to prevent prompt injection."""
    # Remove potential prompt injection patterns
    sanitized = re.sub(r'(system:|user:|assistant:|<\|.+?\|>)', '', input_text)
    # Limit maximum input length
    return sanitized[:1024]  # Reasonable max length

# Usage in llm_service.py
def generate_answer(query: str, context: str, max_tokens: int = 256, temperature: float = 0.2) -> str:
    # Sanitize inputs before constructing prompt
    sanitized_query = sanitize_prompt_input(query)
    sanitized_context = sanitize_prompt_input(context)
    
    prompt = f"Context:\n{sanitized_context}\n\nQuestion: {sanitized_query}\nAnswer:"
    # Rest of function remains the same
```

2. **Add Prompt Injection Detection**:

```python
def detect_prompt_injection(input_text: str) -> bool:
    """Detect potential prompt injection patterns."""
    # Common injection patterns
    injection_patterns = [
        r'(ignore|disregard|forget).*previous.*instructions',
        r'system:\s*',
        r'<\|.+?\|>',
        r'you are now [a-z\s]+',
        r'your (role|purpose|goal) is now'
    ]
    
    for pattern in injection_patterns:
        if re.search(pattern, input_text, re.IGNORECASE):
            return True
    return False

# Usage in embedding_service.py
def retrieve_and_generate(self, query: str, top_k: int = 3, document_ids: Optional[List[str]] = None):
    if detect_prompt_injection(query):
        logger.warning("Potential prompt injection detected: %s", query)
        return "Sorry, your request contained potentially unsafe instructions and cannot be processed.", [], []
```

3. **Implement Input Validation and Boundaries**:

```python
def validate_ai_input(input_text: str, max_length: int = 1024) -> Tuple[bool, str]:
    """Validate AI input for safety and boundaries."""
    # Check for empty input
    if not input_text or not input_text.strip():
        return False, "Input cannot be empty"
    
    # Check length boundaries
    if len(input_text) > max_length:
        return False, f"Input exceeds maximum length of {max_length} characters"
    
    # Validate input complexity to prevent DoS
    if input_text.count('\n') > 100:
        return False, "Input contains too many line breaks"
        
    return True, ""
```

### Content Safety & Moderation

**Score: 1/10 (CRITICAL)**

The application has no content moderation or safety filtering mechanisms:

#### Key Findings:

1. **No Content Filtering**:
   - AI-generated responses are returned directly to users without filtering
   - No checks for inappropriate, harmful, or toxic content
   - No content classification or rating system

2. **Missing Bias Detection**:
   - No mechanisms to detect or mitigate bias in AI-generated content
   - No review process for content quality or safety

3. **Absent Content Policies**:
   - No defined content policies or safety guidelines
   - No user reporting mechanism for inappropriate content

```python
# backend/app/services/llm_service.py - Direct return of model response without filtering
def generate_answer(query: str, context: str, max_tokens: int = 256, temperature: float = 0.2) -> str:
    # ...
    try:
        response = openai_client.chat.completions.create(
            # ...
        )
        # Response returned directly without content safety checks
        answer = getattr(response.choices[0].message, 'content', '').strip()
        logger.info("OpenAI answer generated successfully.")
        return answer or ""
    # ...
```

#### Recommendations:

1. **Implement Basic Content Moderation**:

```python
def moderate_content(content: str) -> Tuple[bool, str, str]:
    """Moderate content for toxic or inappropriate material."""
    # List of problematic patterns to check
    toxic_patterns = [
        (r'\b(f[u*]ck|sh[i*]t|b[i*]tch|d[i*]ck|c[u*]nt|a[s*][s*]hole)\b', "Profanity detected"),
        (r'\b(kill|murder|hurt|harm|injure)\b.*\b(yourself|themselves|himself|herself)\b', "Self-harm content"),
        (r'\b(nazis?|hitler|fascist|white\s*power|white\s*supremacy)\b', "Extremist content"),
        (r'\b(how\s+to\s+make|create|build)\b.*\b(bomb|explosive|weapon|poison)\b', "Dangerous instructions"),
        (r'\b(hack|crack|steal|illegal\s+access)\b', "Potentially illegal activity")
    ]
    
    original_content = content
    filtered_content = content
    is_safe = True
    reason = ""
    
    # Check for toxic patterns
    for pattern, violation_reason in toxic_patterns:
        if re.search(pattern, content, re.IGNORECASE):
            is_safe = False
            reason = violation_reason
            # Replace matched content with [FILTERED] tag
            filtered_content = re.sub(pattern, "[FILTERED]", filtered_content, flags=re.IGNORECASE)
    
    if is_safe:
        return True, original_content, ""
    else:
        return False, filtered_content, reason

# Usage in llm_service.py
def generate_answer(query: str, context: str, max_tokens: int = 256, temperature: float = 0.2) -> str:
    # ...existing code...
    
    # Apply content moderation to response
    is_safe, filtered_answer, reason = moderate_content(answer)
    
    if not is_safe:
        logger.warning("Generated content was moderated: %s", reason)
        return f"[Note: The response contained content that was filtered: {reason}]\n\n{filtered_answer}"
    
    return answer
```

2. **Add PII Detection and Redaction**:

```python
def detect_and_redact_pii(content: str) -> Tuple[str, List[str]]:
    """Detect and redact personally identifiable information (PII) from content."""
    pii_patterns = {
        "email": r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b',
        "phone": r'\b(\+\d{1,2}\s?)?\(?\d{3}\)?[\s.-]?\d{3}[\s.-]?\d{4}\b',
        "ssn": r'\b\d{3}-\d{2}-\d{4}\b',
        "credit_card": r'\b(?:\d{4}[-\s]?){3}\d{4}\b',
        "address": r'\b\d+\s+([A-Za-z]+\s+){1,5},\s+[A-Za-z]+,\s+[A-Z]{2}\s+\d{5}\b',
        "ip_address": r'\b\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}\b'
    }
    
    redacted_content = content
    detected_pii_types = []
    
    for pii_type, pattern in pii_patterns.items():
        matches = re.findall(pattern, content, re.IGNORECASE)
        if matches:
            detected_pii_types.append(pii_type)
            redacted_content = re.sub(pattern, f"[REDACTED {pii_type.upper()}]", redacted_content, flags=re.IGNORECASE)
    
    return redacted_content, detected_pii_types
```

3. **Implement OpenAI's Content Moderation API**:

```python
def check_content_safety(text: str) -> Tuple[bool, str]:
    """Use OpenAI's moderation API to check content safety."""
    try:
        response = openai_client.moderations.create(input=text)
        result = response.results[0]
        
        if result.flagged:
            # Get specific violation categories
            categories = [cat for cat, flagged in result.categories.items() if flagged]
            return False, f"Content flagged for: {', '.join(categories)}"
        return True, ""
    except Exception as e:
        logger.exception("Content moderation API error: %s", e)
        # Fail open to avoid blocking legitimate content when service fails
        return True, ""
```

### Data Privacy & Protection

**Score: 2/10 (CRITICAL)**

The application demonstrates poor data privacy practices:

#### Key Findings:

1. **No PII Detection or Protection**:
   - User queries might contain personal information
   - No detection or redaction of sensitive data
   - No data minimization strategy

```python
# backend/app/services/embedding_service.py - User queries stored without PII detection
def retrieve_and_generate(self, query: str, top_k: int = 3, document_ids: Optional[List[str]] = None) -> Tuple[str, List[str], List[dict]]:
    # User query logged without anonymization
    logger.info("[retrieve_and_generate] Enter: query='%s', top_k=%d", query, top_k)
```

2. **Insecure Data Transmission**:
   - No explicit HTTPS enforcement
   - No API key rotation strategy
   - No encryption for data in transit

3. **Insufficient Data Management**:
   - No data retention policies
   - No user consent management
   - No data deletion capabilities

4. **Missing Privacy Documentation**:
   - No privacy policy
   - No terms of use
   - No explanation of data processing practices

#### Recommendations:

1. **Implement PII Detection and Protection**:

```python
# Integrate with retrieve_and_generate method
def retrieve_and_generate(self, query: str, top_k: int = 3, document_ids: Optional[List[str]] = None) -> Tuple[str, List[str], List[dict]]:
    # Log sanitized version of query
    sanitized_query_for_logs = re.sub(r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b', '[REDACTED EMAIL]', query)
    sanitized_query_for_logs = re.sub(r'\b\d{3}-\d{2}-\d{4}\b', '[REDACTED SSN]', sanitized_query_for_logs)
    logger.info("[retrieve_and_generate] Enter: query='%s', top_k=%d", sanitized_query_for_logs, top_k)
    
    # Process query normally but with PII awareness
    q_embs = self._encode_texts([query])
    # ...rest of the method
```

2. **Add Data Anonymization**:

```python
def anonymize_data_for_ai_processing(data: str) -> str:
    """Anonymize data before sending to external AI services."""
    # Replace emails with placeholders
    anonymized = re.sub(r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b', 'EMAIL@EXAMPLE.COM', data)
    
    # Replace phone numbers
    anonymized = re.sub(r'\b(\+\d{1,2}\s?)?\(?\d{3}\)?[\s.-]?\d{3}[\s.-]?\d{4}\b', '(555) 555-5555', anonymized)
    
    # Replace SSNs
    anonymized = re.sub(r'\b\d{3}-\d{2}-\d{4}\b', '123-45-6789', anonymized)
    
    return anonymized
```

3. **Implement Data Retention Policy**:

```python
# In metadata_store.py - Add data retention management
def apply_data_retention_policy(max_age_days: int = 90) -> int:
    """Delete data older than the specified retention period.
    
    Args:
        max_age_days: Maximum age in days before data is deleted
        
    Returns:
        Number of records deleted
    """
    conn = get_db()
    cutoff_date = (datetime.now() - timedelta(days=max_age_days)).strftime('%Y-%m-%d')
    
    cursor = conn.cursor()
    cursor.execute("DELETE FROM chunk_metadata WHERE created_at < ?", (cutoff_date,))
    deleted_count = cursor.rowcount
    
    # Also delete associated FAISS embeddings and files
    # (implementation depends on how indices are mapped)
    
    conn.commit()
    conn.close()
    
    logger.info("Applied data retention policy: %d records deleted", deleted_count)
    return deleted_count
```

4. **Add Data Privacy Configuration**:

```python
# In config.py - Add privacy settings
class PrivacySettings:
    # Data retention period in days
    DATA_RETENTION_DAYS: int = int(os.getenv("DATA_RETENTION_DAYS", "90"))
    
    # Enable/disable PII detection
    PII_DETECTION_ENABLED: bool = os.getenv("PII_DETECTION_ENABLED", "true").lower() in ("1", "true", "yes")
    
    # Enable/disable content moderation
    CONTENT_MODERATION_ENABLED: bool = os.getenv("CONTENT_MODERATION_ENABLED", "true").lower() in ("1", "true", "yes")
    
    # Enable/disable data anonymization for external services
    DATA_ANONYMIZATION_ENABLED: bool = os.getenv("DATA_ANONYMIZATION_ENABLED", "true").lower() in ("1", "true", "yes")
```

### Access Control & Authorization

**Score: 1/10 (CRITICAL)**

The application completely lacks access controls:

#### Key Findings:

1. **No Authentication Mechanism**:
   - All API endpoints are publicly accessible
   - No user identity verification
   - No API key validation

```python
# backend/app/api/v1/endpoints.py - Unprotected API endpoints
@router.post("/retrieve_and_generate", response_model=RetrieveAndGenerateResponse)
def retrieve_and_generate_api(body: RetrieveAndGenerateRequest) -> RetrieveAndGenerateResponse:
    # No authentication check before processing
    logger.info("/retrieve_and_generate called for query: %s", body.query)
    embedding_service = EmbeddingService()
    # ...
```

2. **Missing Authorization Controls**:
   - No role-based access control
   - No resource-level permissions
   - All operations available to anyone

3. **Absent API Key Security**:
   - OpenAI API key stored in plain environment variables
   - No key rotation mechanisms
   - No secure secret management

```python
# backend/app/services/llm_service.py - Insecure API key handling
OPENAI_API_KEY: str = os.getenv("OPENAI_API_KEY", "")
openai_client: Optional[openai.OpenAI] = (
    openai.OpenAI(api_key=OPENAI_API_KEY) if OPENAI_API_KEY else None
)
```

4. **No Audit Logging**:
   - Limited request logging
   - No security event logging
   - No user action auditing

#### Recommendations:

1. **Implement API Authentication**:

```python
# In app/auth/auth.py
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, APIKeyHeader
from jose import JWTError, jwt
from datetime import datetime, timedelta
from typing import Optional

# Secret key should be stored securely
SECRET_KEY = os.getenv("JWT_SECRET_KEY")
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

# API key header for machine-to-machine auth
API_KEY_HEADER = APIKeyHeader(name="X-API-Key")

# API keys (should be stored in a database)
API_KEYS = {
    "test-key-1": {"roles": ["user"]},
    "admin-key-1": {"roles": ["admin", "user"]}
}

# OAuth2 bearer token for user auth
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

def verify_api_key(api_key: str = Depends(API_KEY_HEADER)):
    if api_key not in API_KEYS:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid API key",
            headers={"WWW-Authenticate": "APIKey"},
        )
    return api_key

def get_api_key_roles(api_key: str = Depends(verify_api_key)):
    return API_KEYS[api_key]["roles"]
```

2. **Add Role-Based Access Control**:

```python
# In app/auth/permissions.py
from fastapi import Depends, HTTPException, status
from typing import List

def required_roles(required: List[str]):
    def role_checker(roles: List[str] = Depends(get_api_key_roles)):
        for role in required:
            if role in roles:
                return True
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Insufficient permissions"
        )
    return role_checker

# Usage in endpoints.py
@router.post("/scrape_and_embed", response_model=ScrapeAndEmbedResponse)
def scrape_and_embed(
    data: ScrapeAndEmbedRequest,
    has_permission: bool = Depends(required_roles(["admin"]))
) -> ScrapeAndEmbedResponse:
    # Only accessible to admin role
    logger.info("/scrape_and_embed called for URL: %s", data.url)
    # ...rest of function
```

3. **Implement Secure Secrets Management**:

```python
# Use a secrets management solution like HashiCorp Vault
# app/services/secrets_manager.py
import hvac
from typing import Optional

class SecretsManager:
    def __init__(self):
        self.client = hvac.Client(url=os.getenv("VAULT_ADDR"))
        self.client.auth.approle.login(
            role_id=os.getenv("VAULT_ROLE_ID"),
            secret_id=os.getenv("VAULT_SECRET_ID")
        )
        
    def get_secret(self, path: str, key: str) -> Optional[str]:
        """Get a secret from the vault."""
        try:
            secret_data = self.client.secrets.kv.v2.read_secret_version(
                path=path
            )
            return secret_data["data"]["data"].get(key)
        except Exception as e:
            logger.exception("Failed to retrieve secret: %s", e)
            return None

# Usage in llm_service.py
secrets_manager = SecretsManager()
OPENAI_API_KEY = secrets_manager.get_secret("ai/openai", "api_key")
```

4. **Add Security Audit Logging**:

```python
# In app/core/security_logger.py
import logging
from datetime import datetime

security_logger = logging.getLogger("security")

def log_security_event(event_type: str, user_id: str, resource: str, action: str, success: bool, details: str = ""):
    """Log a security event for audit purposes."""
    timestamp = datetime.utcnow().isoformat()
    security_logger.info(
        f"SECURITY_EVENT|{timestamp}|{event_type}|{user_id}|{resource}|{action}|{success}|{details}"
    )

# Usage in endpoints.py
@router.post("/retrieve_and_generate", response_model=RetrieveAndGenerateResponse)
def retrieve_and_generate_api(
    body: RetrieveAndGenerateRequest,
    api_key: str = Depends(verify_api_key)
) -> RetrieveAndGenerateResponse:
    log_security_event(
        event_type="AI_QUERY",
        user_id=api_key,  # Or user ID for user auth
        resource="llm",
        action="generate",
        success=True,
        details=f"query_length={len(body.query)}"
    )
    # ...rest of function
```

### AI Service Security

**Score: 3/10 (POOR)**

The application has minimal security measures for external AI service integration:

#### Key Findings:

1. **Basic HTTPS Communication**:
   - Uses OpenAI SDK which enforces HTTPS
   - No additional certificate validation
   - No custom security headers

2. **Missing API Protection**:
   - No rate limiting implementation
   - No circuit breaker pattern
   - No protection against excessive costs

3. **Absent Monitoring**:
   - No security monitoring for AI services
   - Limited error handling for API failures
   - No alerting for security incidents

```python
# backend/app/services/llm_service.py - Basic error handling without security focus
def generate_answer(query: str, context: str, max_tokens: int = 256, temperature: float = 0.2) -> str:
    # ...
    try:
        response = openai_client.chat.completions.create(
            # ...
        )
        # ...
    except Exception as e:
        logger.exception("OpenAI error while generating answer: %s", e)
        return f"[OpenAI error: {e}]\n\nContext:\n{context}"
```

4. **Limited Vendor Security Assessment**:
   - No vendor security evaluation
   - No compliance verification
   - No data sovereignty considerations

#### Recommendations:

1. **Implement Rate Limiting and Circuit Breaker**:

```python
# In app/services/rate_limiter.py
import time
from functools import wraps

class RateLimiter:
    def __init__(self, max_calls: int, period: int):
        self.max_calls = max_calls
        self.period = period
        self.calls = []
    
    def __call__(self, func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            current_time = time.time()
            # Remove expired timestamps
            self.calls = [t for t in self.calls if current_time - t < self.period]
            
            if len(self.calls) >= self.max_calls:
                raise Exception(f"Rate limit exceeded: {self.max_calls} calls in {self.period}s")
                
            self.calls.append(current_time)
            return func(*args, **kwargs)
        return wrapper

# In app/services/circuit_breaker.py
import time
from enum import Enum
from functools import wraps

class CircuitState(Enum):
    CLOSED = 'closed'  # Normal operation
    OPEN = 'open'      # Not allowing calls
    HALF_OPEN = 'half-open'  # Testing if service is back

class CircuitBreaker:
    def __init__(self, failure_threshold: int = 5, recovery_timeout: int = 30):
        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout
        self.state = CircuitState.CLOSED
        self.failures = 0
        self.last_failure_time = 0
    
    def __call__(self, func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            if self.state == CircuitState.OPEN:
                if time.time() - self.last_failure_time > self.recovery_timeout:
                    # Try to recover
                    self.state = CircuitState.HALF_OPEN
                else:
                    raise Exception("Circuit breaker is open")
            
            try:
                result = func(*args, **kwargs)
                if self.state == CircuitState.HALF_OPEN:
                    self.state = CircuitState.CLOSED
                    self.failures = 0
                return result
            except Exception as e:
                self.failures += 1
                self.last_failure_time = time.time()
                if self.failures >= self.failure_threshold:
                    self.state = CircuitState.OPEN
                raise e
        return wrapper
```

2. **Add AI Service Security Monitoring**:

```python
# In app/monitoring/ai_service_monitor.py
from datetime import datetime
import logging

ai_monitor = logging.getLogger("ai_monitor")

class AIServiceMonitor:
    def __init__(self):
        self.request_count = 0
        self.error_count = 0
        self.last_error = None
        self.total_tokens = 0
        self.start_time = datetime.now()
    
    def record_request(self, tokens: int):
        """Record a successful API request."""
        self.request_count += 1
        self.total_tokens += tokens
    
    def record_error(self, error_msg: str):
        """Record a failed API request."""
        self.error_count += 1
        self.last_error = error_msg
        ai_monitor.error(f"AI service error: {error_msg}")
        
        # Check for security-related errors
        if "rate limit" in error_msg.lower():
            ai_monitor.warning("Possible rate limiting attack detected")
        elif "authentication" in error_msg.lower():
            ai_monitor.critical("AI service authentication failure")
    
    def get_metrics(self):
        """Get current monitoring metrics."""
        uptime = (datetime.now() - self.start_time).total_seconds()
        return {
            "request_count": self.request_count,
            "error_count": self.error_count,
            "error_rate": self.error_count / max(self.request_count, 1),
            "total_tokens": self.total_tokens,
            "tokens_per_request": self.total_tokens / max(self.request_count, 1),
            "uptime_seconds": uptime
        }

# Create a singleton instance
ai_service_monitor = AIServiceMonitor()
```

3. **Enhance OpenAI API Security**:

```python
# In app/services/llm_service.py - Enhanced security
import os
import openai
from app.services.circuit_breaker import CircuitBreaker
from app.services.rate_limiter import RateLimiter
from app.monitoring.ai_service_monitor import ai_service_monitor
from app.services.secrets_manager import SecretsManager

# Get API key from secure storage
secrets_manager = SecretsManager()
OPENAI_API_KEY = secrets_manager.get_secret("ai/openai", "api_key")

# Set up OpenAI client with security
openai_client = None
if OPENAI_API_KEY:
    openai_client = openai.OpenAI(api_key=OPENAI_API_KEY)

# Apply rate limiter (100 calls per minute)
@RateLimiter(max_calls=100, period=60)
# Apply circuit breaker (5 failures before opening circuit)
@CircuitBreaker(failure_threshold=5, recovery_timeout=30)
def generate_answer(query: str, context: str, max_tokens: int = 256, temperature: float = 0.2) -> str:
    """Generate a natural-language answer with enhanced security controls."""
    if not openai_client:
        return "AI service not configured."
    
    try:
        response = openai_client.chat.completions.create(
            model="gpt-4o-mini",
            messages=[
                {"role": "system", "content": "You are a helpful web content analyst."},
                {"role": "user", "content": f"Context:\n{context}\n\nQuestion: {query}\nAnswer:"}
            ],
            max_tokens=max_tokens,
            temperature=temperature
        )
        
        # Record successful request and token usage
        prompt_tokens = response.usage.prompt_tokens
        completion_tokens = response.usage.completion_tokens
        ai_service_monitor.record_request(prompt_tokens + completion_tokens)
        
        answer = response.choices[0].message.content.strip()
        return answer
    
    except Exception as e:
        # Record error for monitoring
        ai_service_monitor.record_error(str(e))
        # Safely handle error without revealing implementation details
        return "Sorry, I'm unable to generate an answer at this time. Please try again later."
```

### Compliance & Governance

**Score: 1/10 (CRITICAL)**

The application lacks fundamental AI governance and compliance measures:

#### Key Findings:

1. **No AI Ethics Framework**:
   - No defined ethical principles
   - No responsible AI guidelines
   - No fairness or bias mitigation

2. **Missing Regulatory Compliance**:
   - No evidence of compliance with AI regulations
   - No documentation of AI decision processes
   - No alignment with industry standards

3. **Absent Risk Assessment**:
   - No risk evaluation for AI components
   - No mitigation strategies for AI risks
   - No incident response plan

4. **No Governance Structure**:
   - No AI oversight mechanisms
   - No review process for AI changes
   - No accountability framework

#### Recommendations:

1. **Implement AI Ethics Framework**:

```python
# In app/governance/ethics.py
class AIEthicsConfig:
    """Configuration for AI ethics enforcement."""
    
    # Define allowed use cases
    ALLOWED_USE_CASES = [
        "information_retrieval",
        "content_summarization",
        "factual_question_answering"
    ]
    
    # Define prohibited use cases
    PROHIBITED_USE_CASES = [
        "harmful_content_generation",
        "deception",
        "impersonation",
        "discrimination"
    ]
    
    # Bias categories to monitor
    BIAS_CATEGORIES = [
        "gender",
        "race",
        "religion",
        "age",
        "disability",
        "political"
    ]
    
    @staticmethod
    def validate_use_case(use_case: str) -> bool:
        """Validate if a use case is allowed."""
        return use_case in AIEthicsConfig.ALLOWED_USE_CASES
```

2. **Add Decision Documentation**:

```python
# In app/services/llm_service.py - Add decision transparency
def generate_answer(query: str, context: str, max_tokens: int = 256, temperature: float = 0.2) -> str:
    # ...existing code...
    
    # Document the decision process
    decision_record = {
        "timestamp": datetime.now().isoformat(),
        "query": query,
        "context_length": len(context),
        "model": MODEL,
        "temperature": temperature,
        "max_tokens": max_tokens,
        "tokens_used": response.usage.total_tokens,
        "response_length": len(answer)
    }
    
    # Store decision record for compliance
    try:
        from app.services.metadata_store import store_decision_record
        store_decision_record(decision_record)
    except Exception:
        logger.exception("Failed to store decision record")
    
    return answer
```

3. **Create AI Risk Assessment Framework**:

```python
# In app/governance/risk.py
from enum import Enum
from typing import List, Dict, Any
import logging

logger = logging.getLogger(__name__)

class RiskLevel(Enum):
    LOW = "low"
    MEDIUM = "medium"
    HIGH = "high"
    CRITICAL = "critical"

class AIRiskAssessment:
    def __init__(self):
        self.risk_factors = {
            "data_sensitivity": RiskLevel.LOW,
            "decision_impact": RiskLevel.LOW,
            "automation_level": RiskLevel.LOW,
            "transparency": RiskLevel.MEDIUM,
            "user_vulnerability": RiskLevel.LOW
        }
    
    def assess_query_risk(self, query: str) -> Dict[str, Any]:
        """Assess the risk level of a specific query."""
        risk_profile = {
            "overall_risk": RiskLevel.LOW,
            "factors": {}
        }
        
        # Check for sensitive topics
        sensitive_topics = ["medical", "financial", "legal", "personal"]
        for topic in sensitive_topics:
            if topic in query.lower():
                risk_profile["factors"]["sensitive_topic"] = topic
                risk_profile["overall_risk"] = RiskLevel.MEDIUM
        
        # Check for potentially harmful intent
        harmful_patterns = ["how to harm", "how to hack", "bypass security"]
        for pattern in harmful_patterns:
            if pattern in query.lower():
                risk_profile["factors"]["harmful_intent"] = pattern
                risk_profile["overall_risk"] = RiskLevel.HIGH
                logger.warning("High risk query detected: %s", query)
        
        return risk_profile
```

4. **Implement AI Governance Structure**:

```python
# In app/governance/oversight.py
class AIGovernanceConfig:
    # Versioning for AI models
    MODEL_VERSIONS = {
        "production": "gpt-4o-mini",
        "development": "gpt-3.5-turbo",
        "fallback": "gpt-3.5-turbo"
    }
    
    # Environment-specific settings
    ENVIRONMENTS = {
        "production": {
            "monitoring_enabled": True,
            "audit_logging_enabled": True,
            "content_filtering_level": "strict"
        },
        "staging": {
            "monitoring_enabled": True,
            "audit_logging_enabled": True,
            "content_filtering_level": "medium"
        },
        "development": {
            "monitoring_enabled": False,
            "audit_logging_enabled": False,
            "content_filtering_level": "low"
        }
    }
    
    # Get current environment
    CURRENT_ENV = os.getenv("APP_ENV", "development")
    
    @staticmethod
    def get_model_for_environment() -> str:
        """Get the appropriate model for current environment."""
        env = AIGovernanceConfig.CURRENT_ENV
        if env in AIGovernanceConfig.MODEL_VERSIONS:
            return AIGovernanceConfig.MODEL_VERSIONS[env]
        return AIGovernanceConfig.MODEL_VERSIONS["fallback"]
    
    @staticmethod
    def get_environment_config() -> dict:
        """Get configuration for current environment."""
        env = AIGovernanceConfig.CURRENT_ENV
        if env in AIGovernanceConfig.ENVIRONMENTS:
            return AIGovernanceConfig.ENVIRONMENTS[env]
        return AIGovernanceConfig.ENVIRONMENTS["development"]
```

## Implementation Roadmap

Based on the security assessment, I recommend the following implementation roadmap:

### Phase 1: Critical Security Fixes (Immediate)

1. **Authentication & Authorization**:
   - Implement basic API key authentication for all endpoints
   - Add role-based access control (admin/user)
   - Secure API key management for OpenAI

2. **Input Validation & Sanitization**:
   - Add prompt injection detection
   - Implement input validation for all AI prompts
   - Add input boundaries and sanitization

3. **Basic Content Safety**:
   - Implement simple content filtering with regex patterns
   - Add PII detection and redaction
   - Implement basic error handling for unsafe content

### Phase 2: Enhanced Security (1-2 Weeks)

1. **Advanced AI Security**:
   - Implement rate limiting for API endpoints
   - Add circuit breaker pattern for external services
   - Enhance error handling and logging

2. **Content Moderation**:
   - Integrate OpenAI's moderation API
   - Implement bias detection mechanisms
   - Add content classification system

3. **Privacy Protection**:
   - Add data anonymization for external AI services
   - Implement data retention policies
   - Add user consent management

### Phase 3: Governance & Compliance (2-4 Weeks)

1. **AI Ethics Framework**:
   - Define ethical principles and guidelines
   - Implement use case validation
   - Add fairness and bias monitoring

2. **Regulatory Compliance**:
   - Add decision documentation
   - Implement transparency measures
   - Create compliance reporting

3. **Risk Management**:
   - Develop AI risk assessment framework
   - Implement mitigation strategies
   - Create incident response plans

## Conclusion

The Smart Knowledge Repository has critical AI security and safety vulnerabilities that require immediate attention. The application lacks fundamental protections against prompt injection, has no content moderation or safety filtering, and completely lacks authentication and authorization controls. These issues expose the system to potential misuse, data breaches, and compliance violations.

The most urgent concerns include the complete absence of access controls, lack of input sanitization, and missing content moderation. By implementing the recommended security measures following the phased implementation roadmap, the application can significantly improve its security posture and better protect user data, system integrity, and AI service usage.

**Key Security Metrics**:
- Input Security: 2/10 - Critical gaps in prompt injection prevention
- Content Safety: 1/10 - No content moderation or safety measures
- Data Protection: 2/10 - Minimal privacy protection for sensitive data
- Access Control: 1/10 - Complete absence of authentication and authorization
- Compliance: 1/10 - No AI governance or regulatory compliance measures

The overall security score of 2/10 (CRITICAL) indicates that this application should not be deployed in a production environment without implementing the recommended security improvements.

---

**Security Review conducted by:** GitHub Copilot  
**Review Date:** September 15, 2025  
**Project:** Smart Knowledge Repository  
**Version:** 1.0
