# Data Architecture Review: Web Content Analyzer

## Project Information
- **Project Title:** Web Content Analyzer  
- **Name:** Havish Sai
- **Review Date:** August 7th, 2025
- **Reviewer:** Github Copilot



## Project Information
Project Title: Web Content Analyzer
Name: Havish Sai
Review Date: Aug 7th 2025

## Overview
This assessment evaluates the data architecture of the Web Content Analyzer application, focusing on data models, data flow patterns, and data transformation approaches. The application is primarily focused on web content extraction and analysis rather than traditional database operations.

**Data Architecture Quality Score: 7/10**

The application demonstrates a well-structured data architecture for its domain, with clean data models, effective data transformation pipelines, and good separation of data concerns. While the application doesn't use a traditional database, it shows good practices in handling web content data and its transformations.

## Data Model Architecture

### Domain Data Model Design
**Score: 8/10**

The application uses Pydantic models to define its core data structures:

```python
# In src/models/data_models.py
class URLAnalysisRequest(BaseModel):
    url: str
    depth: int = 0
    same_domain_only: bool = True
    max_pages: int = 5 
    run_analysis: bool = False 

class NormalizedTexts(BaseModel):
    lower_ws_punct: Optional[str] = None
    lower_ws_punct_no_stopwords: Optional[str] = None

class ScrapedContent(BaseModel):
    url: AnyUrl
    title: Optional[str] = None
    meta: Dict[str, str] = {}
    headings: List[str] = []
    main_text: str = ""
    links: List[str] = []
    images: List[str] = []
    raw_html_preview: Optional[str] = Field(default=None, description="Sanitized preview")
    normalized: Optional[NormalizedTexts] = None
    analysis: Optional[AnalysisReport] = None
```

**Strengths:**
- Clear and semantically meaningful data models
- Appropriate use of Pydantic for validation and type safety
- Good default values for optional fields
- Proper nesting of related models (e.g., NormalizedTexts within ScrapedContent)
- Field descriptions where appropriate (e.g., raw_html_preview)
- Type annotations for all fields

**Improvement Opportunities:**
- Limited validation constraints on numeric fields (e.g., depth, max_pages)
- No custom validators for specific business rules
- Minimal documentation on field meanings and usage

### Analysis Data Models
**Score: 8/10**

The AnalysisReport model effectively represents AI analysis results:

```python
# In src/models/analysis_models.py
class Sentiment(BaseModel):
    label: Literal["positive", "neutral", "negative"]
    score: float = Field(..., ge=-1.0, le=1.0)
    rationale: str
    evidence: List[str] = Field(default_factory=list)

class Entity(BaseModel):
    text: str
    type: str  # PERSON, ORG, PRODUCT, LOCATION, DATE, OTHER

class AnalysisReport(BaseModel):
    summary: str
    key_points: List[str] = Field(default_factory=list)
    topics: List[str] = Field(default_factory=list)
    keywords: List[str] = Field(default_factory=list)
    sentiment: Sentiment
    entities: List[Entity] = Field(default_factory=list)
    word_count: int
    reading_grade: Optional[str] = None
```

**Strengths:**
- Well-structured hierarchy of analysis data
- Appropriate use of nested models (Sentiment, Entity)
- Good type constraints (e.g., Literal types for sentiment labels)
- Value range constraints (e.g., score between -1.0 and 1.0)
- Use of default_factory for empty collections
- Clear separation of different analysis components

**Improvement Opportunities:**
- Entity.type could use a Literal type for predefined categories
- Limited documentation on field meanings
- No versioning strategy for the analysis model
- Minimal validation beyond basic types

### Data Relationship Architecture
**Score: 7/10**

The application has a clear hierarchical data relationship structure:

```
ScrapedContent
├── Meta information (title, URL)
├── Content elements (headings, main_text, links, images)
├── NormalizedTexts (text processing results)
└── AnalysisReport (optional AI analysis)
    ├── Summary information (summary, key_points, topics, keywords)
    ├── Sentiment analysis
    └── Entity recognition
```

**Strengths:**
- Clear parent-child relationships between data models
- Appropriate nesting of related data
- Optional components clearly marked
- Good separation between raw content and derived analyses
- Consistent relationship patterns

**Improvement Opportunities:**
- No explicit relationship documentation
- Limited exploration of potential circular references
- No design patterns for handling collection relationships
- Missing domain relationship diagrams

## Data Access & Storage Architecture

### Data Retrieval Architecture
**Score: 7/10**

The application uses a specialized repository-like pattern for web content retrieval:

```python
# In src/scrapers/web_scraper.py
class WebScraper:
    def __init__(self):
        self._client = httpx.AsyncClient(
            timeout=TIMEOUT,
            follow_redirects=True,
            headers={"Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8"},
        )

    async def fetch(self, url: str) -> Tuple[str, bytes, str]:
        """
        Returns: (content_type, content_bytes, final_url)
        """
        # Implementation...

    async def aclose(self):
        await self._client.aclose()
```

**Strengths:**
- Repository-like pattern for external data access
- Proper resource management with aclose() method
- Async/await pattern for non-blocking I/O
- Appropriate error handling and retries
- Clear method signatures

**Improvement Opportunities:**
- Return types as tuple instead of domain object
- Limited abstraction of data source details
- No caching strategy for repeat retrievals
- Missing interface definition for potential alternate implementations

### External API Integration Architecture
**Score: 7/10**

The application integrates with OpenAI's API for content analysis:

```python
# In src/providers/llm_client.py
class LLMClient:
    def __init__(self):
        if not settings.OPENAI_API_KEY:
            raise RuntimeError("OPENAI_API_KEY not set")
        self.client = OpenAI(api_key=settings.OPENAI_API_KEY)

    def complete_json(self, system_prompt: str, user_prompt: str) -> Dict[str, Any]:
        r = self.client.chat.completions.create(
            model=settings.LLM_MODEL,
            temperature=settings.LLM_TEMPERATURE,
            messages=[
                {"role": "system", "content": system_prompt},
                {"role": "user", "content": user_prompt},
            ],
            response_format={"type": "json_object"},
            timeout=settings.LLM_TIMEOUT_S,
            max_tokens=settings.LLM_MAX_OUTPUT_TOKENS,
        )
        content = r.choices[0].message.content
        return json.loads(content)
```

**Strengths:**
- Clean abstraction of API details
- Proper error handling for missing credentials
- Configurable parameters through settings
- JSON response format specification for safer parsing
- Timeout configuration for resilience

**Improvement Opportunities:**
- Limited error handling for API responses
- No retries or circuit breaker pattern
- Missing response validation before parsing
- No caching strategy for repeated analyses

### Storage Architecture
**Score: N/A**

The application doesn't implement persistent storage:

**Architectural Decision Analysis:**
- Application is designed for stateless, on-demand processing
- No database storage needed for the current use case
- Results are returned directly to the client without persisting
- Appropriate design choice for the current requirements

**Future Storage Considerations:**
- Potential for adding caching of scraping results
- Possible user history storage for returning users
- Optional result persistence for analysis comparison
- Potential for distributed storage in a scaled deployment

## Data Processing Architecture

### Content Extraction Architecture
**Score: 8/10**

The application implements a robust extraction architecture:

```python
# In src/scrapers/content_extractor.py
def extract(
    blob: bytes,
    base_url: Optional[str] = None,
    content_type: Optional[str] = None,
) -> Tuple[str, Dict[str, str], List[str], str, List[str], List[str]]:
    """
    Unified extractor: HTML or RSS/Atom based on content_type (or auto-detect).
    Returns: (title, meta, headings, main_text, links, images)
    """
    ctype = (content_type or "").lower()

    # Heuristic: treat as feed if content-type says xml/rss/atom OR if it looks like one
    if any(ctype.startswith(p) for p in ("application/rss+xml", "application/xml", "text/xml", "application/atom+xml")):
        return _extract_from_rss(blob, base_url)

    # Auto-detect RSS/Atom by root tags if content-type missing/misleading
    sniff = blob[:2000].lower()
    if b"<rss" in sniff or b"<feed" in sniff or b"<entry" in sniff:
        return _extract_from_rss(blob, base_url)

    # Default: HTML
    return _extract_from_html(blob, base_url)
```

**Strengths:**
- Strategy pattern for different content types (HTML vs RSS)
- Content-type detection with fallbacks
- Clean function signatures with clear documentation
- Separation of extraction logic by content type
- URL resolution for relative links
- Good error handling and fallbacks

**Improvement Opportunities:**
- Return as tuples rather than domain objects
- Limited content type support beyond HTML and RSS
- No extension mechanism for additional content types
- Limited documentation on extraction heuristics

### Text Processing Architecture
**Score: 9/10**

The application has a sophisticated text processing pipeline:

```python
# In src/processors/text_normalizer.py
def normalize_pipeline(
    text: str,
    stopwords: Set[str] | None = None,
    remove_sw: bool = False,
) -> Tuple[str, str | None]:
    """
    Returns (normalized_with_stopwords, normalized_without_stopwords or None)
    Steps: lowercase -> strip punctuation -> normalize whitespace -> (optional) remove stopwords
    """
    if not text:
        return "", "" if remove_sw else None

    t = to_lower(text)
    t = strip_punctuation(t)
    t = normalize_whitespace(t)

    if remove_sw and stopwords is not None:
        t_no_sw = remove_stopwords(t, stopwords)
        return t, t_no_sw
    return t, None
```

**Strengths:**
- Clean pipeline architecture with transformation steps
- Function composition for text processing operations
- Clear single-responsibility utility functions
- Configurable processing options
- Good documentation of transformation steps
- Separation of different normalization concerns

**Improvement Opportunities:**
- Limited language support (primarily English)
- No extension points for additional processing steps
- Limited text analytics beyond basic normalization
- No pipeline configuration via external settings

### Content Analysis Architecture
**Score: 8/10**

The application implements a well-designed AI analysis architecture:

```python
# In src/services/analysis_service.py
class AnalysisService:
    def __init__(self):
        self.enabled = bool(settings.ENABLE_LLM_ANALYSIS and settings.OPENAI_API_KEY)
        
    def run(self, main_text: str) -> AnalysisReport | None:
        if not self.enabled or not main_text:
            return None

        budget = settings.LLM_MAX_INPUT_TOKENS
        text_for_llm = chunk_text_to_token_limit(main_text, settings.LLM_MODEL, budget)

        client = LLMClient()
        user_prompt = _build_user_prompt(text_for_llm)
        raw = client.complete_json(SYSTEM_PROMPT, user_prompt)

        # Deterministic fields (computed locally)
        word_count = len(main_text.split())
        reading_grade = _fkgl(main_text)

        # Minimal sanitation / defaults
        # ... result processing ...

        return AnalysisReport(**raw)
```

**Strengths:**
- Feature toggle pattern for enabling/disabling analysis
- Text chunking to manage token limits
- Local computation for deterministic fields
- Result validation and sanitization
- Clean separation of concerns (prompt building, API call, processing)
- Good error handling and fallback strategies

**Improvement Opportunities:**
- Limited analysis result validation
- No caching of analysis results
- Limited error handling for API failures
- Missing logging of analysis performance
- No alternative analysis providers

## Data Transformation & Flow Architecture

### Data Transformation Pipeline
**Score: 8/10**

The application implements a clear data transformation pipeline:

```
Raw HTML/XML
  ↓
Content Extraction (title, meta, headings, main_text, links, images)
  ↓
Text Normalization (lowercase, punctuation removal, whitespace normalization)
  ↓
Optional Stopword Removal
  ↓
Optional AI Analysis
  ↓
Response Assembly (ScrapedContent with optional normalized text and analysis)
```

**Strengths:**
- Clean, unidirectional data flow
- Clear transformation steps with single responsibilities
- Optional processing paths based on configuration
- Good intermediate data structures
- Type safety throughout the pipeline

**Improvement Opportunities:**
- Limited parallelization of independent transformations
- No explicit pipeline architecture pattern
- Transformation monitoring/metrics missing
- Limited pipeline configuration options

### Concurrent Data Processing Architecture
**Score: 7/10**

The application uses async patterns for concurrent web scraping:

```python
# In src/services/scraping_service.py
sem = asyncio.Semaphore(CONCURRENCY)

async def fetch_one(target: str):
    async with sem:
        return await ws.fetch(target)

tasks = [asyncio.create_task(fetch_one(link)) for link, _ in batch]
for (link, d), task in zip(batch, tasks):
    try:
        ctype2, content2, f2 = await task
    except Exception:
        continue
    
    # Process fetched content...
```

**Strengths:**
- Semaphore for controlling concurrency
- Task creation for parallel processing
- Proper async/await patterns
- Error handling for individual task failures
- Resource limiting to prevent overloading

**Improvement Opportunities:**
- Hardcoded concurrency limits
- Limited monitoring of concurrent operations
- No adaptive concurrency based on system load
- Basic error handling without specificity

### Data Aggregation Architecture
**Score: 8/10**

The application implements a data aggregation pattern for combining content from multiple pages:

```python
# In src/services/scraping_service.py
# Aggregate: concatenate texts, merge headings/links/images (dedupe, preserve order)
def merge_preserve(primary: List[str], many: List[List[str]]) -> List[str]:
    out, seen = [], set()
    for x in primary + [y for sub in many for y in sub]:
        if x and x not in seen:
            seen.add(x)
            out.append(x)
    return out

root.main_text = "\n\n---\n\n".join([t for t in [root.main_text] + [r.main_text for r in results] if t])
root.headings = merge_preserve(root.headings, [r.headings for r in results])
root.links = merge_preserve(root.links, [r.links for r in results])
root.images = merge_preserve(root.images, [r.images for r in results])
```

**Strengths:**
- Effective deduplication while preserving order
- Clear separator for concatenated text
- Smart handling of different content types
- Reusable aggregation function
- Handling of empty values

**Improvement Opportunities:**
- Limited configuration of aggregation strategies
- No weighting or priority for different sources
- Basic text concatenation without semantic merging
- Limited documentation of aggregation logic

## Data Validation & Integrity Architecture

### Input Validation Architecture
**Score: 7/10**

The application implements input validation through Pydantic and custom validators:

```python
# In src/models/data_models.py
class URLAnalysisRequest(BaseModel):
    url: str
    depth: int = 0
    same_domain_only: bool = True
    max_pages: int = 5 
    run_analysis: bool = False 

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
    
    # Additional validation...
```

**Strengths:**
- Type validation through Pydantic models
- Custom validation for security-critical inputs
- Specific error messages for validation failures
- Security-focused URL validation
- Good separation between basic and advanced validation

**Improvement Opportunities:**
- Limited range validation for numeric parameters
- No custom validators in Pydantic models
- Missing validation for some edge cases
- Limited documentation on validation rules

### Content Validation & Sanitization
**Score: 9/10**

The application implements strong content validation and sanitization:

```python
# In src/utils/security.py
def sanitize_html_preview(html: str, max_len: int = 4000) -> str:
    cleaned = bleach.clean(html[:max_len], tags=[], attributes={}, strip=True)
    return cleaned

# In src/scrapers/web_scraper.py
if len(content) > MAX_BYTES:
    raise TooLargeError(f"Response too large: {len(content)} bytes > {MAX_BYTES}")

ctype = (r.headers.get("content-type") or "text/html").split(";")[0].strip().lower()
accept = any(ctype.startswith(p) for p in ALLOWED_CT_PREFIXES) or ctype.endswith("+xml")
if not accept:
    raise ScrapeError(f"Unsupported content-type: {ctype}")
```

**Strengths:**
- HTML sanitization using bleach library
- Content size limits to prevent DOS
- Content-type validation for security
- URL validation to prevent SSRF
- Clear validation errors with context
- Resource constraints for reliability

**Improvement Opportunities:**
- Some hardcoded limits instead of configuration
- Limited content-type options beyond HTML/XML
- Basic content structure validation

### Error Handling Architecture
**Score: 8/10**

The application has a comprehensive error handling architecture:

```python
# In src/utils/exceptions.py
class InvalidURLError(ValueError):
    pass

class SSRFBlockedError(PermissionError):
    pass

class TooLargeError(RuntimeError):
    pass

class ScrapeError(RuntimeError):
    pass

# In src/api/middleware.py
def add_exception_handlers(app: FastAPI) -> None:
    @app.exception_handler(RequestValidationError)
    async def request_validation_handler(_, exc: RequestValidationError):
        return JSONResponse(
            status_code=422,
            content={"detail": exc.errors(), "hint": 'Body must be JSON: {"url":"https://..."}'}
        )

    # Additional handlers...
```

**Strengths:**
- Domain-specific exception types
- Centralized exception handling
- Appropriate HTTP status codes
- Helpful error messages
- Clean separation of error handling concerns
- Good error context preservation

**Improvement Opportunities:**
- Some error details could leak sensitive information
- Limited logging of errors
- No global exception handler for unexpected errors
- Missing error categorization for analytics

## Data Caching & Performance Architecture

### Caching Strategy
**Score: 4/10**

The application has limited caching implementation:

**Current Caching:**
- No HTTP response caching
- No computation result caching
- No intermediate result caching
- Session state in Streamlit frontend for UI state

**Improvement Opportunities:**
- Implement HTTP response caching for repeated URLs
- Cache analysis results for the same content
- Add memoization for expensive processing functions
- Implement TTL-based cache invalidation
- Add distributed caching for scaled deployment

### Token Management Architecture
**Score: 8/10**

The application implements efficient token management for AI analysis:

```python
# In src/utils/chunking.py
def chunk_text_to_token_limit(text: str, model: str, max_tokens: int) -> str:
    """
    Returns a prefix of `text` that fits within `max_tokens` tokens for the given model.
    """
    if not text:
        return ""
    enc = tiktoken.encoding_for_model(model) if model in tiktoken.list_encoding_names() else tiktoken.get_encoding("cl100k_base")
    tokens = enc.encode(text)
    if len(tokens) <= max_tokens:
        return text
    trimmed = enc.decode(tokens[:max_tokens])
    return trimmed
```

**Strengths:**
- Model-specific token encoding
- Efficient token counting
- Graceful truncation of excessive text
- Fallback encoding for unknown models
- Clean function interface

**Improvement Opportunities:**
- No token usage tracking or analytics
- No adaptive token budgeting based on content
- Missing semantic chunking strategy
- Limited token preservation strategies

### Performance Optimization Architecture
**Score: 6/10**

The application implements several performance optimizations:

**Strengths:**
- Concurrent HTTP requests for web scraping
- Content size limits to prevent memory issues
- Resource constraints for predictable performance
- Timeout configuration to prevent hanging requests
- Configuration-driven performance settings

**Improvement Opportunities:**
- No performance monitoring or metrics
- Limited caching for repeat operations
- Basic resource pooling
- No adaptive resource allocation
- Missing performance testing infrastructure

## Data Architecture Recommendations

### 1. Implement Caching Architecture
**Priority: HIGH**

Add a caching layer for improved performance:

```python
# Create in src/services/cache_service.py
from typing import TypeVar, Dict, Optional, Any, Callable
import time
import functools

T = TypeVar('T')

class CacheService:
    """Cache service for storing computed results with TTL"""
    
    def __init__(self, default_ttl: int = 3600):
        self._cache: Dict[str, Dict[str, Any]] = {}
        self._default_ttl = default_ttl
    
    def get(self, key: str) -> Optional[Any]:
        """Get value from cache if it exists and is not expired"""
        if key not in self._cache:
            return None
            
        entry = self._cache[key]
        if time.time() > entry["expires_at"]:
            del self._cache[key]
            return None
            
        return entry["value"]
    
    def set(self, key: str, value: Any, ttl: Optional[int] = None) -> None:
        """Store value in cache with expiration"""
        expiration = time.time() + (ttl if ttl is not None else self._default_ttl)
        self._cache[key] = {
            "value": value,
            "expires_at": expiration
        }
    
    def clear(self) -> None:
        """Clear all cached values"""
        self._cache.clear()

# Cache decorator
def cached(ttl: Optional[int] = None):
    """Decorator for caching function results"""
    cache_service = CacheService()
    
    def decorator(func: Callable[..., T]) -> Callable[..., T]:
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            # Create cache key from function name and arguments
            key_parts = [func.__name__]
            key_parts.extend(str(arg) for arg in args)
            key_parts.extend(f"{k}={v}" for k, v in sorted(kwargs.items()))
            cache_key = ":".join(key_parts)
            
            # Check cache
            cached_result = cache_service.get(cache_key)
            if cached_result is not None:
                return cached_result
                
            # Calculate result and cache it
            result = func(*args, **kwargs)
            cache_service.set(cache_key, result, ttl)
            return result
        return wrapper
    return decorator
```

### 2. Improve Data Model Validation
**Priority: MEDIUM**

Enhance Pydantic models with more comprehensive validation:

```python
# Update src/models/data_models.py
from pydantic import BaseModel, AnyUrl, Field, validator
from typing import Dict, List, Optional
from .analysis_models import AnalysisReport

class URLAnalysisRequest(BaseModel):
    url: str
    depth: int = Field(default=0, ge=0, le=3, description="Crawl depth (0-3)")
    same_domain_only: bool = True
    max_pages: int = Field(default=5, ge=1, le=50, description="Maximum pages to crawl (1-50)")
    run_analysis: bool = False
    
    @validator('url')
    def validate_url_format(cls, v):
        """Validate URL format"""
        if not v or not isinstance(v, str):
            raise ValueError("URL must be a non-empty string")
        
        v = v.strip()
        if not v:
            raise ValueError("URL cannot be empty")
            
        # Add http:// or https:// prefix if missing
        if not v.startswith(('http://', 'https://')):
            v = f"https://{v}"
            
        return v
```

### 3. Implement Repository Pattern
**Priority: MEDIUM**

Refactor data access with a cleaner repository pattern:

```python
# Create src/repositories/web_content_repository.py
from typing import Protocol, Optional, Dict, List, Tuple, AsyncIterator
from urllib.parse import urlparse

from ..utils.exceptions import InvalidURLError, SSRFBlockedError, TooLargeError, ScrapeError
from ..models.data_models import ScrapedContent
from ..scrapers.web_scraper import WebScraper
from ..scrapers.content_extractor import extract

class WebContentRepository(Protocol):
    """Repository interface for retrieving web content"""
    
    async def get_content(self, url: str) -> ScrapedContent:
        """Retrieve content from a single URL"""
        ...
    
    async def get_linked_content(self, url: str, depth: int, same_domain_only: bool, 
                                max_pages: int) -> List[ScrapedContent]:
        """Retrieve content from linked pages"""
        ...
    
    async def close(self) -> None:
        """Close repository and release resources"""
        ...

class HttpWebContentRepository:
    """HTTP implementation of web content repository"""
    
    def __init__(self):
        self._scraper = WebScraper()
    
    async def get_content(self, url: str) -> ScrapedContent:
        """Retrieve content from a single URL"""
        try:
            content_type, content_bytes, final_url = await self._scraper.fetch(url)
            title, meta, headings, main_text, links, images = extract(
                content_bytes, base_url=final_url, content_type=content_type
            )
            
            return ScrapedContent(
                url=final_url,
                title=title,
                meta=meta,
                headings=headings,
                main_text=main_text,
                links=links,
                images=images,
                raw_html_preview=None  # Don't include preview by default
            )
        except Exception as e:
            # Rethrow as repository exceptions or create new exception types
            raise
    
    async def get_linked_content(self, url: str, depth: int, same_domain_only: bool, 
                                max_pages: int) -> List[ScrapedContent]:
        """Retrieve content from linked pages"""
        # Implementation...
    
    async def close(self) -> None:
        """Close repository and release resources"""
        await self._scraper.aclose()
```

### 4. Create a Data Transformation Pipeline
**Priority: MEDIUM**

Implement a more explicit data transformation pipeline:

```python
# Create src/processors/pipeline.py
from typing import Protocol, TypeVar, Generic, List, Callable, Any

T = TypeVar('T')
U = TypeVar('U')

class Processor(Protocol, Generic[T, U]):
    """Interface for data processors in a pipeline"""
    
    def process(self, data: T) -> U:
        """Process input data and return transformed data"""
        ...

class ProcessingPipeline(Generic[T, U]):
    """Pipeline for data transformations"""
    
    def __init__(self, processors: List[Processor]):
        self._processors = processors
    
    def process(self, data: Any) -> Any:
        """Run data through the processing pipeline"""
        result = data
        for processor in self._processors:
            result = processor.process(result)
        return result

# Example text processing pipeline implementation
class TextNormalizationProcessor:
    """Text normalization processor"""
    
    def process(self, text: str) -> str:
        """Normalize text by converting to lowercase and standardizing whitespace"""
        if not text:
            return ""
        return " ".join(text.lower().split())

class PunctuationRemovalProcessor:
    """Punctuation removal processor"""
    
    def __init__(self, keep_chars: str = ""):
        self._keep_chars = set(keep_chars)
    
    def process(self, text: str) -> str:
        """Remove punctuation from text"""
        if not text:
            return ""
        return "".join(c if c.isalnum() or c.isspace() or c in self._keep_chars else " " for c in text)

class StopwordRemovalProcessor:
    """Stopword removal processor"""
    
    def __init__(self, stopwords: set):
        self._stopwords = stopwords
    
    def process(self, text: str) -> str:
        """Remove stopwords from text"""
        if not text:
            return ""
        return " ".join(word for word in text.split() if word not in self._stopwords)

# Usage example:
# from src.processors.stopwords_en import STOPWORDS_EN
# 
# # Create text normalization pipeline
# text_pipeline = ProcessingPipeline([
#     TextNormalizationProcessor(),
#     PunctuationRemovalProcessor(),
#     StopwordRemovalProcessor(STOPWORDS_EN)
# ])
# 
# normalized_text = text_pipeline.process(original_text)
```

### 5. Implement a Data Transfer Object Pattern
**Priority: LOW**

Add DTOs for API communication:

```python
# Create src/models/dtos.py
from pydantic import BaseModel, Field, validator
from typing import Dict, List, Optional, Any

class UrlAnalysisRequestDto(BaseModel):
    """Data transfer object for URL analysis requests"""
    url: str
    depth: int = Field(default=0, ge=0, le=3)
    same_domain_only: bool = True
    max_pages: int = Field(default=5, ge=1, le=50)
    run_analysis: bool = False

class ScrapedContentDto(BaseModel):
    """Data transfer object for scraped content responses"""
    url: str
    title: Optional[str] = None
    meta: Dict[str, str] = {}
    headings: List[str] = []
    main_text: str = ""
    links: List[str] = []
    images: List[str] = []
    analysis: Optional[Dict[str, Any]] = None
    
    class Config:
        schema_extra = {
            "example": {
                "url": "https://example.com",
                "title": "Example Domain",
                "meta": {"description": "Example website"},
                "headings": ["Example Domain", "More Information"],
                "main_text": "This domain is for use in illustrative examples in documents.",
                "links": ["https://www.iana.org/domains/example"],
                "images": []
            }
        }
```

### 6. Add Results Caching
**Priority: HIGH**

Implement caching for scraped content results:

```python
# Create src/services/scraping_cache_service.py
import hashlib
import json
import time
from typing import Dict, Optional, Any

class ScrapingCacheService:
    """Cache service for storing scraped content results"""
    
    def __init__(self, max_entries: int = 100, ttl_seconds: int = 3600):
        self._cache: Dict[str, Dict[str, Any]] = {}
        self._max_entries = max_entries
        self._ttl_seconds = ttl_seconds
    
    def _compute_key(self, url: str, depth: int, same_domain_only: bool, max_pages: int) -> str:
        """Compute cache key from request parameters"""
        key_dict = {
            "url": url,
            "depth": depth,
            "same_domain_only": same_domain_only,
            "max_pages": max_pages
        }
        key_json = json.dumps(key_dict, sort_keys=True)
        return hashlib.md5(key_json.encode()).hexdigest()
    
    def get(self, url: str, depth: int, same_domain_only: bool, max_pages: int) -> Optional[Any]:
        """Get cached result if available and not expired"""
        key = self._compute_key(url, depth, same_domain_only, max_pages)
        
        if key not in self._cache:
            return None
            
        entry = self._cache[key]
        if time.time() > entry["expires_at"]:
            del self._cache[key]
            return None
            
        return entry["value"]
    
    def set(self, url: str, depth: int, same_domain_only: bool, max_pages: int, value: Any) -> None:
        """Store scraped content in cache"""
        key = self._compute_key(url, depth, same_domain_only, max_pages)
        
        # Manage cache size
        if len(self._cache) >= self._max_entries:
            # Remove oldest entry
            oldest_key = min(self._cache.items(), key=lambda x: x[1]["expires_at"])[0]
            del self._cache[oldest_key]
        
        # Store new entry
        self._cache[key] = {
            "value": value,
            "expires_at": time.time() + self._ttl_seconds,
            "created_at": time.time()
        }
    
    def clear(self) -> None:
        """Clear all cached entries"""
        self._cache.clear()

# Usage in ScrapingService:
# self._cache = ScrapingCacheService()
# 
# cached_result = self._cache.get(url, depth, same_domain_only, max_pages)
# if cached_result:
#     return cached_result
# 
# # Fetch and process content
# result = ...
# 
# self._cache.set(url, depth, same_domain_only, max_pages, result)
# return result
```

### 7. Improve Data Metrics & Analytics
**Priority: LOW**

Add data metrics collection for content processing:

```python
# Create src/services/metrics_service.py
from typing import Dict, List, Any, Optional
import time
import statistics

class MetricsService:
    """Service for collecting and reporting metrics"""
    
    def __init__(self):
        self._request_counts: Dict[str, int] = {}
        self._processing_times: Dict[str, List[float]] = {}
        self._content_sizes: Dict[str, List[int]] = {}
        self._error_counts: Dict[str, int] = {}
        
    def record_request(self, endpoint: str) -> None:
        """Record an API request"""
        self._request_counts[endpoint] = self._request_counts.get(endpoint, 0) + 1
        
    def start_timer(self) -> float:
        """Start a timing operation and return start time"""
        return time.time()
        
    def record_processing_time(self, operation: str, start_time: float) -> float:
        """Record processing time for an operation"""
        elapsed = time.time() - start_time
        if operation not in self._processing_times:
            self._processing_times[operation] = []
        self._processing_times[operation].append(elapsed)
        return elapsed
        
    def record_content_size(self, content_type: str, size: int) -> None:
        """Record size of processed content"""
        if content_type not in self._content_sizes:
            self._content_sizes[content_type] = []
        self._content_sizes[content_type].append(size)
        
    def record_error(self, error_type: str) -> None:
        """Record an error occurrence"""
        self._error_counts[error_type] = self._error_counts.get(error_type, 0) + 1
        
    def get_metrics(self) -> Dict[str, Any]:
        """Get all collected metrics"""
        metrics = {
            "requests": self._request_counts,
            "errors": self._error_counts,
            "content_sizes": {},
            "processing_times": {}
        }
        
        # Calculate statistics for content sizes
        for content_type, sizes in self._content_sizes.items():
            if sizes:
                metrics["content_sizes"][content_type] = {
                    "count": len(sizes),
                    "min": min(sizes),
                    "max": max(sizes),
                    "avg": statistics.mean(sizes),
                    "median": statistics.median(sizes),
                    "total": sum(sizes)
                }
                
        # Calculate statistics for processing times
        for operation, times in self._processing_times.items():
            if times:
                metrics["processing_times"][operation] = {
                    "count": len(times),
                    "min": min(times),
                    "max": max(times),
                    "avg": statistics.mean(times),
                    "median": statistics.median(times),
                    "p95": statistics.quantiles(times, n=20)[18] if len(times) >= 20 else None
                }
                
        return metrics
```

## Conclusion

The Web Content Analyzer application demonstrates a well-designed data architecture for its specific domain of web content extraction and analysis. The use of Pydantic models provides clean data structures with type safety, while the text processing pipeline shows good separation of concerns for data transformation.

The application's strengths lie in its clear data models, effective content extraction strategies, and strong text processing capabilities. The use of async patterns for concurrent data retrieval is also noteworthy, allowing for efficient processing of multiple linked pages.

The main areas for improvement are in caching and data persistence, as the application currently has no mechanism for storing results between runs. Adding a caching layer would significantly improve performance for repeated requests. Additionally, implementing a cleaner repository pattern and more explicit data transformation pipeline would enhance maintainability and extensibility.

Overall, the data architecture is well-suited to the application's requirements, balancing performance with maintainability. The recommendations provided would further enhance the architecture's robustness and performance characteristics while maintaining its clean design.

**Data Architecture Quality Score: 7/10**
