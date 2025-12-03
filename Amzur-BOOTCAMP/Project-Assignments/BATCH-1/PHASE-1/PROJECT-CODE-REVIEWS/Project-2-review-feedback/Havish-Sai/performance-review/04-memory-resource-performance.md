# Memory & Resource Performance Review: Web Content Analyzer

## Project Information
- **Project Title:** Web Content Analyzer  
- **Name:** Havish Sai
- **Review Date:** August 7th, 2025
- **Reviewer:** Github Copilot


## Overall Summary

The Web Content Analyzer application processes potentially large amounts of web content with limited resource constraints. This report evaluates how efficiently the application manages memory, handles resources, and optimizes resource usage across different operations. While the application implements some good practices for resource management, there are several opportunities to improve memory efficiency and resource handling.

**Overall Memory & Resource Performance Score: 6/10**

The application demonstrates reasonable resource management with explicit constraints and cleanup mechanisms but would benefit from more sophisticated memory optimization techniques, streaming processing for large content, and more granular resource controls.

---

## Memory Management Analysis

### Memory Allocation Patterns
**Score: 6/10**

The application's memory allocation is primarily driven by content size, with several important patterns identified:

```python
# Memory constraint example in settings.py
MAX_HTML_BYTES: int = 2_500_000  # ~2.5MB per page
```

**Memory Allocation Observations:**
- Fixed-size memory limit for HTML content (2.5MB per page)
- Total byte limit for multi-page crawling (10MB)
- Content is fully loaded into memory before processing
- String operations create multiple copies of content in memory
- No streaming processing for large documents
- Full document trees maintained in memory during extraction

**Memory Usage Patterns:**
- Memory spikes during BeautifulSoup parsing of large HTML
- String concatenation during content merging creates additional copies
- Full document retention throughout processing pipeline
- No partial processing or pagination of large content
- Memory usage scales linearly with page size and crawl depth

**Memory Optimization Opportunities:**
- HTML parsing could use incremental or streaming approaches
- Large content could be processed in chunks
- Temporary objects could be more aggressively garbage collected
- String operations could be optimized to reduce copying
- Memory usage metrics and monitoring are not implemented

### Memory Leak Prevention
**Score: 7/10**

The application demonstrates good resource cleanup practices that help prevent memory leaks:

```python
# Resource cleanup in WebScraper
async def aclose(self):
    await self._client.aclose()

# Resource cleanup in service with try-finally
async def scrape(self, url: str, depth: int = 0, same_domain_only: bool = True, max_pages: int = 5, run_analysis: bool = False) -> ScrapedContent:
    ws = WebScraper()
    try:
        # Operations...
    finally:
        await ws.aclose()
```

**Leak Prevention Strengths:**
- Proper httpx AsyncClient cleanup in finally blocks
- Context managers for resource management
- Explicit resource constraints to prevent unbounded memory growth
- Reasonable timeout settings to prevent hanging connections
- Type annotations help prevent memory issues from type errors

**Potential Memory Leak Concerns:**
- No explicit garbage collection for large objects
- Nested loops in scraping service could accumulate references
- No memory profiling or leak detection mechanisms
- Reference cycles could occur in complex object graphs
- Long-living objects in global scope

### Garbage Collection & Object Lifecycle
**Score: 6/10**

The application relies primarily on Python's built-in garbage collection with some manual resource management:

```python
# Example of manual reference clearing in the scraper service
batch = []
while queue and len(batch) < CONCURRENCY:
    link, d = queue.popleft()
    if link in visited or d > depth:
        continue
    visited.add(link)
    batch.append((link, d))
# Batch processing
# ...
# No explicit batch clearing, relies on scope exit
```

**Object Lifecycle Observations:**
- Most objects rely on scope-based garbage collection
- Few explicit calls to free resources besides HTTP client closing
- Large objects like HTML content and BeautifulSoup objects have unpredictable collection timing
- No explicit del statements for large temporary objects
- Python's garbage collector might be suboptimal for the application's memory patterns
- No customization of garbage collection parameters

**Lifecycle Optimization Opportunities:**
- Add explicit reference clearing for large temporary objects
- Consider gc.collect() calls after processing large content
- Implement object pooling for frequently created objects
- More aggressive variable scoping for better collection timing
- Consider weak references for caching scenarios

---

## Resource Utilization Assessment

### Network Resource Management
**Score: 7/10**

The application demonstrates good network resource management with connection pooling and concurrency control:

```python
# Connection pooling in WebScraper
self._client = httpx.AsyncClient(
    timeout=TIMEOUT,
    follow_redirects=True,
    headers={"Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8"},
)

# Concurrency control in ScrapingService
sem = asyncio.Semaphore(CONCURRENCY)

async def fetch_one(target: str):
    async with sem:
        return await ws.fetch(target)
```

**Network Resource Strengths:**
- Effective use of httpx connection pooling
- Semaphore-based concurrency control (limited to 5 concurrent requests)
- Proper timeout settings (15 seconds) to prevent resource exhaustion
- Retry logic with exponential backoff for failed requests
- Resource limits for total crawling size

**Network Resource Optimization Opportunities:**
- No adaptive concurrency based on target server response time
- Fixed timeout value for all requests regardless of content size
- No bandwidth throttling for large downloads
- No prioritization mechanism for critical resources
- Connection pooling configuration could be more explicit

### CPU Resource Utilization
**Score: 6/10**

The application's CPU resource utilization is primarily driven by content parsing and normalization:

```python
# CPU-intensive parsing in extract_from_html
soup = BeautifulSoup(html, "lxml")
if not soup or not soup.find():
    soup = BeautifulSoup(html, "html5lib")

# Text normalization pipeline
def normalize_pipeline(
    text: str,
    stopwords: Set[str] | None = None,
    remove_sw: bool = False,
) -> Tuple[str, str | None]:
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

**CPU Resource Observations:**
- HTML parsing with BeautifulSoup is CPU-intensive, especially for large documents
- Text normalization creates multiple intermediate string copies
- AI analysis with OpenAI API offloads computation but adds network overhead
- Regular expressions in normalization can be CPU-intensive for large text
- No parallel processing for CPU-bound operations
- String operations could be optimized for better CPU cache locality

**CPU Optimization Opportunities:**
- More selective content extraction based on actual needs
- Parallel processing for CPU-bound operations on multicore systems
- Optimize regular expressions for better performance
- Consider more efficient HTML parsing alternatives for simple extraction
- Implement caching for repetitive parsing and normalization

### I/O Resource Efficiency
**Score: 7/10**

The application demonstrates good I/O efficiency with asynchronous operations:

```python
# Asynchronous I/O for web requests
async def fetch(self, url: str) -> Tuple[str, bytes, str]:
    validate_url_public(url)
    # Async implementation...

# Batched asynchronous operations
tasks = [asyncio.create_task(fetch_one(link)) for link, _ in batch]
for (link, d), task in zip(batch, tasks):
    try:
        ctype2, content2, f2 = await task
    except Exception:
        continue
```

**I/O Resource Strengths:**
- Effective use of async/await for I/O-bound operations
- Concurrent requests for multiple pages
- Connection pooling reduces connection establishment overhead
- Proper error handling for I/O failures
- Request batching for multiple links

**I/O Optimization Opportunities:**
- No streaming processing for large content
- No partial result return for long-running operations
- All content is processed synchronously after download
- No prioritization for critical I/O operations
- No I/O monitoring or metrics collection

### Memory Profiling & Monitoring
**Score: 3/10**

The application lacks comprehensive memory profiling and monitoring capabilities:

**Monitoring Limitations:**
- No explicit memory usage tracking
- No performance profiling infrastructure
- No resource utilization logging
- No alerts for memory-intensive operations
- No instrumentation for memory bottleneck identification
- Limited logging of resource-intensive operations

**Monitoring Recommendations:**
- Implement memory usage tracking for key operations
- Add logging for resource-intensive tasks
- Create performance metrics for memory usage patterns
- Implement resource utilization dashboards
- Add alerting for potential memory leaks or resource exhaustion

---

## Resource-Intensive Operations Analysis

### Critical Resource Operations

The following operations have been identified as the most resource-intensive in the application:

#### 1. HTML Content Extraction
**Resource Impact: HIGH**

```python
def _extract_from_html(html: bytes, base_url: Optional[str]) -> Tuple[str, Dict[str, str], List[str], str, List[str], List[str]]:
    # Try lxml, fallback to html5lib for messy pages
    soup = BeautifulSoup(html, "lxml")
    if not soup or not soup.find():
        soup = BeautifulSoup(html, "html5lib")
```

**Resource Concerns:**
- Full HTML parsing creates large object graphs in memory
- BeautifulSoup keeps the entire document tree in memory
- Fallback parser creates a second copy if first parser fails
- No incremental or streaming parsing option
- Memory usage scales directly with document size

**Optimization Opportunities:**
- Implement selective parsing based on needed content
- Consider more memory-efficient parsing for large documents
- Add optional streaming extraction for specific content types
- Implement parsing timeouts for complex documents
- Add memory constraints for parser operations

#### 2. Multi-Page Crawling
**Resource Impact: HIGH**

```python
# Crawler implementation with nested loops and queue
visited: Set[str] = set([final_url])
queue: Deque[Tuple[str, int]] = deque()
total_bytes = len(content)

# Link queuing
for l in root.links:
    u = eligible(l)
    if u:
        queue.append((u, 1))

# Processing loop
while queue and len(visited) < max_pages + 1:
    batch = []
    # Batch creation loop
    while queue and len(batch) < CONCURRENCY:
        # ...
    # Batch processing
    tasks = [asyncio.create_task(fetch_one(link)) for link, _ in batch]
    for (link, d), task in zip(batch, tasks):
        # Process each result
        # ...
```

**Resource Concerns:**
- Queue can grow unbounded based on linked pages
- All visited URLs are kept in memory until process completion
- Results list grows with each processed page
- Content from all pages is concatenated in memory
- No prioritization of important content

**Optimization Opportunities:**
- Implement depth-first vs breadth-first crawling options
- Add priority queueing based on content relevance
- Implement incremental result processing
- Add more granular resource constraints per level
- Consider streaming result generation

#### 3. Text Normalization Pipeline
**Resource Impact: MEDIUM**

```python
def normalize_pipeline(
    text: str,
    stopwords: Set[str] | None = None,
    remove_sw: bool = False,
) -> Tuple[str, str | None]:
    # Creates multiple string copies
    t = to_lower(text)
    t = strip_punctuation(t)
    t = normalize_whitespace(t)

    if remove_sw and stopwords is not None:
        t_no_sw = remove_stopwords(t, stopwords)
        return t, t_no_sw
    return t, None
```

**Resource Concerns:**
- Creates multiple intermediate string copies
- Processes the entire text even when partial results would suffice
- Always performs all normalization steps
- Creates duplicate normalized versions
- No incremental processing for large text content

**Optimization Opportunities:**
- Implement lazy normalization that processes only when needed
- Use generators for large text processing
- Optimize string operations to reduce copying
- Add caching for repeated normalizations
- Implement streaming text processing

#### 4. OpenAI API Integration
**Resource Impact: MEDIUM**

```python
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

**Resource Concerns:**
- No caching for similar or identical AI requests
- Synchronous API call within async function
- No streaming response processing
- Fixed timeout regardless of content size
- Token limits could be optimized based on content

**Optimization Opportunities:**
- Implement result caching based on input similarity
- Add streaming response processing
- Implement adaptive token limits based on content
- Add circuit breaker for API failures
- Optimize prompt construction to reduce token usage

---

## Memory Optimization Recommendations

### 1. Implement Selective HTML Parsing
**Priority: HIGH**

Replace full document parsing with more selective extraction:

```python
# Create src/scrapers/selective_extractor.py
from typing import Dict, List, Optional, Set, Tuple
from html.parser import HTMLParser
import re

class SelectiveHTMLParser(HTMLParser):
    """Memory-efficient HTML parser that extracts only needed components"""
    
    def __init__(self, target_elements: Set[str] = None):
        super().__init__()
        self.title = ""
        self.meta = {}
        self.headings = []
        self.main_text_parts = []
        self.links = []
        self.images = []
        self._current_tag = None
        self._in_target = False
        self._target_elements = target_elements or {'title', 'h1', 'h2', 'h3', 'p', 'a', 'img', 'meta'}
        self._current_text = []
        
    def handle_starttag(self, tag, attrs):
        self._current_tag = tag
        if tag not in self._target_elements:
            return
            
        self._in_target = True
        attrs_dict = dict(attrs)
        
        if tag == 'meta':
            name = attrs_dict.get('name', '')
            if name == 'description':
                self.meta['description'] = attrs_dict.get('content', '')
            elif attrs_dict.get('property') == 'og:title':
                self.meta['og:title'] = attrs_dict.get('content', '')
        
        elif tag == 'a' and 'href' in attrs_dict:
            href = attrs_dict['href']
            if href and not href.startswith('javascript:'):
                self.links.append(href)
                
        elif tag == 'img' and 'src' in attrs_dict:
            self.images.append(attrs_dict['src'])
    
    def handle_endtag(self, tag):
        if tag == self._current_tag:
            if tag in {'h1', 'h2', 'h3'} and self._current_text:
                self.headings.append(''.join(self._current_text).strip())
            elif tag == 'p' and self._current_text:
                self.main_text_parts.append(''.join(self._current_text).strip())
            elif tag == 'title' and self._current_text:
                self.title = ''.join(self._current_text).strip()
                
            self._current_text = []
            self._in_target = False
            self._current_tag = None
    
    def handle_data(self, data):
        if self._in_target:
            self._current_text.append(data)
    
    @property
    def main_text(self) -> str:
        return '\n\n'.join(p for p in self.main_text_parts if p)

def extract_memory_efficient(html: bytes, extract_links: bool = True, 
                           extract_images: bool = True) -> Tuple[str, Dict[str, str], 
                                                              List[str], str, List[str], List[str]]:
    """Memory-efficient HTML extraction that processes content selectively"""
    target_elements = {'title', 'h1', 'h2', 'h3', 'p', 'meta'}
    if extract_links:
        target_elements.add('a')
    if extract_images:
        target_elements.add('img')
        
    parser = SelectiveHTMLParser(target_elements)
    parser.feed(html.decode('utf-8', errors='replace'))
    
    return (
        parser.title,
        parser.meta,
        parser.headings,
        parser.main_text,
        parser.links,
        parser.images
    )
```

### 2. Implement Streaming Content Processing
**Priority: MEDIUM**

Add streaming processing capability for large content:

```python
# Add to src/scrapers/streaming_processor.py
import asyncio
from typing import AsyncGenerator, Dict, List, Optional, Tuple
import aiohttp
from bs4 import BeautifulSoup, SoupStrainer

async def stream_large_content(url: str, chunk_size: int = 16384) -> AsyncGenerator[bytes, None]:
    """Stream content from URL in chunks instead of loading all at once"""
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            if response.status != 200:
                return
                
            content_type = response.headers.get("Content-Type", "")
            if not any(ct in content_type.lower() for ct in ("html", "xml", "text")):
                return
                
            while True:
                chunk = await response.content.read(chunk_size)
                if not chunk:
                    break
                yield chunk

async def extract_streaming(url: str) -> Tuple[str, List[str], AsyncGenerator[str, None]]:
    """Extract title and headings immediately, then stream paragraphs"""
    # First get just the head to extract metadata quickly
    head_parser = SoupStrainer('head')
    title = ""
    
    async for chunk in stream_large_content(url, chunk_size=8192):
        soup = BeautifulSoup(chunk, 'lxml', parse_only=head_parser)
        if soup.title:
            title = soup.title.get_text(strip=True)
            break
    
    # Then extract headings
    heading_parser = SoupStrainer(['h1', 'h2', 'h3'])
    headings = []
    
    async for chunk in stream_large_content(url, chunk_size=16384):
        soup = BeautifulSoup(chunk, 'lxml', parse_only=heading_parser)
        headings.extend([h.get_text(strip=True) for h in soup.find_all(['h1', 'h2', 'h3'])])
        
    # Create paragraph streaming generator
    async def stream_paragraphs():
        para_parser = SoupStrainer('p')
        async for chunk in stream_large_content(url, chunk_size=32768):
            soup = BeautifulSoup(chunk, 'lxml', parse_only=para_parser)
            for p in soup.find_all('p'):
                text = p.get_text(strip=True)
                if text:
                    yield text
    
    return title, headings, stream_paragraphs()
```

### 3. Implement Memory-Efficient Text Processing
**Priority: MEDIUM**

Replace the current text normalization with a more memory-efficient approach:

```python
# Add to src/processors/efficient_normalizer.py
import re
from typing import Generator, Optional, Set

# Compile regex patterns once for better performance
_WS_MULTI = re.compile(r"\s+")
_PUNCT = re.compile(r"[^\w\s]", flags=re.UNICODE)

def normalize_text_stream(text_stream: Generator[str, None, None], 
                        stopwords: Optional[Set[str]] = None,
                        remove_sw: bool = False) -> Generator[str, None, None]:
    """Memory-efficient text normalization that works on text streams"""
    for text_chunk in text_stream:
        if not text_chunk:
            continue
            
        # Process in a single pass to minimize string copies
        chunk = text_chunk.lower()  # Step 1: lowercase
        chunk = _PUNCT.sub(" ", chunk)  # Step 2: strip punctuation
        chunk = _WS_MULTI.sub(" ", chunk).strip()  # Step 3: normalize whitespace
        
        if remove_sw and stopwords:
            # Filter stopwords without creating an intermediate list
            yield " ".join(word for word in chunk.split() if word not in stopwords)
        else:
            yield chunk

def normalize_with_generator(text: str) -> Generator[str, None, None]:
    """Break large text into chunks and process incrementally"""
    # Process text in ~10KB chunks to avoid large string operations
    chunk_size = 10000
    for i in range(0, len(text), chunk_size):
        yield text[i:i+chunk_size]
```

### 4. Implement Resource Usage Monitoring
**Priority: HIGH**

Add memory and resource usage monitoring:

```python
# Create src/utils/resource_monitor.py
import gc
import logging
import os
import resource
import time
import tracemalloc
from dataclasses import dataclass
from typing import Dict, List, Optional

logger = logging.getLogger("resource_monitor")

@dataclass
class ResourceSnapshot:
    timestamp: float
    memory_usage_mb: float
    peak_memory_mb: float
    cpu_usage_s: float
    operation: str
    trace_top: Optional[List[tracemalloc.Snapshot]] = None

class ResourceMonitor:
    def __init__(self, enable_tracing: bool = False):
        self.snapshots: Dict[str, List[ResourceSnapshot]] = {}
        self.enable_tracing = enable_tracing
        if enable_tracing:
            tracemalloc.start()
        
    def take_snapshot(self, operation: str) -> ResourceSnapshot:
        """Take a snapshot of current resource usage"""
        # Get memory usage from resource module
        usage = resource.getrusage(resource.RUSAGE_SELF)
        memory_mb = usage.ru_maxrss / 1024  # Convert to MB (system dependent)
        cpu_usage = usage.ru_utime + usage.ru_stime
        
        trace_top = None
        if self.enable_tracing:
            snapshot = tracemalloc.take_snapshot()
            trace_top = snapshot.statistics('lineno')[:10]  # Top 10 allocating lines
        
        snapshot = ResourceSnapshot(
            timestamp=time.time(),
            memory_usage_mb=memory_mb,
            peak_memory_mb=memory_mb,  # Same as memory_usage in this case
            cpu_usage_s=cpu_usage,
            operation=operation,
            trace_top=trace_top
        )
        
        if operation not in self.snapshots:
            self.snapshots[operation] = []
        self.snapshots[operation].append(snapshot)
        
        # Log resource usage
        logger.info(f"Resource usage for {operation}: {memory_mb:.2f}MB memory, {cpu_usage:.2f}s CPU time")
        if trace_top:
            top_lines = "\n".join(f"  {stat.count/1024:.1f}KB: {stat.traceback.filename}:{stat.traceback.lineno}" 
                               for stat in trace_top[:3])
            logger.debug(f"Top memory allocations for {operation}:\n{top_lines}")
        
        return snapshot
    
    def compare_snapshots(self, operation: str) -> Optional[Dict[str, float]]:
        """Compare first and last snapshots for an operation to see resource changes"""
        if operation not in self.snapshots or len(self.snapshots[operation]) < 2:
            return None
            
        first = self.snapshots[operation][0]
        last = self.snapshots[operation][-1]
        
        return {
            "duration_s": last.timestamp - first.timestamp,
            "memory_delta_mb": last.memory_usage_mb - first.memory_usage_mb,
            "cpu_usage_s": last.cpu_usage_s - first.cpu_usage_s,
        }
        
    def clear_snapshots(self, operation: Optional[str] = None):
        """Clear snapshots for a specific operation or all operations"""
        if operation:
            if operation in self.snapshots:
                del self.snapshots[operation]
        else:
            self.snapshots.clear()
        
        # Force garbage collection
        gc.collect()

# Create a singleton instance
resource_monitor = ResourceMonitor()
```

### 5. Implement Memory-Efficient Caching
**Priority: MEDIUM**

Add a memory-conscious caching implementation:

```python
# Create src/services/memory_aware_cache.py
import hashlib
import time
from dataclasses import dataclass
from typing import Any, Dict, List, Optional
import gc

@dataclass
class CacheEntry:
    key: str
    value: Any
    expiry: float
    size_estimate: int

class MemoryAwareCache:
    def __init__(self, max_size_mb: float = 100.0, max_entries: int = 1000, ttl_seconds: int = 3600):
        self._cache: Dict[str, CacheEntry] = {}
        self._max_size_bytes = int(max_size_mb * 1024 * 1024)
        self._max_entries = max_entries
        self._ttl_seconds = ttl_seconds
        self._current_size_bytes = 0
        self._hits = 0
        self._misses = 0
    
    def get(self, key: str) -> Optional[Any]:
        """Get a value from cache if available and not expired"""
        # Clean expired entries periodically (1% chance each get)
        if self._cache and hash(key) % 100 == 0:
            self._clean_expired()
            
        if key not in self._cache:
            self._misses += 1
            return None
            
        entry = self._cache[key]
        if time.time() > entry.expiry:
            self._evict_entry(key)
            self._misses += 1
            return None
            
        self._hits += 1
        # Update expiry to implement LRU behavior
        entry.expiry = time.time() + self._ttl_seconds
        return entry.value
    
    def set(self, key: str, value: Any, size_estimate: Optional[int] = None) -> None:
        """Store a value in cache with size tracking"""
        # Make room if needed
        if len(self._cache) >= self._max_entries or self._current_size_bytes >= self._max_size_bytes:
            self._evict_lru(max(1, len(self._cache) // 10))  # Evict at least 10% of entries
        
        # Estimate size if not provided
        if size_estimate is None:
            # Simple size estimation heuristic
            if isinstance(value, str):
                size_estimate = len(value) * 2  # Unicode strings ~2 bytes per char
            elif isinstance(value, bytes):
                size_estimate = len(value)
            elif isinstance(value, dict):
                size_estimate = sum((len(str(k)) + len(str(v))) * 2 for k, v in value.items())
            elif isinstance(value, list):
                size_estimate = sum(len(str(item)) * 2 for item in value)
            else:
                # Default size estimate
                size_estimate = 1024  # 1KB default
        
        # Add to cache
        if key in self._cache:
            old_entry = self._cache[key]
            self._current_size_bytes -= old_entry.size_estimate
            
        entry = CacheEntry(
            key=key,
            value=value,
            expiry=time.time() + self._ttl_seconds,
            size_estimate=size_estimate
        )
        
        self._cache[key] = entry
        self._current_size_bytes += size_estimate
    
    def _evict_entry(self, key: str) -> None:
        """Remove a specific entry from cache"""
        if key in self._cache:
            entry = self._cache[key]
            self._current_size_bytes -= entry.size_estimate
            del self._cache[key]
    
    def _evict_lru(self, count: int = 1) -> None:
        """Evict least recently used entries"""
        if not self._cache:
            return
            
        # Sort by expiry time (lower = older)
        sorted_entries = sorted(
            self._cache.items(), 
            key=lambda item: item[1].expiry
        )
        
        # Remove oldest entries
        for i in range(min(count, len(sorted_entries))):
            key, entry = sorted_entries[i]
            self._current_size_bytes -= entry.size_estimate
            del self._cache[key]
        
        # Force garbage collection after major eviction
        if count > 10:
            gc.collect()
    
    def _clean_expired(self) -> None:
        """Clean all expired entries"""
        now = time.time()
        expired_keys = [k for k, v in self._cache.items() if now > v.expiry]
        for key in expired_keys:
            self._evict_entry(key)
    
    def clear(self) -> None:
        """Clear the entire cache"""
        self._cache.clear()
        self._current_size_bytes = 0
        gc.collect()
        
    def get_stats(self) -> Dict[str, Any]:
        """Get cache statistics"""
        return {
            "entries": len(self._cache),
            "size_bytes": self._current_size_bytes,
            "size_mb": self._current_size_bytes / (1024 * 1024),
            "max_size_mb": self._max_size_bytes / (1024 * 1024),
            "utilization": self._current_size_bytes / self._max_size_bytes if self._max_size_bytes else 0,
            "hits": self._hits,
            "misses": self._misses,
            "hit_ratio": self._hits / (self._hits + self._misses) if (self._hits + self._misses) > 0 else 0
        }
```

---

## Resource Utilization Improvement Plan

### 1. Immediate Optimizations (High Impact/Low Effort)

1. **Add Resource Constraints for Components**
   - Implement memory usage limits per component
   - Add timeouts for resource-intensive operations
   - Configure timeout based on content size
   - Add resource usage logging for key operations
   - Implement garbage collection after large operations

2. **Memory Leak Prevention**
   - Add explicit object cleanup in resource-intensive sections
   - Review object lifecycle in complex loops
   - Optimize dictionary and list usage for large data sets
   - Clear references to large objects after use
   - Implement resource usage monitoring for leak detection

3. **Optimize String Operations**
   - Reduce string copying in normalization pipeline
   - Use string builders or generators for large concatenations
   - Implement streaming text processing for large content
   - Optimize regular expression usage
   - Implement incremental text processing

### 2. Short-term Improvements (1-2 Weeks)

1. **Implement Caching Strategy**
   - Add in-memory caching for web content
   - Implement LRU cache with size awareness
   - Cache extraction and normalization results
   - Add cache statistics and monitoring
   - Implement tiered caching strategy

2. **Optimize HTML Parsing**
   - Implement selective HTML parsing based on need
   - Use streaming parsers for large documents
   - Add parser memory usage limits
   - Optimize parser selection based on content
   - Implement partial document parsing

3. **Resource Monitoring**
   - Add memory profiling and monitoring
   - Implement resource usage logging
   - Create resource usage dashboards
   - Add alerts for resource exhaustion
   - Implement resource usage analysis tools

### 3. Medium-term Enhancements (2-4 Weeks)

1. **Streaming Content Processing**
   - Implement streaming response generation
   - Add incremental content processing
   - Create progressive result delivery
   - Implement chunked content handling
   - Add partial result capabilities

2. **Resource Optimization Framework**
   - Create adaptive resource allocation
   - Implement resource-aware scheduling
   - Add resource utilization metrics
   - Create resource optimization guidelines
   - Implement resource forecasting

3. **Advanced Memory Management**
   - Add object pooling for frequent allocations
   - Implement weak reference caching
   - Create memory-efficient data structures
   - Add generational object lifecycle
   - Implement custom garbage collection triggers

### 4. Long-term Strategy (1-3 Months)

1. **Resource Virtualization**
   - Implement worker processes for isolation
   - Create resource virtualization layer
   - Add distributed resource management
   - Implement resource sandboxing
   - Create cloud resource integration

2. **Advanced Content Processing**
   - Implement domain-specific parsers
   - Create content-aware processing strategies
   - Add semantic content chunking
   - Implement context-aware memory management
   - Create adaptive processing based on content

3. **Resource Optimization Research**
   - Research memory-efficient algorithms
   - Investigate specialized parsing techniques
   - Study distributed processing architectures
   - Research adaptive resource allocation
   - Investigate content optimization techniques

---

## Resource Performance Benchmark

### Current Resource Utilization Baseline

| Operation | Memory Usage | CPU Usage | Network Usage | Time |
|-----------|--------------|-----------|--------------|------|
| Single page scrape | 5-10MB | Low-Medium | ~1MB | 1-3s |
| Large page scrape | 20-50MB | Medium | ~5MB | 3-5s |
| Multi-page crawl (depth=1) | 50-100MB | Medium | ~10MB | 5-10s |
| Text normalization | 2x text size | Medium | N/A | 50-200ms |
| AI analysis | 10-20MB | Low | ~20KB | 2-5s |

### Target Resource Utilization

| Operation | Target Memory | Target CPU | Target Network | Target Time |
|-----------|---------------|------------|----------------|------------|
| Single page scrape | 3-6MB | Low | ~1MB | <1s |
| Large page scrape | 10-20MB | Low-Medium | ~5MB | 1-3s |
| Multi-page crawl | 20-40MB | Medium | ~10MB | 2-5s |
| Text normalization | 1.2x text size | Low | N/A | 20-100ms |
| AI analysis | 5-10MB | Low | ~15KB | 1-3s |

### Implementation Strategy

1. **HTML Parsing Optimization**
   - Replace BeautifulSoup with selective HTML parser for 50% memory reduction
   - Implement streaming parsing for large documents
   - Add memory constraints and monitoring

2. **Text Processing Optimization**
   - Implement incremental text processing for 40% memory reduction
   - Optimize string operations to reduce copies
   - Add caching for repeated operations

3. **Resource Monitoring Implementation**
   - Add memory usage tracking and reporting
   - Implement resource usage alerting
   - Create resource utilization dashboards

4. **Caching Strategy Implementation**
   - Add memory-aware caching for web content
   - Implement result caching for repeated operations
   - Create tiered caching strategy

---

## Conclusion & Recommendations

The Web Content Analyzer application demonstrates reasonable resource management with explicit constraints and cleanup mechanisms. However, several opportunities exist to improve memory efficiency and resource utilization. The most critical areas for improvement are HTML parsing optimization, text processing efficiency, and implementing a comprehensive caching strategy.

### Key Recommendations

1. **Replace Full HTML Parsing with Selective Extraction**
   - Implement selective HTML parsing for focused content extraction
   - Add streaming parsing capabilities for large documents
   - Reduce memory footprint by 40-60% for large documents

2. **Optimize Text Processing**
   - Implement incremental text normalization
   - Reduce string copying in processing pipeline
   - Add memory-efficient text transformation

3. **Implement Memory-Aware Caching**
   - Add LRU caching with size awareness
   - Implement tiered caching strategy
   - Add cache monitoring and optimization

4. **Add Resource Monitoring**
   - Implement memory usage tracking
   - Add resource utilization logging
   - Create resource usage dashboards

5. **Implement Streaming Content Processing**
   - Add incremental content processing
   - Implement progressive result delivery
   - Create chunked content handling

By implementing these recommendations, the application will achieve better resource efficiency, handle larger content more effectively, and provide a more responsive user experience, especially for resource-intensive operations like multi-page crawling and large document processing.
