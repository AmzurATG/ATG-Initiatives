# Memory & Resource Performance Review

## Project Information
- **Candidate Name:** Rushitha Chittibomma
- **Project Title:** Web Content Analyzer
- **Review Date:** September 16, 2025
- **Reviewer:** GitHub Copilot
- **Memory & Resource Performance Grade:** D
- **Overall Resource Performance Score:** 3/10

---

## Executive Summary

The Web Content Analyzer application demonstrates significant resource management issues that would prevent it from operating reliably in a production environment. The application lacks proper memory management patterns, connection pooling, resource cleanup mechanisms, and efficient data handling strategies. Most critically, the web scraping and AI analysis components operate synchronously with unbounded memory usage, creating potential for resource exhaustion under load. The Docker configuration does not define resource limits, allowing containers to consume unbounded resources. This review provides a detailed assessment of the application's resource management issues and presents prioritized recommendations to optimize memory usage, improve resource lifecycle management, and implement proper monitoring systems.

---

## Resource Performance Analysis

### Memory Management Analysis
**Score: 3/10 (Poor)**

#### Memory Allocation Patterns

The application shows several problematic memory allocation patterns:

1. **Unbounded Memory Usage in Content Processing:**
   ```python
   # scraping_service.py
   def analyze_url(self, url: str) -> Dict:
       try:
           # Security and validation checks
           if not validate_url(url):
               return {"status": "error", "error": "Invalid URL format", "url": url}
           
           if not check_url_security(url):
               return {"status": "error", "error": "URL failed security check", "url": url}
           
           # Fetch and extract content - no size limits on content
           page_data = self.scraper.fetch_page(url)
           # ...
   ```

   No limits are set on how much content can be loaded from a web page, potentially causing excessive memory consumption for large pages.

2. **Full Document Loading:** 
   ```python
   # web_scraper.py
   def fetch_page(self, url: str) -> Optional[Dict]:
       # ... validation code ...
       try:
           response = self.session.get(url, timeout=self.timeout)
           response.raise_for_status()
           
           # Entire content loaded into memory at once
           content = response.content
           soup = self.get_soup(content)
           # ...
   ```

   The entire HTTP response is loaded into memory at once, with no streaming or chunking for large responses.

3. **In-Memory Content Processing:**
   ```python
   # content_extractor.py
   def extract_content(self, soup: BeautifulSoup) -> Dict[str, str]:
       # Entire document processed in memory
       title = self._extract_title(soup)
       description = self._extract_meta_description(soup)
       main_content = self._extract_main_content(soup)
       # ...
   ```

   BeautifulSoup processes the entire DOM in memory, which can be memory-intensive for large documents.

#### Memory Leak Identification

1. **No Resource Cleanup in Web Scraper:**
   ```python
   # web_scraper.py
   def __init__(self, timeout: int = 30):
       self.timeout = timeout
       self.session = requests.Session()
       self.session.headers.update({
           'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
       })
   ```

   The `requests.Session` object is created but never explicitly closed. While Python's garbage collector will eventually clean this up, it's better practice to explicitly close sessions.

2. **No Explicit Connection Management:**
   The application does not explicitly manage HTTP connection lifecycles, relying on the default behavior of the libraries used.

#### Garbage Collection Impact

1. **Large Object Creation:** The application creates potentially large objects (complete web pages, HTML parse trees) without consideration for garbage collection.

2. **No Memory Monitoring:** No mechanisms exist to monitor memory usage or garbage collection behavior.

#### Large Object Handling

The application does not implement strategies for handling large objects:

1. **No Streaming for Large Content:** Large web pages are loaded entirely into memory.

2. **No Pagination for Large Results:** Analysis results are processed and returned as a complete unit.

#### Memory Profiling

The application lacks any memory profiling or monitoring setup:

1. **No Memory Metrics Collection:** No code for tracking memory usage.

2. **No Memory Alerting:** No alerting for high memory conditions.

### Resource Lifecycle Management
**Score: 2/10 (Critical)**

#### Connection Lifecycle Management

1. **HTTP Session Management:**
   ```python
   # web_scraper.py
   def __init__(self, timeout: int = 30):
       self.timeout = timeout
       self.session = requests.Session()
       # Session never explicitly closed
   ```

   No proper lifecycle management for the requests Session object. The session should be closed when no longer needed.

2. **No Connection Pooling:**
   The application does not implement connection pooling for HTTP requests, potentially creating many short-lived connections.

3. **Missing Resource Cleanup:**
   No `__del__` method or context manager implementation to ensure resources are properly cleaned up.

#### File Handle Management

1. **PDF Generation Without Cleanup:**
   ```python
   # export_service.py
   def to_pdf(self, data: Dict) -> bytes:
       buffer = io.BytesIO()
       doc = SimpleDocTemplate(buffer, pagesize=letter)
       # ... PDF generation code ...
       doc.build(elements)
       pdf = buffer.getvalue()
       # Buffer not explicitly closed
       return pdf
   ```

   The BytesIO buffer is not explicitly closed after use, though it will eventually be garbage collected.

#### Background Task Resource Usage

1. **No Background Task Management:**
   The application does not implement any background task system for resource-intensive operations like web scraping or AI analysis.

2. **Synchronous Processing of Long Operations:**
   ```python
   # app.py
   async def analyze_url(self, url: str, custom_prompt: Optional[str] = None) -> Dict:
       try:
           # Synchronous call blocking the event loop
           scraping_result = self.scraping_service.analyze_url(url)
           # ... more synchronous processing ...
       except Exception as e:
           return {
               "status": "error",
               "error": str(e),
               "url": url
           }
   ```

   Resource-intensive operations are performed synchronously in the request handler, blocking the server from handling other requests.

#### Container Resource Optimization

1. **No Resource Limits in Docker:**
   ```dockerfile
   # Dockerfile
   FROM python:3.11-slim
   # ... other configuration ...
   # No resource limits defined
   CMD ["uvicorn", "frontend.app:app", "--host", "0.0.0.0", "--port", "8001"]
   ```

   No memory or CPU limits are defined for the Docker container, allowing it to consume unbounded resources.

### Streaming & Large Data Handling
**Score: 3/10 (Poor)**

#### File Upload/Download Optimization

1. **Non-Streaming PDF Generation:**
   ```python
   # export_service.py
   def to_pdf(self, data: Dict) -> bytes:
       buffer = io.BytesIO()
       # ... PDF generation code ...
       pdf = buffer.getvalue()
       return pdf
   ```

   PDF generation loads the entire document into memory before returning it, rather than streaming the response.

#### Large Dataset Processing

1. **Batch Processing Without Chunking:**
   ```python
   # app.py
   async def batch_analysis(self, urls: List[str], custom_prompt: Optional[str] = None) -> List[Dict]:
       results = []
       for url in urls:
           result = await self.analyze_url(url, custom_prompt)
           results.append(result)
       return results
   ```

   Batch analysis processes all URLs and holds all results in memory before returning, with no chunking or pagination.

#### Memory-Efficient Transformations

1. **Inefficient Content Extraction:**
   ```python
   # content_extractor.py
   def _extract_main_content(self, soup: BeautifulSoup) -> str:
       # Try to find content in article tags first
       for tag_name in self.content_tags:
           article = soup.find(tag_name)
           if article:
               # Extract all text at once
               return self._clean_text(article.get_text())
       
       # If no content tags found, extract from body
       body = soup.find('body')
       if body:
           return self._clean_text(body.get_text())
       
       # Fallback to extracting all text
       return self._clean_text(soup.get_text())
   ```

   Content extraction processes and returns entire text blocks at once, rather than iteratively or in chunks.

### Background Processing Optimization
**Score: 1/10 (Critical)**

#### Task Queue Implementation

1. **No Task Queue System:**
   The application does not implement any task queue (like Celery, RQ) for background processing of resource-intensive operations.

2. **No Asynchronous Processing:**
   ```python
   # app.py
   async def batch_analysis(self, urls: List[str], custom_prompt: Optional[str] = None) -> List[Dict]:
       results = []
       for url in urls:
           # Sequential processing instead of parallel
           result = await self.analyze_url(url, custom_prompt)
           results.append(result)
       return results
   ```

   Batch analysis is performed sequentially rather than in parallel, with no background processing.

#### Long-Running Process Optimization

1. **No Timeout Handling:**
   ```python
   # web_scraper.py
   def fetch_page(self, url: str) -> Optional[Dict]:
       try:
           # Single timeout parameter, no connect vs. read timeout distinction
           response = self.session.get(url, timeout=self.timeout)
           # ...
   ```

   No separate connect and read timeouts, and no handling for abnormally long-running requests.

2. **No Resource Limits for Processes:**
   No code to limit CPU or memory usage for individual processes or operations.

### Container & Deployment Resource Optimization
**Score: 3/10 (Poor)**

#### Docker Container Configuration

1. **No Resource Limits:**
   ```dockerfile
   # Dockerfile
   FROM python:3.11-slim
   # ... other configuration ...
   # No resource limits
   ```

   Docker container has no memory or CPU limits defined.

2. **No Health Checks:**
   ```dockerfile
   # docker-compose.yml
   services:
     backend:
       build: 
         context: .
         dockerfile: Dockerfile
       ports:
         - "8001:8001"
       environment:
         - OPENAI_API_KEY=${OPENAI_API_KEY}
       # No health checks defined
   ```

   No health checks defined to monitor container resource usage or status.

#### Production Resource Configuration

1. **Default Worker Configuration:**
   ```dockerfile
   # Dockerfile
   CMD ["uvicorn", "frontend.app:app", "--host", "0.0.0.0", "--port", "8001"]
   ```

   No worker or thread configuration for the uvicorn server, using default values which may not be optimized for the application's resource usage patterns.

2. **No Auto-Scaling Configuration:**
   No configuration for auto-scaling based on resource usage metrics.

---

## Resource Performance Recommendations

### Critical Optimizations (Implement Immediately)

1. **Implement HTTP Session Lifecycle Management**

   ```python
   # web_scraper.py - Updated class with proper resource cleanup
   class WebScraper:
       def __init__(self, timeout: int = 30):
           self.timeout = timeout
           self.session = requests.Session()
           self.session.headers.update({
               'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
           })
       
       def __del__(self):
           """Ensure session is closed when the object is garbage collected."""
           if hasattr(self, 'session'):
               self.session.close()
       
       def close(self):
           """Explicitly close resources."""
           if hasattr(self, 'session'):
               self.session.close()
   ```

2. **Add Docker Resource Limits**

   ```yaml
   # docker-compose.yml - Updated with resource limits
   services:
     backend:
       build: 
         context: .
         dockerfile: Dockerfile
       ports:
         - "8001:8001"
       environment:
         - OPENAI_API_KEY=${OPENAI_API_KEY}
       volumes:
         - .:/app
       command: uvicorn frontend.app:app --host 0.0.0.0 --port 8001 --reload
       deploy:
         resources:
           limits:
             cpus: '1.0'
             memory: 512M
           reservations:
             cpus: '0.25'
             memory: 256M
   ```

3. **Implement Streaming for Large Content Processing**

   ```python
   # web_scraper.py - Updated to use streaming for large content
   def fetch_page(self, url: str) -> Optional[Dict]:
       # Validation code...
       try:
           # Use stream mode to avoid loading entire content into memory at once
           with self.session.get(url, timeout=self.timeout, stream=True) as response:
               response.raise_for_status()
               
               # Define content size limit (10MB)
               content_size_limit = 10 * 1024 * 1024  # 10MB
               content = b''
               content_length = 0
               
               # Process content in chunks
               for chunk in response.iter_content(chunk_size=8192):
                   content_length += len(chunk)
                   if content_length > content_size_limit:
                       return {
                           "status": "error",
                           "error": f"Content too large (exceeds {content_size_limit/(1024*1024)}MB)",
                           "url": url
                       }
                   content += chunk
               
               soup = self.get_soup(content.decode(response.encoding or 'utf-8'))
               # Rest of processing...
       except Exception as e:
           # Error handling...
   ```

4. **Implement Background Processing for Resource-Intensive Operations**

   ```python
   # api.py - Updated to use background tasks
   from fastapi import FastAPI, HTTPException, BackgroundTasks
   from fastapi.responses import Response
   from pydantic import BaseModel
   from typing import Dict
   import uuid
   
   # In-memory store for task results (replace with Redis in production)
   task_results = {}
   
   class URLRequest(BaseModel):
       url: str
   
   @app.post("/analyze")
   async def analyze_url(request: URLRequest, background_tasks: BackgroundTasks):
       task_id = str(uuid.uuid4())
       
       # Schedule the task to run in the background
       background_tasks.add_task(
           process_url_in_background, 
           url=request.url,
           task_id=task_id
       )
       
       return {
           "status": "processing",
           "task_id": task_id,
           "message": "URL analysis started. Check status endpoint for results."
       }
   
   async def process_url_in_background(url: str, task_id: str):
       try:
           result = await analyzer.analyze_url(url)
           task_results[task_id] = result
       except Exception as e:
           task_results[task_id] = {
               "status": "error",
               "error": str(e),
               "url": url
           }
   
   @app.get("/status/{task_id}")
   async def get_task_status(task_id: str):
       if task_id not in task_results:
           return {"status": "processing", "message": "Task still running or not found"}
       return task_results[task_id]
   ```

### High Priority Optimizations

1. **Implement Connection Pooling**

   ```python
   # web_scraper.py - Updated with connection pooling
   from requests.adapters import HTTPAdapter
   from urllib3.util.retry import Retry
   
   class WebScraper:
       def __init__(self, timeout: int = 30, max_retries: int = 3, pool_connections: int = 10, pool_maxsize: int = 10):
           self.timeout = timeout
           self.session = requests.Session()
           
           # Configure connection pooling
           adapter = HTTPAdapter(
               pool_connections=pool_connections,  # Number of connection pools
               pool_maxsize=pool_maxsize,          # Connections per pool
               max_retries=Retry(
                   total=max_retries,
                   backoff_factor=0.5,
                   status_forcelist=[500, 502, 503, 504]
               )
           )
           
           self.session.mount('http://', adapter)
           self.session.mount('https://', adapter)
           self.session.headers.update({
               'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
           })
   ```

2. **Implement Memory Monitoring**

   ```python
   # app.py - Add memory monitoring
   import psutil
   import logging
   
   class MemoryMonitor:
       def __init__(self, threshold_mb: int = 512, log_interval: int = 60):
           self.threshold_bytes = threshold_mb * 1024 * 1024
           self.log_interval = log_interval
           self.last_log_time = time.time()
           self.logger = logging.getLogger("memory_monitor")
           
       def check_memory(self):
           """Check current memory usage and log if over threshold."""
           current_time = time.time()
           if current_time - self.last_log_time > self.log_interval:
               process = psutil.Process()
               memory_info = process.memory_info()
               memory_usage = memory_info.rss
               
               log_message = f"Memory usage: {memory_usage / (1024*1024):.2f} MB"
               if memory_usage > self.threshold_bytes:
                   self.logger.warning(f"HIGH MEMORY USAGE: {log_message}")
               else:
                   self.logger.info(log_message)
               
               self.last_log_time = current_time
               return memory_usage
           return None
   
   # Create a singleton instance
   memory_monitor = MemoryMonitor()
   
   # Use in request handlers
   @app.middleware("http")
   async def monitor_memory(request: Request, call_next):
       memory_monitor.check_memory()
       response = await call_next(request)
       return response
   ```

3. **Implement Chunking for Batch Operations**

   ```python
   # app.py - Updated batch processing with chunking
   async def batch_analysis(self, urls: List[str], custom_prompt: Optional[str] = None, chunk_size: int = 5) -> List[Dict]:
       """
       Process URLs in chunks to limit memory usage.
       
       Args:
           urls: List of URLs to analyze
           custom_prompt: Optional custom prompt for AI analysis
           chunk_size: Number of URLs to process in each chunk
           
       Returns:
           List of analysis results
       """
       all_results = []
       
       # Process URLs in chunks
       for i in range(0, len(urls), chunk_size):
           chunk = urls[i:i+chunk_size]
           
           # Process each URL in the chunk concurrently
           tasks = [self.analyze_url(url, custom_prompt) for url in chunk]
           chunk_results = await asyncio.gather(*tasks, return_exceptions=True)
           
           # Process results, handling any exceptions
           for j, result in enumerate(chunk_results):
               if isinstance(result, Exception):
                   all_results.append({
                       "status": "error",
                       "error": str(result),
                       "url": chunk[j]
                   })
               else:
                   all_results.append(result)
       
       return all_results
   ```

### Medium Priority Optimizations

1. **Optimize PDF Generation with Chunked Processing**

   ```python
   # export_service.py - Updated PDF generation with resource controls
   def to_pdf(self, data: Dict) -> bytes:
       """Generate PDF with memory efficiency considerations."""
       try:
           buffer = io.BytesIO()
           doc = SimpleDocTemplate(
               buffer,
               pagesize=letter,
               # Limit memory usage by limiting image resolution and compression
               pageCompression=1,
               invariant=1
           )
           
           elements = []
           styles = getSampleStyleSheet()
           
           # Process content in smaller chunks
           # Add document elements in batches
           batch_size = 10
           content_items = self._create_pdf_section(data, styles)
           
           for i in range(0, len(content_items), batch_size):
               batch = content_items[i:i+batch_size]
               elements.extend(batch)
               
               # Force partial processing to avoid memory buildup
               if i % (batch_size * 5) == 0 and i > 0:
                   gc.collect()
           
           # Build the document
           doc.build(elements)
           pdf = buffer.getvalue()
           
           # Explicitly close the buffer
           buffer.close()
           
           return pdf
       except Exception as e:
           raise Exception(f"PDF generation failed: {str(e)}")
   ```

2. **Add Uvicorn Worker Configuration**

   ```yaml
   # docker-compose.yml - Updated with worker configuration
   services:
     backend:
       build: 
         context: .
         dockerfile: Dockerfile
       # ... other config ...
       command: >
         uvicorn frontend.app:app
         --host 0.0.0.0
         --port 8001
         --workers 4
         --limit-concurrency 20
         --timeout-keep-alive 30
   ```

3. **Implement File Handle Tracking and Cleanup**

   ```python
   # Create a resource tracker module (resource_tracker.py)
   import weakref
   import logging
   
   logger = logging.getLogger("resource_tracker")
   
   class ResourceTracker:
       def __init__(self):
           self.open_resources = weakref.WeakSet()
           self.resource_count = 0
           self.resource_limit = 100  # Maximum number of open resources
       
       def register(self, resource):
           """Register a resource to track."""
           self.open_resources.add(resource)
           self.resource_count += 1
           
           if self.resource_count > self.resource_limit:
               logger.warning(f"Resource limit exceeded: {self.resource_count} resources open")
               
           return resource
       
       def unregister(self, resource):
           """Unregister a resource."""
           if resource in self.open_resources:
               self.open_resources.remove(resource)
               self.resource_count -= 1
       
       def cleanup_all(self):
           """Attempt to clean up all tracked resources."""
           for resource in list(self.open_resources):
               try:
                   if hasattr(resource, 'close'):
                       resource.close()
                   self.unregister(resource)
               except Exception as e:
                   logger.error(f"Error closing resource: {e}")
   
   # Create a singleton instance
   resource_tracker = ResourceTracker()
   
   # Use in file operations
   def safe_open(file_path, mode='r'):
       """Open a file and register it with the resource tracker."""
       file_obj = open(file_path, mode)
       return resource_tracker.register(file_obj)
   ```

### Low Priority Optimizations

1. **Add Docker Health Checks**

   ```yaml
   # docker-compose.yml - Add health checks
   services:
     backend:
       # ... other config ...
       healthcheck:
         test: ["CMD", "curl", "-f", "http://localhost:8001/health"]
         interval: 30s
         timeout: 10s
         retries: 3
         start_period: 40s
   ```

2. **Implement Memory-Efficient HTML Parsing**

   ```python
   # content_extractor.py - Memory-efficient HTML parsing
   def extract_content(self, html_content: str) -> Dict[str, str]:
       """
       Extract content from HTML using memory-efficient parsing.
       Use HTML5 parser with stream processing for larger documents.
       """
       from html5lib import HTMLParser, treebuilders
       
       # For large documents (>1MB), use iterative parsing
       if len(html_content) > 1024 * 1024:  # 1MB
           # Create a custom treebuilder with limited depth
           parser = HTMLParser(tree=treebuilders.getTreeBuilder("dom"))
           
           # Parse document with memory limits
           document = parser.parse(html_content, parseMeta=False)
           
           # Extract essential elements only
           title = self._extract_minimal_title(document)
           main_content = self._extract_minimal_content(document)
           
           # Clear parser references
           parser = None
           
           return {
               "title": title,
               "main_content": main_content,
               "is_limited_parse": True
           }
       else:
           # For smaller documents, use normal BeautifulSoup parsing
           soup = BeautifulSoup(html_content, 'html.parser')
           # ... existing extraction logic ...
   ```

3. **Add Resource Usage Metrics Endpoint**

   ```python
   # api.py - Add resource metrics endpoint
   import psutil
   
   @app.get("/metrics/resources")
   async def resource_metrics():
       """Endpoint to monitor resource usage."""
       process = psutil.Process()
       
       # Get memory information
       memory_info = process.memory_info()
       
       # Get CPU usage (percentage)
       cpu_percent = process.cpu_percent(interval=0.5)
       
       # Get open file descriptors
       try:
           open_files = len(process.open_files())
       except (psutil.AccessDenied, psutil.Error):
           open_files = -1
       
       # Get connections
       try:
           connections = len(process.connections())
       except (psutil.AccessDenied, psutil.Error):
           connections = -1
       
       return {
           "memory": {
               "rss_mb": memory_info.rss / (1024 * 1024),
               "vms_mb": memory_info.vms / (1024 * 1024),
           },
           "cpu_percent": cpu_percent,
           "open_files": open_files,
           "connections": connections,
           "threads": len(process.threads()),
           "uptime_seconds": time.time() - process.create_time()
       }
   ```

---

## Resource Monitoring Recommendations

1. **Memory Usage Monitoring**

   ```python
   # Add to app.py or a separate monitoring module
   import psutil
   import logging
   import time
   from threading import Thread
   
   class ResourceMonitor:
       def __init__(self, interval=60, memory_threshold_mb=400):
           self.interval = interval
           self.memory_threshold_mb = memory_threshold_mb
           self.running = False
           self.logger = logging.getLogger("resource_monitor")
           
       def start(self):
           """Start the monitoring thread."""
           self.running = True
           Thread(target=self._monitor_loop, daemon=True).start()
           
       def stop(self):
           """Stop the monitoring thread."""
           self.running = False
           
       def _monitor_loop(self):
           """Main monitoring loop."""
           while self.running:
               self._check_resources()
               time.sleep(self.interval)
               
       def _check_resources(self):
           """Check current resource usage."""
           try:
               process = psutil.Process()
               
               # Memory usage
               memory_info = process.memory_info()
               memory_mb = memory_info.rss / (1024 * 1024)
               
               # CPU usage
               cpu_percent = process.cpu_percent(interval=1)
               
               # File descriptors
               try:
                   open_files = len(process.open_files())
               except:
                   open_files = -1
               
               # Log resource usage
               log_message = (
                   f"Resource Usage: Memory={memory_mb:.2f}MB, "
                   f"CPU={cpu_percent:.1f}%, "
                   f"Files={open_files}"
               )
               
               if memory_mb > self.memory_threshold_mb:
                   self.logger.warning(f"HIGH MEMORY: {log_message}")
               else:
                   self.logger.info(log_message)
                   
           except Exception as e:
               self.logger.error(f"Error in resource monitoring: {e}")
   
   # Initialize and start the monitor on application startup
   @app.on_event("startup")
   async def start_resource_monitor():
       monitor = ResourceMonitor()
       monitor.start()
   ```

2. **Resource Leak Detection**

   ```python
   # Add to app.py or a separate monitoring module
   class LeakDetector:
       def __init__(self, check_interval=300, growth_threshold=0.1):
           self.check_interval = check_interval
           self.growth_threshold = growth_threshold
           self.last_check_time = time.time()
           self.last_memory_usage = self._get_memory_usage()
           self.logger = logging.getLogger("leak_detector")
           
       def _get_memory_usage(self):
           """Get current memory usage."""
           try:
               process = psutil.Process()
               return process.memory_info().rss
           except Exception as e:
               self.logger.error(f"Error getting memory usage: {e}")
               return 0
               
       def check_for_leaks(self):
           """Check for potential memory leaks."""
           current_time = time.time()
           
           # Only check periodically
           if current_time - self.last_check_time < self.check_interval:
               return
               
           # Get current memory usage
           current_memory = self._get_memory_usage()
           
           # Calculate growth rate
           memory_growth = current_memory - self.last_memory_usage
           memory_growth_mb = memory_growth / (1024 * 1024)
           growth_percent = memory_growth / self.last_memory_usage if self.last_memory_usage else 0
           
           # Log growth
           self.logger.info(
               f"Memory growth: {memory_growth_mb:.2f}MB "
               f"({growth_percent:.2%}) over {(current_time - self.last_check_time) / 60:.1f} minutes"
           )
           
           # Alert if growth exceeds threshold
           if growth_percent > self.growth_threshold:
               self.logger.warning(
                   f"POTENTIAL MEMORY LEAK: {growth_percent:.2%} growth detected. "
                   f"Current usage: {current_memory / (1024 * 1024):.2f}MB"
               )
               
           # Update check time and memory usage
           self.last_check_time = current_time
           self.last_memory_usage = current_memory
   
   # Initialize the leak detector
   leak_detector = LeakDetector()
   
   # Use in middleware to periodically check
   @app.middleware("http")
   async def check_for_leaks(request: Request, call_next):
       leak_detector.check_for_leaks()
       response = await call_next(request)
       return response
   ```

3. **Graceful Resource Management on Shutdown**

   ```python
   # app.py - Add shutdown handlers
   @app.on_event("shutdown")
   async def shutdown_event():
       """Clean up resources when shutting down."""
       # Close all requests sessions
       if hasattr(app.state, "scraper") and hasattr(app.state.scraper, "session"):
           app.state.scraper.session.close()
           
       # Force garbage collection
       import gc
       gc.collect()
       
       # Log shutdown
       logging.info("Application shutdown - resources cleaned up")
   ```

---

## Conclusion

The Web Content Analyzer application shows significant resource management issues that would prevent it from operating reliably in a production environment. The most critical concerns are the unbounded memory usage in content processing, lack of connection pooling, absence of resource cleanup mechanisms, and synchronous processing of resource-intensive operations.

The overall resource performance grade of D (3/10) reflects the substantial improvements needed before this application could be deployed in a production setting. By implementing the recommended optimizations—particularly adding proper session lifecycle management, Docker resource limits, streaming for large content, and background task processing—the application could see dramatic improvements in resource efficiency and reliability.

Key priorities for immediate improvement:
1. Implement HTTP session lifecycle management
2. Add Docker resource limits
3. Implement streaming for large content processing
4. Move resource-intensive operations to background tasks

These changes would form the foundation for a more resource-efficient application that could then be further optimized with the medium and low-priority recommendations.
