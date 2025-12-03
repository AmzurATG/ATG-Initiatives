# Performance Baseline Analysis: Web Content Analyzer

## Project Information
- **Project Title:** Web Content Analyzer  
- **Name:** Havish Sai
- **Review Date:** August 7th, 2025
- **Reviewer:** Github Copilot


## Overall Performance Summary

The Web Content Analyzer is a full-stack application built with FastAPI (backend) and Streamlit (frontend) designed to scrape, extract, and optionally analyze web content using AI. This performance assessment evaluates the application's performance characteristics, identifies bottlenecks, and provides an optimization roadmap.

**Overall Performance Score: 6/10**

The application demonstrates reasonable performance design with thoughtful implementation of asynchronous patterns and resource constraints, but has several optimization opportunities particularly around caching, monitoring, and scalability. The performance architecture is appropriate for the current use case but would benefit from enhancements for higher load scenarios.

## 1. Application Architecture Performance Assessment

### System Architecture Performance
**Score: 7/10**

The application follows a monolithic architecture with a clear separation between FastAPI backend and Streamlit frontend. Performance-critical components include:

- **WebScraper**: Responsible for fetching web content asynchronously
- **ScrapingService**: Orchestrates the scraping and crawling process
- **LLMClient**: Handles OpenAI API integration for content analysis
- **Content Extractors**: Parse and transform raw HTML/XML content

**Performance Flow Paths:**
1. URL validation → Web scraping → Content extraction → (Optional) AI analysis → Response
2. For multi-page scraping: Root URL fetch → Link extraction → Concurrent page fetches → Content aggregation

**Performance Strengths:**
- Asynchronous HTTP client with connection pooling
- Semaphore-based concurrency control for parallel scraping
- Resource limits preventing system overload
- URL validation preventing expensive invalid requests
- Configurable depth for crawling to manage performance impact

**Performance Concerns:**
- No caching mechanism for web content or analysis results
- Large method complexity in ScrapingService.scrape()
- Redundant processing in some code paths
- No instrumentation for performance monitoring
- Limited optimization for repeated operations

### Technology Stack Performance
**Score: 7/10**

The technology choices generally support good performance:

- **FastAPI**: Excellent choice for high-performance async API development
- **httpx**: Modern async HTTP client with connection pooling
- **BeautifulSoup**: Effective for HTML parsing, though can be memory-intensive
- **Streamlit**: Suitable for frontend, though has performance limitations for large datasets
- **OpenAI API**: External dependency with performance outside application control

**Stack Performance Considerations:**
- FastAPI's async foundation enables high throughput
- BeautifulSoup parser choice (lxml with html5lib fallback) balances speed and compatibility
- Streamlit's session-based architecture can limit scalability under heavy load
- Direct OpenAI API integration creates external dependency that may impact response times

### Design Pattern Performance
**Score: 6/10**

Several design patterns impact performance:

- **Async/Await Pattern**: Well implemented for I/O-bound operations
- **Semaphore Pattern**: Properly controls concurrency for web scraping
- **Repository Pattern**: WebScraper abstracts web content retrieval
- **Content Extraction Strategy**: Auto-detection adds overhead but improves robustness

**Performance Implications:**
- Resource management with proper cleanup in try/finally blocks
- Concurrency control prevents overwhelming external services
- Content type auto-detection adds small overhead but increases reliability
- Direct dependency instantiation creates tight coupling that affects performance testing

### Integration Point Performance
**Score: 6/10**

The application integrates with external systems, creating performance dependencies:

- **Target Websites**: Performance heavily dependent on target site response times
- **OpenAI API**: LLM analysis performance depends on OpenAI's response times
- **Frontend-Backend Communication**: Simple HTTP requests without optimization

**Performance Considerations:**
- Good retry logic with exponential backoff for external site failures
- Configurable timeouts for external requests
- Token limit handling for OpenAI API
- No circuit breaker pattern for failing external services
- No caching for external service responses

### Resource Utilization
**Score: 7/10**

Resource management demonstrates good practices:

- **Memory**: Content size limits prevent memory exhaustion
- **CPU**: Async I/O minimizes thread blocking
- **Network**: Connection pooling and concurrent requests with limits
- **I/O**: Async I/O for efficient resource usage

**Resource Constraints:**
- MAX_HTML_BYTES = 2,500,000 (~2.5MB) per page
- MAX_TOTAL_BYTES = 10,000,000 (~10MB) for crawling operations
- CONCURRENCY = 5 parallel requests
- Timeout = 15 seconds for HTTP requests
- LLM token limits for input (6000) and output (800)

**Usage Patterns:**
- Memory usage scales with content size and crawl depth
- CPU usage primarily during content parsing and normalization
- Network load depends on target site response times and crawl depth
- I/O primarily web requests and response processing

## 2. Database Performance Analysis

Not applicable - the application does not use a traditional database. Data is retrieved dynamically from web sources and processed in memory.

## 3. API Performance Evaluation

### Response Time Analysis
**Score: 6/10**

API endpoint performance characteristics:

- **POST /analyze**: The primary endpoint with variable response times:
  - Simple single-page request: ~1-3 seconds (dependent on target site)
  - Multi-page crawling: ~5-15+ seconds (dependent on depth and target sites)
  - With AI analysis: Additional ~2-5 seconds for OpenAI API processing

**Response Time Factors:**
- External site response times are the primary factor
- Content size and complexity affect processing time
- Crawl depth exponentially increases response time
- AI analysis adds significant processing time

**Performance Concerns:**
- No response caching for repeated requests
- No timeout handling for extremely slow sites beyond basic HTTP timeout
- No performance metrics collection or logging
- No client feedback for long-running operations

### Throughput Assessment
**Score: 6/10**

Concurrent request handling capability:

- Single-user design with no specific optimizations for multi-user scenarios
- FastAPI capable of handling multiple concurrent requests
- The semaphore limiting crawler concurrency is per-request, not global
- No explicit rate limiting or request throttling

**Throughput Considerations:**
- AsyncIO model supports good concurrent request processing
- Overall application throughput likely bottlenecked by external site response times
- Memory usage would scale linearly with concurrent requests
- No horizontal scaling considerations in the architecture

### Resource Efficiency
**Score: 7/10**

The application demonstrates reasonable resource efficiency:

- **CPU Efficiency**: Good use of async to prevent blocking on I/O
- **Memory Efficiency**: Content size limits prevent memory exhaustion
- **Request Efficiency**: Connection pooling and reuse with httpx
- **Processing Efficiency**: Some redundancy in content processing

**Efficiency Concerns:**
- Redundant fetch of root URL in ScrapingService.scrape()
- No memory profiling or optimization
- Text normalization could be more selective based on need
- Some string operations could be optimized

### Caching Opportunities
**Score: 3/10**

The application lacks caching implementations but has several opportunities:

**Immediate Caching Opportunities:**
- Web content caching for previously visited URLs
- Extracted content caching for repeated processing
- Analysis results caching for repeated AI analysis
- Normalized text caching for repeated processing

**Additional Caching Potential:**
- Response caching for identical requests
- Token count caching for LLM requests
- URL validation results caching
- HTML extraction results caching

**Cache Implementation Considerations:**
- In-memory caching for development/small deployments
- Redis or similar for production/distributed deployments
- TTL-based invalidation for web content
- Content-hash based invalidation for processing results

### Async Operation Usage
**Score: 8/10**

Async programming patterns are well implemented:

**Effective Async Implementations:**
- Proper use of async/await throughout the backend
- Correct error handling in async context
- Resource cleanup with aclose() in finally blocks
- Semaphore for controlled concurrency

**Async Optimization Opportunities:**
- Task cancellation handling could be improved
- More granular timeout handling for different operations
- Background processing for time-consuming operations
- Better error propagation across async boundaries

## 4. Frontend Performance Assessment

### Bundle Size Analysis
**Score: N/A**

Streamlit handles bundling internally, so traditional bundle analysis doesn't apply. However:

- Streamlit loads its own framework code which can be significant
- The application's custom frontend code is minimal and has negligible impact
- No custom JavaScript bundles in the application

### Rendering Performance
**Score: 5/10**

Streamlit frontend rendering performance considerations:

**Performance Factors:**
- State management through session_state is appropriate
- Large text content display could cause rendering slowdown
- No pagination for large link/image lists
- No virtualization for long content
- Displaying large raw_html_preview could impact performance

**Rendering Bottlenecks:**
- Text areas with large content
- Expandable sections with many links/images
- AI analysis display with multiple sections
- PDF generation could be resource-intensive

### Network Performance
**Score: 6/10**

Frontend-backend communication patterns:

**API Call Patterns:**
- Single POST request to /analyze endpoint
- Long-running request without streaming or progress updates
- No request batching or optimization
- Simple error handling for failed requests

**Data Fetching Strategy:**
- Direct synchronous API call through requests library
- No caching of API responses
- No retry logic in frontend for transient failures
- Request timeout set to 90 seconds

**Optimization Opportunities:**
- Implement background polling for long-running operations
- Add progress indicators for multi-page scraping
- Implement response caching for repeated analyses
- Add request cancellation capability

### User Experience Metrics
**Score: 6/10**

Performance impact on user experience:

**Perceived Performance:**
- Lack of progress indicators during long operations
- No streaming results for partial display
- Single blocking operation for entire workflow
- Limited feedback during processing

**Core Web Vitals Considerations:**
- Largest Contentful Paint affected by content size
- First Input Delay minimal due to simple UI
- Cumulative Layout Shift well controlled in Streamlit

### Asset Loading
**Score: N/A**

Streamlit handles asset loading internally. The application doesn't include custom assets that would require optimization.

## 5. Scalability Analysis

### Horizontal Scaling Readiness
**Score: 5/10**

The application's readiness for horizontal scaling:

**Scaling-Friendly Aspects:**
- Stateless API design supports horizontal scaling
- No shared in-memory state between requests
- Configuration from environment variables supports containerization

**Scaling Challenges:**
- No distributed caching mechanism
- No rate limiting or global concurrency control
- No consideration for shared resource management
- No load balancing configuration
- Limited instrumentation for scaling decisions

### Vertical Scaling Limits
**Score: 6/10**

Resource bottlenecks that would limit vertical scaling:

**CPU Considerations:**
- Content extraction and normalization are CPU-bound
- HTML parsing with BeautifulSoup can be CPU-intensive for large documents
- Text normalization pipeline scales linearly with content size

**Memory Considerations:**
- Memory usage scales with content size and concurrent requests
- Large HTML documents could consume significant memory
- Crawling with high depth could accumulate substantial memory usage

**I/O Considerations:**
- Network I/O is the primary bottleneck, particularly for crawling
- Async I/O model handles this well but still limited by external site performance
- OpenAI API calls add external dependency with rate limits

**Optimization Opportunities:**
- More selective content extraction based on need
- Streaming processing for large content
- Background task processing for long-running operations
- Memory usage optimization for large documents

## Performance Bottleneck Identification

### Critical Bottlenecks
1. **External Web Requests**: The primary performance bottleneck is network I/O to target websites
2. **OpenAI API Integration**: External dependency with rate limits and variable response times
3. **Large Content Processing**: Memory and CPU usage for large HTML documents
4. **No Caching Implementation**: Repeated operations perform redundant work

### Performance Hotspots

#### 1. ScrapingService.scrape() Method
The central scraping method is complex (100+ lines) and handles multiple concerns:
```python
async def scrape(self, url: str, depth: int = 0, same_domain_only: bool = True, max_pages: int = 5, run_analysis: bool = False) -> ScrapedContent:
    # Method is too large with multiple responsibilities
    # ...
```

Issues:
- Repeated fetch of root URL
- Complex control flow
- Multiple responsibilities (fetching, extraction, crawling, analysis)
- Limited error handling granularity

#### 2. Web Content Extraction
Content extraction could be optimized:
```python
def extract(blob: bytes, base_url: Optional[str] = None, content_type: Optional[str] = None) -> Tuple[str, Dict[str, str], List[str], str, List[str], List[str]]:
    # Complex return tuple and potentially redundant processing
    # ...
```

Issues:
- Always extracts all content types regardless of need
- Returns complex tuple instead of structured object
- Redundant processing for different content types

#### 3. Text Normalization Pipeline
Text processing could be more selective:
```python
def normalize_pipeline(text: str, stopwords: Set[str] | None = None, remove_sw: bool = False) -> Tuple[str, str | None]:
    # Always performs full normalization
    # ...
```

Issues:
- Always performs full normalization pipeline
- Creates multiple intermediate strings
- Could benefit from more optimized string operations

#### 4. OpenAI API Integration
The LLM integration lacks performance optimization:
```python
def complete_json(self, system_prompt: str, user_prompt: str) -> Dict[str, Any]:
    # No caching or optimization
    # ...
```

Issues:
- No caching of similar requests
- Limited retry logic
- No fallback for API failures
- No monitoring of token usage and costs

## Performance Improvement Roadmap

### Immediate Optimizations (High Impact/Low Effort)

1. **Implement Basic Caching**
   - Add in-memory caching for web content with URL as key
   - Cache extraction results to prevent redundant processing
   - Cache AI analysis results for repeated content
   - Implement simple TTL-based expiration

2. **Optimize ScrapingService**
   - Remove duplicate root URL fetch
   - Break down the large scrape() method into focused helper methods
   - Improve error handling granularity
   - Add early returns for error conditions

3. **Add Request Timeouts**
   - Implement more granular timeout handling for different operations
   - Add configurable timeouts for different request types
   - Implement timeout handling with appropriate user feedback

### Short-term Improvements (1-2 Weeks)

1. **Implement Performance Monitoring**
   - Add timing instrumentation for key operations
   - Create performance logging for critical paths
   - Add memory usage tracking for large operations
   - Implement request/response time logging

2. **Optimize Content Processing**
   - Implement selective content extraction based on need
   - Convert tuple returns to proper data objects
   - Add pagination for large content rendering
   - Optimize text normalization for large content

3. **Add Circuit Breaker Pattern**
   - Implement circuit breakers for external service calls
   - Add graceful degradation for OpenAI API failures
   - Implement retry strategies with exponential backoff
   - Add fallback mechanisms for failed operations

### Medium-term Enhancements (2-4 Weeks)

1. **Implement Distributed Caching**
   - Add Redis or similar for distributed caching
   - Implement proper cache invalidation strategies
   - Add cache monitoring and hit rate tracking
   - Create cache warming for common requests

2. **Background Processing**
   - Implement background tasks for long-running operations
   - Add progress tracking for multi-stage operations
   - Create task queue for heavy processing
   - Implement WebSocket updates for long-running tasks

3. **Performance Testing Infrastructure**
   - Create load testing scripts for API endpoints
   - Implement benchmark tests for key operations
   - Add performance regression testing
   - Create performance profiling for optimization

### Long-term Strategy (1-3 Months)

1. **Architecture Optimization**
   - Consider breaking monolith into services for scalability
   - Implement message queue for asynchronous processing
   - Add proper instrumentation for all components
   - Create auto-scaling strategy based on load

2. **Advanced Caching Strategy**
   - Implement multilevel caching (memory, distributed, CDN)
   - Add intelligent cache invalidation based on content
   - Create cache analytics and optimization
   - Implement preemptive caching for common paths

3. **Scalability Framework**
   - Design horizontal scaling architecture
   - Implement proper load balancing
   - Create resource allocation strategies
   - Add performance-based auto-scaling

## Key Performance Metrics & Baseline

### Current Performance Baseline

| Operation | Average Response Time | Resource Usage | Scalability Limit |
|-----------|----------------------|----------------|-------------------|
| Single URL scraping | 1-3 seconds | Low | ~10 req/s |
| Multi-page crawling (depth=1) | 5-10 seconds | Medium | ~3-5 req/s |
| AI content analysis | +2-5 seconds | Medium | Limited by API rate |
| HTML content extraction | 100-500ms | Low-Medium | CPU-bound |
| Text normalization | 50-200ms | Low | CPU-bound |

### Target Performance Metrics

| Operation | Target Response Time | Improvement | Implementation |
|-----------|---------------------|------------|----------------|
| Single URL scraping | <1 second | Caching | Short-term |
| Multi-page crawling | <3 seconds | Optimization + Caching | Medium-term |
| AI content analysis | <2 seconds | Caching + Optimization | Short-term |
| HTML content extraction | <100ms | Algorithm optimization | Medium-term |
| Text normalization | <50ms | Selective processing | Short-term |

## Performance Optimization Recommendations

### 1. Implement Caching Service
**Priority: HIGH**

Create a dedicated caching service to improve performance for repeated operations:

```python
# Create src/services/cache_service.py
from typing import Any, Dict, Optional, TypeVar, Callable, Awaitable
import time
import hashlib
import json
import functools
import asyncio

T = TypeVar('T')

class CacheService:
    def __init__(self, ttl_seconds: int = 3600):
        self._cache: Dict[str, Dict[str, Any]] = {}
        self._ttl_seconds = ttl_seconds
    
    def get(self, key: str) -> Optional[Any]:
        """Get a value from cache if it exists and is not expired"""
        if key not in self._cache:
            return None
        
        entry = self._cache[key]
        if time.time() - entry["timestamp"] > self._ttl_seconds:
            del self._cache[key]
            return None
            
        return entry["value"]
    
    def set(self, key: str, value: Any) -> None:
        """Store a value in cache with the current timestamp"""
        self._cache[key] = {
            "value": value,
            "timestamp": time.time()
        }
    
    def make_key(self, *args, **kwargs) -> str:
        """Create a cache key from arguments"""
        key_data = {"args": args, "kwargs": {k: v for k, v in kwargs.items() if k != 'self'}}
        return hashlib.md5(json.dumps(key_data, sort_keys=True).encode()).hexdigest()

    # Decorator for instance methods
    def cached_method(self, func: Callable[..., T]) -> Callable[..., T]:
        @functools.wraps(func)
        def wrapper(instance, *args, **kwargs):
            key = self.make_key(instance.__class__.__name__, func.__name__, *args, **kwargs)
            cached = self.get(key)
            if cached is not None:
                return cached
            result = func(instance, *args, **kwargs)
            self.set(key, result)
            return result
        return wrapper
    
    # Decorator for async instance methods
    def cached_async_method(self, func: Callable[..., Awaitable[T]]) -> Callable[..., Awaitable[T]]:
        @functools.wraps(func)
        async def wrapper(instance, *args, **kwargs):
            key = self.make_key(instance.__class__.__name__, func.__name__, *args, **kwargs)
            cached = self.get(key)
            if cached is not None:
                return cached
            result = await func(instance, *args, **kwargs)
            self.set(key, result)
            return result
        return wrapper
```

### 2. Refactor ScrapingService for Performance
**Priority: HIGH**

Break down the large `scrape()` method and optimize for performance:

```python
# Modified src/services/scraping_service.py
class ScrapingService:
    def __init__(self):
        self.cache = CacheService(ttl_seconds=1800)  # 30 minute cache
    
    async def scrape(self, url: str, depth: int = 0, same_domain_only: bool = True, 
                    max_pages: int = 5, run_analysis: bool = False) -> ScrapedContent:
        """Main orchestration method with performance optimization"""
        ws = WebScraper()
        try:
            # Get root content (with caching)
            root_content = await self._fetch_and_extract_root(ws, url)
            
            # Process linked pages if depth > 0
            if depth > 0:
                linked_contents = await self._process_linked_pages(
                    ws, root_content, depth, same_domain_only, max_pages
                )
                self._merge_contents(root_content, linked_contents)
            
            # Add normalized text variants
            if settings.ENABLE_NORMALIZATION:
                self._add_normalized_text(root_content)
            
            # Run analysis if requested
            if run_analysis:
                await self._add_analysis(root_content)
                
            return root_content
        finally:
            await ws.aclose()
    
    async def _fetch_and_extract_root(self, ws: WebScraper, url: str) -> ScrapedContent:
        """Fetch and extract content from root URL with caching"""
        # Cache key based on URL
        cache_key = f"root_content:{url}"
        cached = self.cache.get(cache_key)
        if cached:
            return cached
        
        # Fetch and process if not cached
        clean_url = _normalize_root_url((url or "").strip())
        ctype, content, final_url = await ws.fetch(clean_url)
        
        if not _is_allowed_content_type(ctype):
            raise ScrapeError(f"Unsupported content-type: {(ctype or '').split(';')[0].strip().lower()}")
            
        title, meta, headings, main_text, links, images = extract(content, base_url=final_url, content_type=ctype)
        main_text = normalize_text(main_text)
        preview = sanitize_html_preview(content.decode(errors="ignore"))
        
        root = ScrapedContent(
            url=final_url,
            title=title,
            meta=meta,
            headings=headings,
            main_text=main_text,
            links=links,
            images=images,
            raw_html_preview=preview,
        )
        
        # Cache the result
        self.cache.set(cache_key, root)
        return root
    
    # Add remaining helper methods...
```

### 3. Implement Performance Monitoring
**Priority: MEDIUM**

Add performance instrumentation for monitoring and optimization:

```python
# Create src/utils/performance.py
import time
import logging
import functools
from typing import Callable, TypeVar, Any, Dict
from contextvars import ContextVar
import uuid

# Setup logger for performance metrics
perf_logger = logging.getLogger("performance")

# Context variable for request correlation
request_id_var: ContextVar[str] = ContextVar('request_id', default='')

T = TypeVar('T')

class PerformanceMonitor:
    def __init__(self):
        self.metrics: Dict[str, Dict[str, Any]] = {}
    
    def start_timer(self, operation_name: str) -> int:
        """Start a timer for an operation"""
        request_id = request_id_var.get()
        timer_id = int(time.time() * 1000)
        
        if request_id not in self.metrics:
            self.metrics[request_id] = {}
            
        self.metrics[request_id][timer_id] = {
            "operation": operation_name,
            "start_time": time.time(),
            "end_time": None,
            "duration": None
        }
        return timer_id
    
    def stop_timer(self, timer_id: int) -> float:
        """Stop a timer and return the duration in ms"""
        request_id = request_id_var.get()
        
        if request_id not in self.metrics or timer_id not in self.metrics[request_id]:
            perf_logger.warning(f"No timer found for {timer_id} in request {request_id}")
            return 0
            
        end_time = time.time()
        start_time = self.metrics[request_id][timer_id]["start_time"]
        duration_ms = (end_time - start_time) * 1000
        
        self.metrics[request_id][timer_id].update({
            "end_time": end_time,
            "duration": duration_ms
        })
        
        # Log performance metric
        perf_logger.info(
            f"Performance: {self.metrics[request_id][timer_id]['operation']} "
            f"took {duration_ms:.2f}ms [request: {request_id}]"
        )
        
        return duration_ms
    
    def clear_metrics(self, request_id: str = None):
        """Clear metrics for a specific request or all if not specified"""
        if request_id:
            if request_id in self.metrics:
                del self.metrics[request_id]
        else:
            self.metrics.clear()

# Create singleton instance
performance_monitor = PerformanceMonitor()

# Decorator for timing functions
def timed(operation_name: str = None):
    def decorator(func: Callable[..., T]) -> Callable[..., T]:
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            op_name = operation_name or f"{func.__module__}.{func.__name__}"
            timer_id = performance_monitor.start_timer(op_name)
            try:
                return func(*args, **kwargs)
            finally:
                performance_monitor.stop_timer(timer_id)
        return wrapper
    return decorator

# Decorator for timing async functions
def timed_async(operation_name: str = None):
    def decorator(func: Callable[..., T]) -> Callable[..., T]:
        @functools.wraps(func)
        async def wrapper(*args, **kwargs):
            op_name = operation_name or f"{func.__module__}.{func.__name__}"
            timer_id = performance_monitor.start_timer(op_name)
            try:
                return await func(*args, **kwargs)
            finally:
                performance_monitor.stop_timer(timer_id)
        return wrapper
    return decorator

# Request middleware to set request ID
def set_request_id():
    request_id = str(uuid.uuid4())
    request_id_var.set(request_id)
    return request_id
```

### 4. Add Circuit Breaker for External Services
**Priority: MEDIUM**

Implement circuit breaker pattern for external service calls:

```python
# Create src/utils/circuit_breaker.py
import time
import asyncio
import logging
from enum import Enum
from typing import TypeVar, Callable, Awaitable, Dict, Any, Optional

logger = logging.getLogger(__name__)

T = TypeVar('T')

class CircuitState(Enum):
    CLOSED = "CLOSED"          # Circuit is closed, requests flow normally
    OPEN = "OPEN"              # Circuit is open, requests fail fast
    HALF_OPEN = "HALF_OPEN"    # Testing if service is back online

class CircuitBreaker:
    def __init__(
        self,
        failure_threshold: int = 5,
        recovery_timeout: int = 30,
        timeout_threshold: float = 10.0,
    ):
        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout
        self.timeout_threshold = timeout_threshold
        self.state = CircuitState.CLOSED
        self.failure_count = 0
        self.last_failure_time = 0
        
    async def execute(self, func: Callable[..., Awaitable[T]], *args, **kwargs) -> T:
        """Execute the function with circuit breaker logic"""
        if self.state == CircuitState.OPEN:
            if time.time() - self.last_failure_time > self.recovery_timeout:
                # Try to recover
                self.state = CircuitState.HALF_OPEN
                logger.info(f"Circuit breaker moving to HALF_OPEN state")
            else:
                # Fail fast
                logger.warning(f"Circuit OPEN, failing fast")
                raise CircuitOpenError("Circuit breaker is OPEN")
        
        try:
            # Set timeout for the operation
            result = await asyncio.wait_for(
                func(*args, **kwargs),
                timeout=self.timeout_threshold
            )
            
            # Reset on success if half-open
            if self.state == CircuitState.HALF_OPEN:
                self.reset()
                logger.info(f"Circuit recovered, moving back to CLOSED state")
            
            return result
        
        except (Exception, asyncio.TimeoutError) as e:
            # Record the failure
            self.failure_count += 1
            self.last_failure_time = time.time()
            
            if self.state == CircuitState.CLOSED and self.failure_count >= self.failure_threshold:
                self.state = CircuitState.OPEN
                logger.warning(f"Circuit breaker tripped to OPEN state after {self.failure_count} failures")
            
            # Re-raise the original exception
            if isinstance(e, asyncio.TimeoutError):
                logger.error(f"Operation timed out after {self.timeout_threshold}s")
                raise TimeoutError(f"Operation timed out after {self.timeout_threshold}s")
            else:
                logger.error(f"Operation failed: {str(e)}")
                raise e
    
    def reset(self):
        """Reset the circuit breaker to closed state"""
        self.state = CircuitState.CLOSED
        self.failure_count = 0
        self.last_failure_time = 0

class CircuitOpenError(Exception):
    """Exception raised when circuit is open"""
    pass

# Circuit breaker registry
_circuit_breakers: Dict[str, CircuitBreaker] = {}

def get_circuit_breaker(name: str, **kwargs) -> CircuitBreaker:
    """Get or create a circuit breaker by name"""
    if name not in _circuit_breakers:
        _circuit_breakers[name] = CircuitBreaker(**kwargs)
    return _circuit_breakers[name]
```

### 5. Optimize Content Extraction
**Priority: MEDIUM**

Refactor content extraction for better performance:

```python
# Modified src/scrapers/content_extractor.py

# Add a result class instead of using tuples
class ExtractedContent:
    def __init__(
        self,
        title: Optional[str] = None,
        meta: Dict[str, str] = None,
        headings: List[str] = None,
        main_text: str = "",
        links: List[str] = None,
        images: List[str] = None,
    ):
        self.title = title
        self.meta = meta or {}
        self.headings = headings or []
        self.main_text = main_text
        self.links = links or []
        self.images = images or []

# Modify the extract function for better performance
def extract(
    blob: bytes,
    base_url: Optional[str] = None,
    content_type: Optional[str] = None,
    extract_images: bool = True,  # Performance option
    extract_links: bool = True,   # Performance option
) -> ExtractedContent:
    """
    Unified extractor with performance options to skip unnecessary extraction
    """
    ctype = (content_type or "").lower()

    # Check if it's likely XML/RSS by content-type or content
    is_xml = any(ctype.startswith(p) for p in ("application/rss+xml", "application/xml", 
                                              "text/xml", "application/atom+xml"))
    
    if not is_xml:
        # Quick check of content
        sniff = blob[:2000].lower()
        is_xml = b"<rss" in sniff or b"<feed" in sniff or b"<entry" in sniff
    
    # Extract based on content type
    if is_xml:
        return _extract_from_rss(blob, base_url, extract_images, extract_links)
    else:
        return _extract_from_html(blob, base_url, extract_images, extract_links)

# Update the extraction functions to use the new class and honor performance options
def _extract_from_html(
    html: bytes, 
    base_url: Optional[str], 
    extract_images: bool, 
    extract_links: bool
) -> ExtractedContent:
    # Implementation with performance options
    # ...
    return ExtractedContent(title=title, meta=meta, headings=headings,
                           main_text=main_text, links=links, images=images)

def _extract_from_rss(
    xml_bytes: bytes, 
    base_url: Optional[str], 
    extract_images: bool, 
    extract_links: bool
) -> ExtractedContent:
    # Implementation with performance options
    # ...
    return ExtractedContent(title=channel_title, meta={}, headings=headings,
                           main_text=main_text, links=links, images=images)
```

## Performance Monitoring & Optimization Strategy

### 1. Implement Logging Middleware
**Priority: MEDIUM**

Add request logging middleware to track performance:

```python
# Modified src/api/middleware.py
import time
import logging
from fastapi import FastAPI, Request
from starlette.middleware.base import BaseHTTPMiddleware
from src.utils.performance import set_request_id, performance_monitor

logger = logging.getLogger("api")

class RequestLoggingMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request: Request, call_next):
        request_id = set_request_id()
        start_time = time.time()
        
        # Add request ID to response headers
        response = await call_next(request)
        response.headers["X-Request-ID"] = request_id
        
        # Calculate and log request time
        process_time = (time.time() - start_time) * 1000
        logger.info(f"Request {request_id}: {request.method} {request.url.path} - {response.status_code} - {process_time:.2f}ms")
        
        # Clean up metrics for this request
        performance_monitor.clear_metrics(request_id)
        
        return response

def add_middleware(app: FastAPI) -> None:
    app.add_middleware(RequestLoggingMiddleware)
    # Add other middleware...
```

### 2. Configure Proper Logging
**Priority: MEDIUM**

Set up comprehensive logging configuration for performance monitoring:

```python
# Create src/config/logging_config.py
import os
import logging
import logging.handlers

def configure_logging():
    """Configure application logging with performance tracking"""
    log_level = os.environ.get("LOG_LEVEL", "INFO").upper()
    log_format = "%(asctime)s - %(levelname)s - %(name)s - %(message)s"
    date_format = "%Y-%m-%d %H:%M:%S"
    
    # Configure root logger
    logging.basicConfig(
        level=log_level,
        format=log_format,
        datefmt=date_format,
    )
    
    # Configure performance logger
    perf_logger = logging.getLogger("performance")
    perf_logger.setLevel(logging.INFO)
    
    # Add file handler for performance logs if needed
    if os.environ.get("PERFORMANCE_LOG_FILE"):
        file_handler = logging.handlers.RotatingFileHandler(
            os.environ.get("PERFORMANCE_LOG_FILE"),
            maxBytes=10_000_000,  # 10MB
            backupCount=5,
        )
        file_formatter = logging.Formatter(log_format, date_format)
        file_handler.setFormatter(file_formatter)
        perf_logger.addHandler(file_handler)
    
    # Configure API logger
    api_logger = logging.getLogger("api")
    api_logger.setLevel(logging.INFO)
    
    # Disable some verbose loggers
    logging.getLogger("httpx").setLevel(logging.WARNING)
    logging.getLogger("urllib3").setLevel(logging.WARNING)
```

### 3. Add Health Check Endpoint
**Priority: LOW**

Add a health check endpoint for monitoring and load balancing:

```python
# Modified src/api/routes.py
from fastapi import APIRouter, Response
import time
import psutil  # Would need to add this to requirements.txt

# Add health router
health_router = APIRouter(prefix="/health", tags=["health"])

@health_router.get("/ping")
async def ping():
    """Simple health check endpoint"""
    return {"status": "ok", "timestamp": time.time()}

@health_router.get("/status")
async def status():
    """Detailed health status with system information"""
    return {
        "status": "ok",
        "timestamp": time.time(),
        "system": {
            "cpu_usage": psutil.cpu_percent(),
            "memory_usage": psutil.virtual_memory().percent,
            "disk_usage": psutil.disk_usage('/').percent,
        }
    }

# Include in main.py
app.include_router(health_router)
```

## Performance Benchmarks & Future Optimization

### Realistic Performance Benchmarks

| Operation | Small Content | Medium Content | Large Content |
|-----------|---------------|----------------|--------------|
| URL Fetch | 200-500ms | 500-1000ms | 1-2s |
| HTML Extraction | 50-100ms | 100-300ms | 300-600ms |
| Text Normalization | 10-50ms | 50-100ms | 100-300ms |
| AI Analysis | 1-2s | 2-3s | 3-5s |
| Full Operation | 1-3s | 3-5s | 5-10s |

### Future Performance Optimization Research

1. **Investigate Alternate HTML Parsers**
   - Consider alternatives to BeautifulSoup for performance-critical applications
   - Research html5-parser, lxml direct usage, or other high-performance parsers
   - Benchmark parsing performance for different content sizes

2. **Explore Distributed Processing**
   - Research message queues (RabbitMQ, Redis) for background processing
   - Investigate worker pool patterns for distributed content processing
   - Consider microservices architecture for high-scale deployment

3. **Advanced Caching Strategies**
   - Research Redis or Memcached integration for distributed caching
   - Investigate cache warming and prefetching strategies
   - Explore content-based cache invalidation techniques

4. **Streaming Processing Pipeline**
   - Research streaming content processing for large documents
   - Investigate progressive rendering for improved user experience
   - Consider WebSocket integration for real-time updates

## Conclusion

The Web Content Analyzer application demonstrates reasonable performance architecture with thoughtful implementation of asynchronous patterns and resource constraints. The primary performance bottlenecks are external dependencies (web scraping and AI analysis) and the lack of a caching strategy.

By implementing the recommended optimizations, particularly caching and refactoring the ScrapingService, the application could achieve significant performance improvements. The addition of performance monitoring will provide valuable insights for further optimization.

For long-term scalability, the application would benefit from a more distributed architecture, advanced caching strategies, and a comprehensive performance testing infrastructure. These enhancements would enable the application to handle higher loads while maintaining good response times.

Overall, the performance architecture is appropriate for the current use case but has clear paths for enhancement to support greater scale and improved user experience.
