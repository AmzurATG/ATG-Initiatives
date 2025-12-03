# API Performance Review

**Project:** Web-Content-Analysis-main
**Reviewer:** GitHub Copilot
**Date:** 2023-10-27

## API Performance Analysis

After analyzing the Web-Content-Analysis-main application, I've conducted a thorough assessment of the API performance characteristics, response time optimization opportunities, and throughput limitations. This review focuses on the Flask route handlers, request/response processing, and API endpoint performance.

### Overall API Performance Rating: POOR (3/10)

The Web-Content-Analysis-main application demonstrates significant API performance concerns that would affect user experience and scalability. The single Flask route handler performs multiple resource-intensive operations synchronously, leading to potentially long response times and limited throughput capacity.

---

## Response Time Optimization

### Current Response Time Analysis

The application has a single route handler that processes all requests:

```python
@app.route('/', methods=["GET", "POST"])
def index():
    if request.method == 'POST':
        url = request.form['url']
        try:
            # Sequential processing chain with multiple blocking operations:
            
            # 1. External HTTP request - blocking network I/O
            response = requests.get(url)
            
            # 2. HTML parsing - CPU intensive
            soup = BeautifulSoup(response.text, 'html.parser')
            # Extract text content
            text = ' '.join([p.get_text() for p in soup.find_all('p')])
            
            # 3. Sentiment analysis - CPU intensive
            blob = TextBlob(text)
            sentiment = blob.sentiment
            
            # 4. Summarization - CPU intensive
            summary = summarize(text, ratio=0.2)
            
            # 5. Word cloud generation - CPU and I/O intensive
            stop_words = set(stopwords.words('english'))
            wordcloud = WordCloud(...).generate(text)
            filename = f"wordcloud_{uuid.uuid4().hex[:8]}.png"
            filepath = os.path.join('static', filename)
            wordcloud.to_file(filepath)
            
            # 6. Additional NLP processing - CPU intensive
            # - Tokenization
            # - Part-of-speech tagging
            # - Named entity recognition
            # ...
            
            # 7. Template rendering - CPU intensive
            return render_template('index.html', url=url, text=text, sentiment=sentiment, ...)
            
        except Exception as e:
            return render_template('index.html', error=str(e))
    
    # Simple GET request rendering - minimal processing
    return render_template('index.html')
```

### Response Time Issues

1. **Sequential Blocking Operations**: All operations happen synchronously, causing the entire request to wait for each step to complete.

2. **External HTTP Request**: No timeout set for external requests, potentially leading to long-hanging operations.

3. **CPU-Intensive Processing**: Multiple CPU-intensive operations (parsing, NLP tasks, image generation) occurring sequentially.

4. **File I/O Operations**: Synchronous file system operations for word cloud generation.

5. **No Processing Limits**: No limits on text size or processing time, allowing potentially unbounded execution time.

6. **Monolithic Handler**: All processing happens in a single route handler function without task separation.

### Expected Response Times

Based on the implementation, estimated response times would vary significantly based on:

| Input Size         | Estimated Response Time |
|--------------------|-------------------------|
| Small webpage      | 2-5 seconds             |
| Medium webpage     | 5-15 seconds            |
| Large webpage      | 15+ seconds             |
| Very large webpage | 30+ seconds or timeout  |

These response times far exceed the recommended maximum response time of 1 second for good user experience, and even surpass the 10-second threshold where users typically abandon a task.

### Response Time Optimization Recommendations

1. **Implement Asynchronous Processing**:
   - Convert to an async architecture using FastAPI or Flask with Celery
   - Separate the request handling from processing

```python
# Example FastAPI implementation with background tasks
from fastapi import FastAPI, BackgroundTasks, Form
from fastapi.responses import HTMLResponse
from fastapi.templating import Jinja2Templates

app = FastAPI()
templates = Jinja2Templates(directory="templates")

@app.get("/")
def index(request: Request):
    return templates.TemplateResponse("index.html", {"request": request})

@app.post("/")
async def analyze_url(request: Request, background_tasks: BackgroundTasks, url: str = Form(...)):
    # Generate a task ID
    task_id = uuid.uuid4().hex
    
    # Start processing in background
    background_tasks.add_task(process_url, url, task_id)
    
    # Return immediately with task ID
    return templates.TemplateResponse(
        "processing.html", 
        {"request": request, "task_id": task_id}
    )

@app.get("/status/{task_id}")
async def task_status(task_id: str):
    # Check task status in Redis or database
    # ...
```

2. **Implement Request Timeouts**:
   - Add timeout for external HTTP requests
   - Set overall processing time limits

```python
# Example with timeout
try:
    response = requests.get(url, timeout=10)
except requests.Timeout:
    return render_template('index.html', error="Request timed out. The website might be too slow or unavailable.")
```

3. **Add Content Size Limits**:
   - Limit text content size for processing
   - Set maximum processing time for NLP operations

```python
# Example with content limits
if len(response.content) > 5 * 1024 * 1024:  # 5MB limit
    return render_template('index.html', error="The webpage is too large to process.")

if len(text) > 100000:  # 100K character limit for analysis
    text = text[:100000]
    truncated = True
```

4. **Implement Progressive Processing**:
   - Return initial results quickly
   - Process more intensive operations in background

```python
# Example progressive processing
# First phase - quick processing
basic_results = {
    "url": url,
    "title": soup.title.string if soup.title else "No title",
    "text_sample": text[:1000] + "..." if len(text) > 1000 else text,
    "word_count": len(text.split())
}

# Return basic results immediately
response = make_response(render_template('results.html', results=basic_results, processing=True))

# Trigger background processing for intensive operations
background_tasks.process_full_analysis(url, text, task_id)
```

5. **Optimize CPU-Intensive Operations**:
   - Use more efficient NLP libraries
   - Process only necessary text portions
   - Cache intermediate results

---

## Asynchronous Programming Assessment

### Current Async Implementation

The application **does not implement any asynchronous processing**. All operations are synchronous and blocking:

- The Flask route handler processes everything in one synchronous flow
- External HTTP requests are blocking (using `requests.get()`)
- All NLP operations execute sequentially
- File I/O operations for word cloud generation are synchronous
- Template rendering is synchronous

### Blocking Operations Identification

The following blocking operations could be made asynchronous:

1. **External HTTP Requests**:
   - Currently using synchronous `requests.get()`
   - Could use async libraries like `aiohttp` or `httpx`

2. **HTML Parsing**:
   - BeautifulSoup processing is CPU-bound
   - Could be moved to a worker process

3. **NLP Processing**:
   - Text analysis operations are CPU-intensive
   - Could be parallelized or processed asynchronously

4. **Word Cloud Generation**:
   - Image generation is both CPU-intensive and involves file I/O
   - Could be moved to a background task

5. **File Operations**:
   - Writing word cloud files is blocking I/O
   - Could use async file I/O or separate worker

### Async Implementation Recommendations

1. **Convert to FastAPI with Async Support**:

```python
# Example async FastAPI implementation
from fastapi import FastAPI, Request, Form, BackgroundTasks
from fastapi.templating import Jinja2Templates
from fastapi.staticfiles import StaticFiles
import aiohttp
import asyncio

app = FastAPI()
templates = Jinja2Templates(directory="templates")
app.mount("/static", StaticFiles(directory="static"), name="static")

@app.get("/")
async def read_root(request: Request):
    return templates.TemplateResponse("index.html", {"request": request})

@app.post("/analyze")
async def analyze_url(request: Request, background_tasks: BackgroundTasks, url: str = Form(...)):
    # Generate task ID
    task_id = uuid.uuid4().hex
    
    # Quick validation
    if not url.startswith(('http://', 'https://')):
        return templates.TemplateResponse(
            "index.html", 
            {"request": request, "error": "URL must start with http:// or https://"}
        )
    
    # Start async task
    background_tasks.add_task(process_url_async, url, task_id)
    
    # Return processing page with task ID
    return templates.TemplateResponse(
        "processing.html", 
        {"request": request, "task_id": task_id}
    )

async def process_url_async(url: str, task_id: str):
    try:
        # Async HTTP request
        async with aiohttp.ClientSession() as session:
            async with session.get(url, timeout=10) as response:
                html = await response.text()
        
        # Process in thread pool (for CPU-bound operations)
        loop = asyncio.get_event_loop()
        text = await loop.run_in_executor(None, extract_text, html)
        
        # Update task status
        update_task_status(task_id, "text_extracted", {"text": text[:1000]})
        
        # Continue with other processing...
        # ...
    except Exception as e:
        update_task_status(task_id, "error", {"error": str(e)})
```

2. **Implement Worker Queue with Celery**:

```python
# Example Celery integration
from flask import Flask, request, render_template, jsonify
from celery import Celery
import uuid

app = Flask(__name__)
app.config['CELERY_BROKER_URL'] = 'redis://localhost:6379/0'
app.config['CELERY_RESULT_BACKEND'] = 'redis://localhost:6379/0'

celery = Celery(app.name, broker=app.config['CELERY_BROKER_URL'])
celery.conf.update(app.config)

@celery.task(bind=True)
def process_url_task(self, url, task_id):
    self.update_state(state='PROGRESS', meta={'status': 'Fetching URL content'})
    try:
        # Fetch content
        response = requests.get(url, timeout=10)
        
        # Update progress
        self.update_state(state='PROGRESS', meta={'status': 'Parsing HTML'})
        
        # Parse HTML
        soup = BeautifulSoup(response.text, 'html.parser')
        text = ' '.join([p.get_text() for p in soup.find_all('p')])
        
        # Continue with other processing steps
        # ...
        
        return {'status': 'completed', 'result': result_data}
    except Exception as e:
        return {'status': 'error', 'error': str(e)}

@app.route('/', methods=['GET', 'POST'])
def index():
    if request.method == 'POST':
        url = request.form['url']
        task_id = uuid.uuid4().hex
        
        # Start async task
        task = process_url_task.apply_async(args=[url, task_id], task_id=task_id)
        
        # Return task ID for polling
        return jsonify({'task_id': task_id})
        
    return render_template('index.html')

@app.route('/status/<task_id>')
def task_status(task_id):
    task = process_url_task.AsyncResult(task_id)
    if task.state == 'PENDING':
        response = {'status': 'pending'}
    elif task.state == 'PROGRESS':
        response = task.info
    elif task.state == 'SUCCESS':
        response = task.info
    else:
        response = {'status': 'error', 'error': str(task.info)}
    
    return jsonify(response)
```

3. **Implement Thread Pool for CPU-Bound Operations**:

```python
from concurrent.futures import ThreadPoolExecutor

# Create thread pool
executor = ThreadPoolExecutor(max_workers=4)

@app.route('/', methods=['GET', 'POST'])
def index():
    if request.method == 'POST':
        url = request.form['url']
        try:
            # Fetch content synchronously (or with timeout)
            response = requests.get(url, timeout=10)
            soup = BeautifulSoup(response.text, 'html.parser')
            text = ' '.join([p.get_text() for p in soup.find_all('p')])
            
            # Process in parallel using thread pool
            sentiment_future = executor.submit(analyze_sentiment, text)
            summary_future = executor.submit(generate_summary, text)
            entities_future = executor.submit(extract_entities, text)
            wordcloud_future = executor.submit(generate_wordcloud, text)
            
            # Get results from futures
            sentiment = sentiment_future.result()
            summary = summary_future.result()
            entities = entities_future.result()
            wordcloud = wordcloud_future.result()
            
            return render_template('index.html',
                                  url=url,
                                  text=text,
                                  sentiment=sentiment,
                                  summary=summary,
                                  entities=entities,
                                  wordcloud=wordcloud)
        except Exception as e:
            return render_template('index.html', error=str(e))
    
    return render_template('index.html')
```

---

## Request Processing Efficiency

### Request Validation Performance

The application lacks formal request validation, which affects both security and performance:

- No URL validation or sanitization
- No input size limitations
- No format validation
- No protection against malicious inputs

This leads to potentially processing invalid or malicious URLs, wasting server resources.

### Request Processing Flow

The current request processing flow is inefficient:

1. All processing happens after the user submits the form
2. No progressive loading or feedback during processing
3. The entire processing chain must complete before any response
4. No caching of previously processed URLs
5. No optimization for repeated requests

### Middleware and Dependency Impact

The application doesn't use middleware or dependency injection that would affect performance. The Flask application is configured with default settings:

```python
app = Flask(__name__)
# No custom middleware or configurations visible
```

### Performance Optimization Recommendations

1. **Implement Request Validation**:

```python
@app.route('/', methods=['GET', 'POST'])
def index():
    if request.method == 'POST':
        url = request.form.get('url', '')
        
        # Validate URL format
        if not url or not url.startswith(('http://', 'https://')):
            return render_template('index.html', error="Please enter a valid URL starting with http:// or https://")
            
        # Check URL length
        if len(url) > 2048:  # Standard URL length limit
            return render_template('index.html', error="URL is too long")
            
        try:
            # Validate URL before fetching
            parsed_url = urlparse(url)
            if not all([parsed_url.scheme, parsed_url.netloc]):
                return render_template('index.html', error="Invalid URL format")
                
            # Additional validations...
```

2. **Add Request Pre-processing and Optimization**:

```python
def is_valid_content_type(response):
    """Check if content type is processable (text/html)"""
    content_type = response.headers.get('Content-Type', '').lower()
    return 'text/html' in content_type

@app.route('/', methods=['GET', 'POST'])
def index():
    if request.method == 'POST':
        url = request.form['url']
        try:
            # First make a HEAD request to check content type and size
            head_response = requests.head(url, timeout=5)
            
            # Check content type
            if not is_valid_content_type(head_response):
                return render_template('index.html', 
                                     error="URL does not point to a valid HTML page")
                                     
            # Check content length
            content_length = int(head_response.headers.get('Content-Length', 0))
            if content_length > 10 * 1024 * 1024:  # 10MB limit
                return render_template('index.html', 
                                     error="The webpage is too large to process")
                                     
            # Continue with full request...
```

3. **Implement Processing Timeouts**:

```python
def process_with_timeout(func, args=None, kwargs=None, timeout=10):
    """Execute a function with a timeout using ThreadPoolExecutor"""
    args = args or ()
    kwargs = kwargs or {}
    
    with ThreadPoolExecutor(max_workers=1) as executor:
        future = executor.submit(func, *args, **kwargs)
        try:
            return future.result(timeout=timeout)
        except TimeoutError:
            return None  # or raise a specific exception

@app.route('/', methods=['GET', 'POST'])
def index():
    if request.method == 'POST':
        url = request.form['url']
        try:
            # Get content with timeout
            response = process_with_timeout(requests.get, args=[url], timeout=10)
            if response is None:
                return render_template('index.html', error="Request timed out")
                
            # Process content with timeout
            soup = BeautifulSoup(response.text, 'html.parser')
            text = ' '.join([p.get_text() for p in soup.find_all('p')])
            
            # Sentiment analysis with timeout
            sentiment = process_with_timeout(analyze_sentiment, args=[text], timeout=5)
            if sentiment is None:
                sentiment = "Analysis timed out"
                
            # Additional processing...
```

---

## Caching Strategy Implementation

### Current Caching Implementation

The application **has no caching implementation**. Every request results in:

- Fresh HTTP request to the target URL
- Complete re-processing of all text content
- Regeneration of all analysis results
- Creation of a new word cloud image

This creates unnecessary load and duplicate processing.

### Caching Opportunities

Several caching opportunities exist:

1. **URL Content Caching**:
   - Cache the HTML content and extracted text by URL
   - Set appropriate TTL based on content type
   - Avoid re-fetching the same URL repeatedly

2. **Analysis Result Caching**:
   - Cache sentiment analysis, summaries, and other NLP results
   - Create cache keys based on content hash rather than URL
   - Store intermediate processing results

3. **Word Cloud Caching**:
   - Store word cloud images with URL or content hash as key
   - Avoid regenerating for the same or similar content
   - Implement cache expiration and cleanup

4. **Response Caching**:
   - Cache the final rendered HTML for identical requests
   - Use HTTP caching headers for browser-side caching
   - Implement cache invalidation strategy

### Caching Implementation Recommendations

1. **Simple In-Memory URL Content Cache**:

```python
# Simple cache implementation
url_content_cache = {}
MAX_CACHE_ENTRIES = 100
CACHE_TTL = 3600  # 1 hour in seconds

def get_url_content(url):
    """Get URL content with caching"""
    now = time.time()
    
    # Check if URL is in cache and not expired
    if url in url_content_cache:
        timestamp, content = url_content_cache[url]
        if now - timestamp < CACHE_TTL:
            return content
    
    # Fetch new content
    response = requests.get(url, timeout=10)
    content = response.text
    
    # Add to cache, managing size
    if len(url_content_cache) >= MAX_CACHE_ENTRIES:
        # Remove oldest entry
        oldest_url = min(url_content_cache.items(), key=lambda x: x[1][0])[0]
        del url_content_cache[oldest_url]
    
    url_content_cache[url] = (now, content)
    return content
```

2. **Redis-Based Caching for Scalability**:

```python
import redis
import json
import hashlib

# Redis client
redis_client = redis.Redis(host='localhost', port=6379, db=0)

def get_cached_content(url, ttl=3600):
    """Get URL content with Redis caching"""
    cache_key = f"content:{hashlib.md5(url.encode()).hexdigest()}"
    
    # Try to get from cache
    cached_data = redis_client.get(cache_key)
    if cached_data:
        return json.loads(cached_data)
    
    # Fetch new content
    try:
        response = requests.get(url, timeout=10)
        soup = BeautifulSoup(response.text, 'html.parser')
        text = ' '.join([p.get_text() for p in soup.find_all('p')])
        
        # Create cache entry
        data = {
            'url': url,
            'text': text,
            'title': soup.title.string if soup.title else "No title",
            'timestamp': time.time()
        }
        
        # Store in cache with expiration
        redis_client.setex(cache_key, ttl, json.dumps(data))
        
        return data
    except Exception as e:
        # Log error and return None
        print(f"Error fetching URL {url}: {str(e)}")
        return None

def get_cached_analysis(content_hash, ttl=86400):
    """Get cached analysis results"""
    cache_key = f"analysis:{content_hash}"
    
    # Try to get from cache
    cached_analysis = redis_client.get(cache_key)
    if cached_analysis:
        return json.loads(cached_analysis)
    
    return None

def cache_analysis_results(content_hash, analysis_results, ttl=86400):
    """Cache analysis results"""
    cache_key = f"analysis:{content_hash}"
    redis_client.setex(cache_key, ttl, json.dumps(analysis_results))
```

3. **File-Based Word Cloud Caching**:

```python
def get_cached_wordcloud(text, ttl=86400):
    """Get or generate word cloud with file-based caching"""
    # Create content hash for cache key
    content_hash = hashlib.md5(text[:1000].encode()).hexdigest()
    cache_path = os.path.join('static', 'wordcloud_cache')
    os.makedirs(cache_path, exist_ok=True)
    
    # Cached file path
    file_path = os.path.join(cache_path, f"wordcloud_{content_hash}.png")
    
    # Check if cached file exists and is fresh
    if os.path.exists(file_path):
        file_age = time.time() - os.path.getmtime(file_path)
        if file_age < ttl:
            return os.path.basename(file_path)
    
    # Generate new word cloud
    stop_words = set(stopwords.words('english'))
    wordcloud = WordCloud(
        width=800,
        height=400,
        background_color='white',
        stopwords=stop_words,
        min_font_size=10
    ).generate(text)
    
    # Save to cache path
    wordcloud.to_file(file_path)
    
    return os.path.basename(file_path)

def cleanup_wordcloud_cache(max_age=86400, max_files=100):
    """Clean up old word cloud cache files"""
    cache_path = os.path.join('static', 'wordcloud_cache')
    if not os.path.exists(cache_path):
        return
        
    # Get all files with creation times
    files = []
    for filename in os.listdir(cache_path):
        if filename.startswith('wordcloud_'):
            file_path = os.path.join(cache_path, filename)
            files.append((file_path, os.path.getmtime(file_path)))
    
    # Sort by modification time (oldest first)
    files.sort(key=lambda x: x[1])
    
    # Remove excess files
    if len(files) > max_files:
        for file_path, _ in files[:-max_files]:
            try:
                os.remove(file_path)
            except:
                pass
    
    # Remove old files
    cutoff_time = time.time() - max_age
    for file_path, mtime in files:
        if mtime < cutoff_time:
            try:
                os.remove(file_path)
            except:
                pass
```

---

## Scalability & Throughput Analysis

### Concurrent User Handling Capability

The current application has severe limitations for handling concurrent users:

1. **Single-Threaded Processing**: Flask's development server processes one request at a time
2. **Long-Running Requests**: Each request can take several seconds to complete
3. **Resource-Intensive Operations**: High CPU and memory usage per request
4. **No Request Queuing**: No mechanism to handle request backlog
5. **Blocking Operations**: All operations block the processing thread

These factors result in very poor concurrent user handling:

| Scenario | Estimated Concurrent Users |
|----------|----------------------------|
| Development server | 1-2 users |
| Production WSGI (Gunicorn/uWSGI) | 5-10 users |
| With optimization | 20-50 users |

### Load Balancing Readiness

The application is **not ready for load balancing** in its current form:

1. **Stateless Design**: The application is stateless, which is positive for scaling
2. **File System Dependency**: Word cloud images are stored in local file system
3. **No Shared Cache**: No shared caching mechanism across instances
4. **No Session Handling**: No explicit session management (good for scaling)
5. **No Database**: No database scaling considerations needed

### Scalability Recommendations

1. **Implement Production WSGI Server**:

```python
# Example Gunicorn configuration (gunicorn_config.py)
bind = "0.0.0.0:8000"
workers = 4  # (2 * CPU cores) + 1
worker_class = "gthread"
threads = 4
timeout = 120  # Increased timeout for long-running processes
max_requests = 1000
max_requests_jitter = 50
```

2. **Move to Asynchronous Architecture**:

```python
# Example async web server implementation with uvicorn+FastAPI
# server.py
import uvicorn
from fastapi import FastAPI

app = FastAPI()

# Define routes...

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

3. **Implement Task Queue for Processing**:

```python
# Example Celery configuration for task processing
# celery_config.py
broker_url = 'redis://localhost:6379/0'
result_backend = 'redis://localhost:6379/0'
task_serializer = 'json'
result_serializer = 'json'
accept_content = ['json']
worker_prefetch_multiplier = 1  # Disable prefetching for long-running tasks
task_time_limit = 300  # 5 minute time limit
task_soft_time_limit = 240  # 4 minute soft time limit
worker_max_memory_per_child = 200000  # 200MB memory limit per worker
```

4. **Configure for Containerized Deployment**:

```docker
# Example Dockerfile for containerization
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

# Create directories with proper permissions
RUN mkdir -p /app/static/wordcloud_cache && \
    chmod -R 755 /app/static

# Use gunicorn for production
CMD ["gunicorn", "--config", "gunicorn_config.py", "app:app"]
```

5. **Implement Shared Storage for Word Clouds**:

```python
# Example S3 storage for word clouds
import boto3
from io import BytesIO

s3_client = boto3.client(
    's3',
    aws_access_key_id=os.environ.get('AWS_ACCESS_KEY_ID'),
    aws_secret_access_key=os.environ.get('AWS_SECRET_ACCESS_KEY')
)
S3_BUCKET = os.environ.get('S3_BUCKET_NAME')

def save_wordcloud_to_s3(wordcloud, content_hash):
    """Save word cloud image to S3 bucket"""
    img_data = BytesIO()
    wordcloud.to_image().save(img_data, format='PNG')
    img_data.seek(0)
    
    filename = f"wordcloud_{content_hash}.png"
    s3_client.upload_fileobj(img_data, S3_BUCKET, filename)
    
    return f"https://{S3_BUCKET}.s3.amazonaws.com/{filename}"
```

---

## Memory Usage Per Request

### Memory Profile Analysis

The application's memory usage per request is high and potentially problematic:

1. **Full HTML in Memory**: The entire HTML content is loaded into memory
2. **Multiple Text Copies**: The same text is copied multiple times during processing
3. **WordCloud Memory Usage**: Word cloud generation requires significant memory
4. **NLP Libraries Memory**: Multiple NLP libraries with separate memory footprints
5. **No Memory Limiting**: No mechanisms to limit memory consumption

Estimated memory usage patterns:

| Content Size | Estimated Memory Usage |
|--------------|------------------------|
| Small webpage (100KB) | 20-50 MB |
| Medium webpage (1MB) | 100-200 MB |
| Large webpage (5MB+) | 500MB-1GB+ |

### Memory Optimization Recommendations

1. **Implement Memory Limits and Monitoring**:

```python
import resource
import psutil

def set_memory_limit(max_memory_mb=500):
    """Set memory limit for the process"""
    soft, hard = resource.getrlimit(resource.RLIMIT_AS)
    resource.setrlimit(resource.RLIMIT_AS, 
                      (max_memory_mb * 1024 * 1024, hard))

def check_memory_usage():
    """Check current memory usage"""
    process = psutil.Process()
    memory_info = process.memory_info()
    memory_mb = memory_info.rss / 1024 / 1024
    return memory_mb

@app.before_request
def before_request():
    """Check memory before processing request"""
    memory_mb = check_memory_usage()
    if memory_mb > 900:  # 90% of 1GB limit
        # Log warning or take action
        app.logger.warning(f"High memory usage: {memory_mb:.2f} MB")
```

2. **Implement Content Size Limits**:

```python
@app.route('/', methods=['GET', 'POST'])
def index():
    if request.method == 'POST':
        url = request.form['url']
        try:
            # Make HEAD request first
            head_response = requests.head(url, timeout=5)
            
            # Check content length
            content_length = int(head_response.headers.get('Content-Length', 0))
            MAX_CONTENT_SIZE = 5 * 1024 * 1024  # 5MB
            
            if content_length > MAX_CONTENT_SIZE:
                return render_template('index.html', 
                                     error=f"Content too large ({content_length/1024/1024:.1f} MB). Maximum size is 5MB.")
            
            # Proceed with GET request
            response = requests.get(url, timeout=10, stream=True)
            content = b''
            for chunk in response.iter_content(chunk_size=1024 * 1024):  # 1MB chunks
                content += chunk
                if len(content) > MAX_CONTENT_SIZE:
                    return render_template('index.html', 
                                         error=f"Content too large (>5MB). Please try a smaller webpage.")
            
            # Process content
            # ...
```

3. **Use Streaming and Chunked Processing**:

```python
def process_text_in_chunks(text, chunk_size=10000):
    """Process text in chunks to reduce memory usage"""
    results = {
        'word_count': 0,
        'sentiment_sum': 0,
        'chunk_count': 0,
        'entities': []
    }
    
    # Process text in chunks
    for i in range(0, len(text), chunk_size):
        chunk = text[i:i+chunk_size]
        
        # Update word count
        words = chunk.split()
        results['word_count'] += len(words)
        
        # Update sentiment (average later)
        blob = TextBlob(chunk)
        results['sentiment_sum'] += blob.sentiment.polarity
        results['chunk_count'] += 1
        
        # Extract entities (limit total)
        if len(results['entities']) < 100:
            doc = nlp(chunk)
            for ent in doc.ents:
                if len(results['entities']) < 100:
                    results['entities'].append((ent.text, ent.label_))
    
    # Calculate average sentiment
    if results['chunk_count'] > 0:
        results['sentiment'] = results['sentiment_sum'] / results['chunk_count']
    else:
        results['sentiment'] = 0
        
    return results
```

---

## API Performance Benchmarks

### Performance Target Metrics

Based on the application's purpose and user expectations, I recommend the following performance targets:

| Metric | Target Value | Current Value (Estimated) |
|--------|-------------|--------------------------|
| Initial Response Time | < 500ms | N/A (no progressive loading) |
| Complete Analysis Time | < 5 seconds | 5-30+ seconds |
| Memory Usage | < 200MB per request | 100MB-1GB+ |
| Concurrent Users | > 20 | 1-2 |
| Error Rate | < 1% | Unknown (no error tracking) |

### Performance Monitoring Recommendations

1. **Implement Basic Performance Timing**:

```python
import time
from functools import wraps

def timing_decorator(f):
    @wraps(f)
    def wrap(*args, **kw):
        start_time = time.time()
        result = f(*args, **kw)
        end_time = time.time()
        duration = end_time - start_time
        print(f'Function {f.__name__} took {duration:.2f} seconds')
        return result
    return wrap

@timing_decorator
def process_url(url):
    # Processing code
    # ...
    return result

@app.route('/', methods=['GET', 'POST'])
def index():
    if request.method == 'POST':
        url = request.form['url']
        start_time = time.time()
        
        try:
            # Process URL
            result = process_url(url)
            
            # Log total processing time
            processing_time = time.time() - start_time
            app.logger.info(f"Processed {url} in {processing_time:.2f} seconds")
            
            return render_template('index.html', **result)
        except Exception as e:
            error_time = time.time() - start_time
            app.logger.error(f"Error processing {url} after {error_time:.2f} seconds: {str(e)}")
            return render_template('index.html', error=str(e))
```

2. **Add Performance Metrics Logging**:

```python
from datadog import statsd  # Or any other metrics library

def log_performance_metrics(url, metrics):
    """Log performance metrics to monitoring system"""
    # Add URL domain as a tag
    domain = urlparse(url).netloc
    
    # Log total processing time
    statsd.timing('web_analysis.total_time', metrics['total_time'] * 1000, tags=[f'domain:{domain}'])
    
    # Log component times
    statsd.timing('web_analysis.fetch_time', metrics['fetch_time'] * 1000, tags=[f'domain:{domain}'])
    statsd.timing('web_analysis.parse_time', metrics['parse_time'] * 1000, tags=[f'domain:{domain}'])
    statsd.timing('web_analysis.nlp_time', metrics['nlp_time'] * 1000, tags=[f'domain:{domain}'])
    statsd.timing('web_analysis.wordcloud_time', metrics['wordcloud_time'] * 1000, tags=[f'domain:{domain}'])
    
    # Log memory usage
    statsd.gauge('web_analysis.memory_usage_mb', metrics['memory_usage'], tags=[f'domain:{domain}'])
    
    # Log content size
    statsd.gauge('web_analysis.content_size_kb', metrics['content_size'] / 1024, tags=[f'domain:{domain}'])
```

---

## Performance Improvement Roadmap

### Phase 1: Critical Performance Fixes (Week 1)

1. **Implement Request Timeouts and Limits**:
   - Add timeouts for external HTTP requests
   - Implement content size limits
   - Add basic request validation

2. **Implement Simple Caching**:
   - Add in-memory URL content caching
   - Implement word cloud file caching
   - Add cache cleanup mechanism

3. **Optimize Resource Usage**:
   - Limit text processing size
   - Consolidate NLP operations
   - Optimize memory usage with chunking

### Phase 2: Architecture Improvements (Month 1)

1. **Convert to Production Web Server**:
   - Replace Flask development server with Gunicorn
   - Configure proper workers and threads
   - Implement resource limits and monitoring

2. **Implement Asynchronous Processing**:
   - Convert to FastAPI or Flask+Celery
   - Move long-running operations to background tasks
   - Add progressive result loading

3. **Enhance Caching Strategy**:
   - Implement Redis-based shared cache
   - Add multi-level caching (memory, file, database)
   - Implement cache invalidation strategy

### Phase 3: Advanced Optimizations (Month 2+)

1. **Implement Distributed Processing**:
   - Set up worker processes for CPU-intensive tasks
   - Add load balancing for web tier
   - Implement shared storage for word clouds

2. **Add Comprehensive Monitoring**:
   - Implement detailed performance tracking
   - Add alerting for performance issues
   - Create performance dashboards

3. **Optimize for Scale**:
   - Containerize application components
   - Implement auto-scaling configuration
   - Add performance testing suite

## API Performance Summary

The Web-Content-Analysis-main application has significant API performance limitations that would affect user experience and scalability. The single-threaded, synchronous processing model with multiple CPU-intensive operations leads to long response times and limited concurrent user capacity.

The most critical performance improvements needed are:

1. **Asynchronous Processing**: Convert to async architecture with background tasks
2. **Caching Implementation**: Add multi-level caching for content and results
3. **Resource Limits**: Implement content size limits and timeout handling
4. **Production Deployment**: Replace development server with production WSGI server

With these improvements, the application could achieve significantly better response times, handle more concurrent users, and provide a much better user experience while maintaining the same functionality.
