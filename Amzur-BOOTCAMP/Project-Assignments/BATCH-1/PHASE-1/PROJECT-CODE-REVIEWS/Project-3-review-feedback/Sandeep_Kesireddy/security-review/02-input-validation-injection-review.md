# Smart Knowledge Repository - Input Validation & Injection Security Review

## Executive Summary

This security review examines the input validation and injection prevention mechanisms in the Smart Knowledge Repository project. The assessment covers SQL injection, XSS vulnerabilities, command injection, and general input validation practices. Overall, the application demonstrates adequate protection against common injection attacks with some areas for improvement.

**Overall Input Validation Security Score: 7/10 (GOOD)**

The codebase implements several secure coding practices that help mitigate injection vulnerabilities, particularly through the use of Pydantic models for API validation and parameterized queries for database operations. However, there are gaps in internal validation, limited sanitization of user-provided content, and potential issues with file path handling that could be addressed to improve security.

### Key Strengths

- Consistent use of parameterized queries for SQLite operations
- Strong API request validation using Pydantic models with HttpUrl type
- No direct OS command execution that could lead to command injection
- Additional server-side validation for critical operations beyond Pydantic
- Careful management of OpenAI API responses to prevent potential issues

### Key Vulnerabilities

- Inconsistent internal function parameter validation
- Limited sanitization of scraped content before storage and display
- Potential path traversal risks in file operations
- No explicit output encoding for user-displayed content
- Limited validation of document IDs for access control

## Detailed Assessment

### SQL Injection Analysis

**Security Score: 8/10 (GOOD)**

#### Strengths

- Consistent use of parameterized queries across all database operations
- Proper use of query parameters with clear separation of code and data
- No evidence of string concatenation for SQL query construction
- Simple database schema limits the potential impact of injection attempts

#### Vulnerabilities

- No use of an ORM which could provide additional SQL injection protection
- Limited input validation before database operations in some internal functions

#### Code Examples

**Good Practice - Parameterized Queries:**

```python
# In metadata_store.py - Using parameterized queries
c.execute(
    "INSERT INTO chunk_metadata (document_id, chunk_text) VALUES (?, ?)", 
    (document_id, chunk_text)
)
```

```python
# In metadata_store.py - Safely querying with parameters
c.execute(
    "SELECT id, image_path, image_url, alt_text, mime_type, sha256, chunk_id FROM images WHERE document_id = ? ORDER BY id DESC", 
    (document_id,)
)
```

**Potentially Risky Code - Dynamic Query Construction:**

```python
# In metadata_store.py - Dynamic placeholder generation
placeholders = ",".join(["?"] * len(query_ids))
q = f"SELECT id, document_id, chunk_text FROM chunk_metadata WHERE id IN ({placeholders})"
c.execute(q, query_ids)
```

While this does use parameterized queries correctly, the dynamic nature of the placeholder generation could potentially be risky if not carefully managed. However, in this implementation, it appears to be done safely.

#### Recommendations

1. **Implement Input Validation Before Database Operations**

```python
# Add validation for document_id before database operations
def add_chunk_metadata(document_id: str, chunk_text: str) -> Optional[int]:
    # Validate document_id format
    if not isinstance(document_id, str) or not document_id:
        logger.warning("Invalid document_id provided: %s", document_id)
        return None
        
    # Validate chunk_text
    if not isinstance(chunk_text, str) or not chunk_text.strip():
        logger.warning("Empty or invalid chunk_text provided")
        return None
    
    # Continue with existing implementation
    # ...
```

2. **Consider adopting SQLAlchemy for additional SQL injection protection**

### Cross-Site Scripting (XSS) Prevention

**Security Score: 6/10 (ADEQUATE)**

#### Strengths

- Use of Streamlit for frontend rendering which provides some built-in XSS protection
- Limited direct rendering of user-provided content in the UI
- Streamlit automatically escapes markdown content to some extent

#### Vulnerabilities

- No explicit HTML sanitization for scraped content before storage
- Limited output encoding for user-displayed content
- LLM-generated responses displayed without sanitization
- Alt text from scraped images displayed without sanitization

#### Code Examples

**Potential XSS Risk:**

```python
# In frontend/app.py - Displaying images with potentially unsanitized alt text
for image in images:
    st.image(f"{api.BACKEND_URL}/{image['image_path']}", caption=image.get('alt_text', ''))
```

```python
# In frontend/app.py - Displaying scraped content without explicit sanitization
def display_documents():
    documents = api.get_scraped_documents()
    for doc in documents:
        with st.expander(f"📄 {doc['document_id']}", expanded=False):
            st.text(f"Scraped at: {doc['timestamp']}")
            st.text(f"Chunks: {doc['chunk_count']}")
```

#### Recommendations

1. **Implement HTML Sanitization for Scraped Content**

```python
# Add a sanitization function in services/scraper.py
from html.parser import HTMLParser

class MLStripper(HTMLParser):
    def __init__(self):
        super().__init__()
        self.reset()
        self.strict = False
        self.convert_charrefs= True
        self.text = []
    def handle_data(self, d):
        self.text.append(d)
    def get_data(self):
        return ''.join(self.text)

def sanitize_html(html_content):
    s = MLStripper()
    s.feed(html_content)
    return s.get_data()

# Use this in _fetch_and_parse method
def _fetch_and_parse(url: str):
    # ... existing code ...
    text = ' '.join(soup.stripped_strings)
    # Sanitize text to remove any remaining HTML
    text = sanitize_html(text)
    return soup, text
```

2. **Implement Output Encoding for User-Displayed Content**

```python
import html

def safe_display_text(text):
    # Escape HTML entities
    return html.escape(text)
    
# Use in frontend where displaying content
st.text(safe_display_text(doc['chunk_text']))
```

3. **Sanitize Alt Text Before Storage**

```python
# In scraper.py - Sanitize alt text before storage
alt_text = sanitize_html(alt_text)
```

### Command Injection Assessment

**Security Score: 9/10 (EXCELLENT)**

#### Strengths

- No direct use of system commands or shell execution
- No use of `os.system()`, `subprocess.call()`, or similar functions
- Well-defined file operations that don't rely on external commands

#### Vulnerabilities

- Limited validation of file paths which could potentially lead to path traversal issues

#### Code Examples

**Safe Practice - No Command Execution:**

The codebase doesn't contain any instances of command execution functions such as:
```python
os.system()
subprocess.call()
subprocess.Popen()
eval()
exec()
```

**Potential Path Traversal Risk:**

```python
# In main.py - Image serving without path validation
images_dir = os.path.join(os.path.dirname(__file__), 'db', 'images')
if not os.path.exists(images_dir):
    try:
        os.makedirs(images_dir, exist_ok=True)
    except Exception:
        pass
app.mount("/images", StaticFiles(directory=images_dir), name="images")
```

```python
# In scraper.py - Saving images without path validation
def _save_image_bytes(content: bytes, content_type: str) -> dict:
    # ...
    filename = f"{sha}.{ext}"
    path = os.path.join(IMAGES_DIR, filename)
    # No validation that filename doesn't contain path traversal sequences
    if not os.path.exists(path):
        try:
            with open(path, 'wb') as fh:
                fh.write(content)
        except Exception:
            logger.exception("Failed to write image to %s", path)
```

#### Recommendations

1. **Implement Path Validation for File Operations**

```python
def is_safe_path(basedir, path):
    # Ensure path doesn't escape the base directory using path traversal
    return os.path.abspath(os.path.join(basedir, path)).startswith(os.path.abspath(basedir))

def get_safe_image_path(basedir, image_name):
    if not is_safe_path(basedir, image_name):
        raise ValueError(f"Invalid image path: {image_name}")
    return os.path.join(basedir, image_name)
```

2. **Validate Filenames Before Writing**

```python
# Modified _save_image_bytes function
def _save_image_bytes(content: bytes, content_type: str) -> dict:
    # ...
    # Use just the hash without any user-provided input for filename
    sha = hashlib.sha256(content).hexdigest()
    # Only use sanitized extension
    ext = None
    if content_type:
        try:
            ext_raw = content_type.split('/')[-1].split(';')[0]
            # Only allow alphanumeric extensions
            ext = ''.join(c for c in ext_raw if c.isalnum())
        except Exception:
            ext = None
    if not ext:
        ext = 'bin'
    filename = f"{sha}.{ext}"
    # ...
```

### NoSQL Injection Analysis

**Security Score: N/A**

The application does not use NoSQL databases like MongoDB. It uses SQLite for structured data and FAISS for vector storage, which are not susceptible to NoSQL injection vulnerabilities in the traditional sense.

### Validation Security Review

**Security Score: 6/10 (ADEQUATE)**

#### Strengths

- Strong API input validation using Pydantic models
- Type hints used consistently throughout the codebase
- Extra server-side validation for critical operations
- Proper use of specialized types like HttpUrl for URL validation

#### Vulnerabilities

- Inconsistent validation in internal functions
- Limited validation of numerical boundaries
- No custom validators for complex business rules
- Limited sanitization of user queries

#### Code Examples

**Strong API Validation:**

```python
# In models/embedding.py - Using Pydantic for validation
class ScrapeAndEmbedRequest(BaseModel):
    url: HttpUrl  # Automatically validates URL format
    capture_images: bool = False

class RetrieveAndGenerateRequest(BaseModel):
    query: str
    top_k: int = 3
    document_ids: Optional[List[str]] = None
```

**Good Server-Side Validation:**

```python
# In endpoints.py - Additional URL validation
@router.post("/scrape_and_embed", response_model=ScrapeAndEmbedResponse)
def scrape_and_embed(data: ScrapeAndEmbedRequest) -> ScrapeAndEmbedResponse:
    # Extra server-side validation: ensure URL is reachable before scraping.
    try:
        parsed = urllib.parse.urlparse(str(data.url))
        if parsed.scheme not in ("http", "https") or not parsed.netloc:
            raise HTTPException(status_code=status.HTTP_422_UNPROCESSABLE_ENTITY, detail="Invalid URL scheme or host")
    except Exception:
        raise HTTPException(status_code=status.HTTP_422_UNPROCESSABLE_ENTITY, detail="Invalid URL")
```

**Missing Internal Validation:**

```python
# In embedding_service.py - Limited parameter validation
def retrieve_and_generate(self, query: str, top_k: int = 3, document_ids: Optional[List[str]] = None):
    # No validation if query is empty
    # No validation if top_k is negative or too large
    # No validation for document_ids format or content
```

```python
# In embedding_service.py - chunk_text without validation
def chunk_text(self, text: str, chunk_size: int = 500) -> List[str]:
    """Split text into chunks by words."""
    # No validation of chunk_size (could be negative or very large)
    # No validation of text (could be None or not a string)
    words = text.split()
    chunks = [' '.join(words[i:i + chunk_size]) for i in range(0, len(words), chunk_size)]
    return chunks
```

#### Recommendations

1. **Implement Consistent Internal Function Parameter Validation**

```python
# Utility function for parameter validation
def validate_param(value, param_name, required=False, min_val=None, max_val=None):
    if required and (value is None or (isinstance(value, str) and not value.strip())):
        raise ValueError(f"Parameter '{param_name}' is required")
        
    if min_val is not None and isinstance(value, (int, float)) and value < min_val:
        raise ValueError(f"Parameter '{param_name}' must be at least {min_val}")
        
    if max_val is not None and isinstance(value, (int, float)) and value > max_val:
        raise ValueError(f"Parameter '{param_name}' must not exceed {max_val}")
            
    return value

# Example usage in embedding_service.py
def retrieve_and_generate(self, query: str, top_k: int = 3, document_ids: Optional[List[str]] = None):
    validate_param(query, 'query', required=True)
    top_k = validate_param(top_k, 'top_k', min_val=1, max_val=20)
    # Rest of implementation...
```

2. **Add Custom Validators for Complex Business Rules**

```python
# In models/embedding.py - Adding custom validators
from pydantic import validator

class RetrieveAndGenerateRequest(BaseModel):
    query: str
    top_k: int = 3
    document_ids: Optional[List[str]] = None
    
    @validator('query')
    def query_not_empty(cls, v):
        if not v.strip():
            raise ValueError('Query string cannot be empty')
        return v
    
    @validator('top_k')
    def top_k_in_range(cls, v):
        if v < 1:
            raise ValueError('top_k must be at least 1')
        if v > 20:
            raise ValueError('top_k must not exceed 20')
        return v
        
    @validator('document_ids')
    def validate_document_ids(cls, v):
        if v is not None:
            if len(v) > 100:
                raise ValueError('Cannot specify more than 100 document IDs')
            if any(not isinstance(id, str) or not id for id in v):
                raise ValueError('All document IDs must be non-empty strings')
        return v
```

3. **Add validation for the chunk_text method**

```python
def chunk_text(self, text: str, chunk_size: int = 500) -> List[str]:
    """Split text into chunks by words.

    Args:
        text: Input document text to chunk.
        chunk_size: Approximate number of words per chunk.

    Returns:
        A list of chunk strings in the original order.
    """
    logger.info("[chunk_text] Enter: chunk_size=%d", chunk_size)
    
    # Validate inputs
    if not isinstance(text, str):
        logger.warning("Invalid text type provided to chunk_text: %s", type(text))
        return []
        
    if not isinstance(chunk_size, int) or chunk_size <= 0:
        logger.warning("Invalid chunk_size provided: %s", chunk_size)
        chunk_size = 500  # Use default value
    
    if chunk_size > 2000:
        logger.warning("Unusually large chunk_size provided: %s, capping at 2000", chunk_size)
        chunk_size = 2000  # Cap at reasonable maximum
        
    words = text.split()
    chunks = [' '.join(words[i:i + chunk_size]) for i in range(0, len(words), chunk_size)]
    logger.info("[chunk_text] Exit: Created %d chunks", len(chunks))
    return chunks
```

## Security Vulnerability Inventory

| Vulnerability Type | Severity | Description | Recommendation |
|-------------------|----------|-------------|----------------|
| SQL Injection | LOW | Parameterized queries provide protection but some inputs lack validation | Add input validation before database operations |
| XSS | MEDIUM | Limited sanitization of scraped content and user-displayed data | Implement HTML sanitization and output encoding |
| Path Traversal | LOW | Potential risk in file path handling | Add path validation for file operations |
| Input Validation | MEDIUM | Inconsistent validation in internal functions | Implement consistent parameter validation |
| Output Encoding | MEDIUM | Limited encoding of user-displayed content | Add explicit output encoding |
| LLM Output Injection | LOW | Potential risk from LLM-generated content being displayed to users | Add content filtering/sanitization for LLM responses |

## Conclusion

The Smart Knowledge Repository demonstrates good security practices against injection attacks, particularly through parameterized database queries and API input validation with Pydantic. However, there are several areas for improvement to enhance security, including more consistent internal validation, better sanitization of scraped content, and improved path validation for file operations.

The application's architecture inherently limits some risks - for example, using Streamlit provides some protection against XSS, and the absence of direct OS command execution eliminates command injection risks. However, adding defense-in-depth through consistent validation and sanitization would further strengthen the security posture.

## Remediation Priority

1. **HIGH**: Implement consistent internal function parameter validation
2. **MEDIUM**: Add HTML sanitization for scraped content
3. **MEDIUM**: Implement output encoding for user-displayed content
4. **LOW**: Add path validation for file operations
5. **LOW**: Consider adopting SQLAlchemy for additional SQL injection protection

Implementing these recommendations would significantly improve the application's resistance to injection attacks and ensure more robust input validation throughout the codebase.
