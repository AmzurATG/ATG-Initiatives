# Input Validation & Injection Security Review

## Project Information
- **Project Title:** Web Content Analyzer  
- **Name:** Havish Sai
- **Review Date:** August 7th, 2025
- **Reviewer:** Github Copilot


## Overview
This review focuses on the input validation practices and injection vulnerability prevention in the Web Content Analyzer application, which scrapes web content and optionally performs AI analysis.

**Input Validation Security Score: 8/10**

The application demonstrates strong input validation practices, particularly in preventing Server-Side Request Forgery (SSRF) attacks and HTML content sanitization. These security controls are critical for a web scraping application that processes untrusted external content.

## Input Handling Assessment

### Input Vectors Identified

1. **URL Input**: Primary user input for web scraping target
2. **Crawl Configuration Parameters**: Depth, max pages, domain constraints
3. **External Web Content**: HTML/XML content from scraped websites
4. **AI Analysis Configuration**: Parameters for OpenAI analysis

### URL Validation Security

**Score: 9/10**

The URL validation implementation is robust and security-focused, with strong SSRF prevention measures:

```python
# In src/utils/validators.py
def validate_url_public(url: str) -> None:
    try:
        parts = urlparse(url)
    except Exception:
        raise InvalidURLError("Invalid URL format")

    if parts.scheme not in _ALLOWED_SCHEMES:
        raise InvalidURLError("Only http/https allowed")
    if not parts.netloc or not HOST_REGEX.match(parts.hostname or ""):
        raise InvalidURLError("Malformed hostname")

    # DNS resolve and block private IPs (SSRF)
    try:
        infos = socket.getaddrinfo(parts.hostname, None)
    except socket.gaierror:
        raise InvalidURLError("Hostname could not be resolved")

    for family, _, _, _, sockaddr in infos:
        ip_str = sockaddr[0]
        try:
            ip = ipaddress.ip_address(ip_str)
        except ValueError:
            continue
        if any(ip in net for net in _PRIVATE_NETS):
            raise SSRFBlockedError("IP is in a private or link-local range")
```

**Security Strengths:**
- URL format validation with explicit error handling
- Scheme restriction to HTTP/HTTPS only
- Hostname pattern validation with regex
- DNS resolution to validate hostname existence
- Private IP range checking to prevent SSRF attacks
- Custom exception types for clear error reporting

**Security Improvements Needed:**
- Consider adding validation for internationalized domain names (IDNs)
- Implement time-based bans for repeated invalid URL attempts
- Add logging of blocked SSRF attempts for security monitoring

### API Parameter Validation

**Score: 7/10**

The application uses Pydantic for request model validation:

```python
# In src/models/data_models.py
class URLAnalysisRequest(BaseModel):
    url: str
    depth: int = 0               # 0 = single page, 1/2 = follow links
    same_domain_only: bool = True
    max_pages: int = 5 
    run_analysis: bool = False 
```

**Security Strengths:**
- Type validation for all parameters
- Default values for optional parameters
- Clear model definition for request structure

**Security Improvements Needed:**
- Add range validation for depth and max_pages parameters
- Implement more granular constraints on parameters
- Add field validation for URL format (leverage existing validator)

**Recommended Improvement:**
```python
class URLAnalysisRequest(BaseModel):
    url: str
    depth: int = Field(default=0, ge=0, le=2)               
    same_domain_only: bool = True
    max_pages: int = Field(default=5, ge=1, le=50)
    run_analysis: bool = False
    
    @validator('url')
    def validate_url_format(cls, v):
        # Basic URL format validation
        if not v:
            raise ValueError('URL cannot be empty')
        if not v.startswith(('http://', 'https://')):
            raise ValueError('URL must start with http:// or https://')
        return v
```

## Injection Vulnerability Assessment

### HTML Injection & XSS Prevention

**Score: 8/10**

The application employs robust HTML sanitization using the bleach library:

```python
# In src/utils/security.py
def sanitize_html_preview(html: str, max_len: int = 4000) -> str:
    cleaned = bleach.clean(html[:max_len], tags=[], attributes={}, strip=True)
    return cleaned
```

**Security Strengths:**
- Use of a well-established library (bleach) for HTML sanitization
- Removal of all HTML tags and attributes
- Length limitation to prevent excessive content processing
- Content is displayed in text areas rather than rendered as HTML

**Security Improvements Needed:**
- Implement context-specific sanitization for different usage scenarios
- Add sanitization metrics for monitoring and detection of sanitization failures
- Consider adding Content Security Policy headers in API responses

### Content-Type Validation

**Score: 8/10**

The application implements strong content type validation:

```python
# In src/scrapers/web_scraper.py
ALLOWED_CT_PREFIXES = (
    "text/html",
    "application/xhtml+xml",
    "application/rss+xml",
    "application/xml",
    "text/xml",
    "application/atom+xml",
)

# Later used for validation
ctype = (r.headers.get("content-type") or "text/html").split(";")[0].strip().lower()
accept = any(ctype.startswith(p) for p in ALLOWED_CT_PREFIXES) or ctype.endswith("+xml")
if not accept:
    raise ScrapeError(f"Unsupported content-type: {ctype}")
```

**Security Strengths:**
- Whitelist approach for allowed content types
- Proper content-type header parsing
- Explicit error for unsupported content types
- Prevention of binary file processing

**Security Improvements Needed:**
- Consider adding content sniffing prevention headers
- Implement more granular content validation for different XML formats
- Add file type detection beyond content-type headers

### Content Size Validation

**Score: 8/10**

The application enforces content size limits to prevent resource exhaustion:

```python
# In src/scrapers/web_scraper.py
MAX_BYTES = 2_500_000

# Used for validation
if len(content) > MAX_BYTES:
    raise TooLargeError(f"Response too large: {len(content)} bytes > {MAX_BYTES}")
```

**Security Strengths:**
- Explicit size limits for content processing
- Custom exception for clearer error reporting
- Size check before content processing to prevent DoS

**Security Improvements Needed:**
- Consider implementing progressive content processing for large files
- Add aggregate size tracking for multi-page crawling
- Implement more granular size limits for different content types

## Specific Injection Vulnerability Analysis

### SQL Injection Risk

**Score: N/A**

The application does not use a database or execute SQL queries, eliminating SQL injection risks.

### Command Injection Risk 

**Score: N/A**

No system commands or shell executions were identified in the codebase.

### XSS Vulnerability Risk

**Score: 7/10**

While the application sanitizes HTML content, there are some considerations:

**Current Protection:**
- HTML content sanitization with bleach
- Content displayed in text areas (not rendered as HTML)
- Content stored in memory only (not persisted)

**Potential Vulnerabilities:**
- HTML content in error messages might not be fully sanitized
- PDF report generation could potentially include unsanitized content
- Frontend might not properly escape content in all display contexts

**Recommended Improvement:**
```python
# Additional sanitization for error messages
@app.exception_handler(ScrapeError)
async def scrape_handler(_, exc: ScrapeError):
    # Sanitize error message content
    error_message = bleach.clean(str(exc), tags=[], attributes={}, strip=True)
    return JSONResponse(
        status_code=502, 
        content={"detail": f"Upstream fetch failed: {error_message}"}
    )
```

### OpenAI Prompt Injection Risk

**Score: 6/10**

The application sends web content to OpenAI for analysis, which creates prompt injection risks:

```python
# In src/services/analysis_service.py
def _build_user_prompt(text: str) -> str:
    # Note: we ask for short quotes as evidence; each quote <= 15 words to keep it extractive.
    schema = {
        # Schema definition...
    }

    rules = f"""
    RULES
    # Rules definition...
    """.strip()

    return f"""You will analyze the following webpage content and respond ONLY with JSON matching this schema:
    {json.dumps(schema, indent=2)}

    {rules}

    CONTENT START
    {text}
    CONTENT END
    """
```

**Security Concerns:**
- Web content is directly included in the prompt
- Potential for prompt injection attacks through crafted web content
- No explicit sanitization for prompt construction

**Recommended Improvement:**
```python
def _build_user_prompt(text: str) -> str:
    # Sanitize text for prompt safety
    sanitized_text = re.sub(r'```|</\w+>|<\w+>|"""', '', text)
    
    # Limit prompt size for safety
    max_safe_length = 50000
    if len(sanitized_text) > max_safe_length:
        sanitized_text = sanitized_text[:max_safe_length] + "\n[Content truncated for security reasons]"
    
    # Rest of the function...
    return f"""You will analyze the following webpage content and respond ONLY with JSON matching this schema:
    {json.dumps(schema, indent=2)}

    {rules}

    CONTENT START
    {sanitized_text}
    CONTENT END
    """
```

## Validation Implementation Quality

### Server-Side Validation Completeness

**Score: 8/10**

The backend implements thorough validation, particularly for URL inputs and content processing:

**Strengths:**
- Complete URL validation pipeline
- Content type validation
- Size limits enforcement
- Domain and scheme restrictions
- Pydantic model validation for API requests

**Improvements Needed:**
- Add more field-level validators in Pydantic models
- Implement request rate limiting
- Add validation for aggregated resource usage

### Client-Side Validation Security

**Score: 6/10**

The Streamlit frontend implements basic validation:

```python
# In frontend/app.py
ok, clean_or_final_url, err = preflight_validate(
    url, resolve_dns=True, check_tcp=False, check_http=deep_check, timeout_s=2.5
)
if not ok:
    st.error(err)
```

**Security Concerns:**
- Limited client-side validation with reliance on server-side checks
- No robust defense against UI manipulation
- Deep preflight check is optional and configurable by users

**Recommended Improvement:**
```python
# Enhance preflight validation
def preflight_validate(url, resolve_dns=True, check_tcp=True, check_http=True, timeout_s=2.5):
    # Add URL format validation
    if not url or not isinstance(url, str):
        return False, None, "URL cannot be empty"
        
    # Normalize URL
    clean = url.strip()
    if not clean.startswith(("http://", "https://")):
        clean = "https://" + clean
    
    # Validate URL format with regex
    url_pattern = re.compile(r'^https?://[a-zA-Z0-9][-a-zA-Z0-9.]*\.[a-zA-Z]{2,}(/.*)?$')
    if not url_pattern.match(clean):
        return False, None, "Invalid URL format"
    
    # Rest of the validation...
```

## Recommendations for Improved Input Validation

### 1. Enhance Pydantic Model Validation
**Priority: Medium**

Add more comprehensive field-level validation to Pydantic models:

```python
class URLAnalysisRequest(BaseModel):
    url: str
    depth: int = Field(default=0, ge=0, le=2, description="Crawl depth (0-2)")
    same_domain_only: bool = True
    max_pages: int = Field(default=5, ge=1, le=50, description="Maximum pages to crawl")
    run_analysis: bool = False
    
    @validator('url')
    def url_must_be_valid(cls, v):
        if not v:
            raise ValueError('URL cannot be empty')
        if not v.startswith(('http://', 'https://')):
            v = f'https://{v}'
        # Check URL format with regex
        url_pattern = re.compile(r'^https?://[a-zA-Z0-9][-a-zA-Z0-9.]*\.[a-zA-Z]{2,}(/.*)?$')
        if not url_pattern.match(v):
            raise ValueError("Invalid URL format")
        return v
```

### 2. Implement Rate Limiting and Resource Controls
**Priority: High**

Add rate limiting to prevent abuse:

```python
# Using slowapi for rate limiting
from slowapi import Limiter, _rate_limit_exceeded_handler
from slowapi.util import get_remote_address

limiter = Limiter(key_func=get_remote_address)

@router.post("/analyze", response_model=ScrapedContent)
@limiter.limit("5/minute")
async def analyze(req: URLAnalysisRequest) -> ScrapedContent:
    # Existing code...
```

### 3. Add Content Security Headers
**Priority: Medium**

Implement Content-Security-Policy headers:

```python
from fastapi.middleware.trustedhost import TrustedHostMiddleware
from starlette.middleware.cors import CORSMiddleware

# Add security headers middleware
@app.middleware("http")
async def add_security_headers(request, call_next):
    response = await call_next(request)
    response.headers["Content-Security-Policy"] = "default-src 'self'; script-src 'self'; frame-ancestors 'none'"
    response.headers["X-Content-Type-Options"] = "nosniff"
    response.headers["X-Frame-Options"] = "DENY"
    return response

# Update CORS policy to be more restrictive
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:8501"],  # Streamlit default port
    allow_credentials=False,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

### 4. Enhance OpenAI Prompt Security
**Priority: High**

Improve prompt security to prevent injection attacks:

```python
def safely_prepare_content_for_analysis(text: str) -> str:
    """
    Prepare content safely for OpenAI analysis by:
    1. Removing potential prompt injection markers
    2. Limiting content length
    3. Adding explicit content boundaries
    """
    # Remove potential injection markers
    sanitized = re.sub(r'(```|"""|CONTENT START|CONTENT END|<prompt>|</prompt>)', '', text)
    
    # Limit content length
    max_safe_length = 50000
    if len(sanitized) > max_safe_length:
        sanitized = sanitized[:max_safe_length] + "\n[Content truncated]"
    
    # Apply additional sanitization for prompt safety
    sanitized = sanitized.replace("```", "").replace("'''", "")
    
    return sanitized
```

### 5. Improve Content Type Validation
**Priority: Medium**

Enhance content type validation with deeper inspection:

```python
def validate_content(content_type: str, content: bytes) -> bool:
    """Validate content beyond just content-type header"""
    
    # Check allowed content type
    ct_lower = (content_type or "").lower().split(";")[0].strip()
    is_allowed = any(ct_lower.startswith(p) for p in ALLOWED_CT_PREFIXES)
    
    # Additional validation for HTML content
    if ct_lower.startswith("text/html") or ct_lower.startswith("application/xhtml"):
        # Check for HTML signature
        if not (content.startswith(b"<!DOCTYPE") or content.startswith(b"<html") or b"<body" in content[:500]):
            return False
    
    # Additional validation for XML content
    if "xml" in ct_lower:
        # Basic XML format check
        if not (content.startswith(b"<?xml") or content.startswith(b"<rss") or content.startswith(b"<feed")):
            return False
    
    return is_allowed
```

## Conclusion

The Web Content Analyzer application demonstrates strong input validation practices, particularly in URL validation and SSRF prevention - critical security controls for a web scraping application. The HTML sanitization implementation using bleach is also robust.

The main security improvements needed are:
1. More granular field validation in Pydantic models
2. Rate limiting and resource controls
3. Enhanced OpenAI prompt security
4. More comprehensive content validation
5. Security headers for browser protection

Overall, the application's input validation security is strong (8/10), with SSRF protection being a particular strength. Addressing the identified improvements would further enhance the security posture, especially for multi-user deployment scenarios.
