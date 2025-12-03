# Smart Knowledge Repository - AI Performance & Cost Optimization Review

## Executive Summary

This review evaluates the AI performance optimization and cost efficiency aspects of the Smart Knowledge Repository application. The analysis focuses on response times, cost management strategies, caching implementations, async processing, scaling capabilities, and resource utilization.

**Overall Performance Optimization Score: 3/10 (POOR)**

The Smart Knowledge Repository demonstrates minimal AI performance optimization capabilities with fundamental implementation gaps across all performance dimensions. The application lacks critical optimization strategies including caching mechanisms, asynchronous processing, batch operations, cost monitoring, and performance analytics. While the application functions at a basic level, these limitations significantly impact scalability, response times, and operational costs. Implementing the recommended optimization strategies would dramatically improve performance, reduce costs, and enhance the user experience.

## Critical Performance Optimization Limitations

1. **Absent Caching Strategy (HIGH)**: No caching for embeddings, responses, or vector searches
2. **Synchronous Processing Model (HIGH)**: I/O-bound operations block the request thread
3. **Missing Cost Monitoring (MEDIUM)**: No token usage tracking or cost optimization
4. **Limited Batch Processing (MEDIUM)**: Sequential processing of operations that could be batched
5. **Absent Performance Analytics (MEDIUM)**: No measurement of AI performance metrics

## Detailed Performance & Cost Optimization Analysis

### Response Time & Performance Optimization

**Score: 3/10 (POOR)**

The application demonstrates significant response time issues with no optimization strategies implemented:

```python
# From endpoints.py
@router.post("/retrieve_and_generate", response_model=RetrieveAndGenerateResponse)
def retrieve_and_generate_api(body: RetrieveAndGenerateRequest) -> RetrieveAndGenerateResponse:
    logger.info("/retrieve_and_generate called for query: %s", body.query)
    
    # Create service instances for each request (inefficient)
    embedding_service = EmbeddingService()
    
    # Synchronous processing of potentially slow operations
    answer, relevant_chunks, images = embedding_service.retrieve_and_generate(
        body.query, top_k=body.top_k, document_ids=body.document_ids
    )
    
    # Return response after all processing completes
    return RetrieveAndGenerateResponse(
        answer=answer,
        relevant_chunks=relevant_chunks,
        images=images
    )
```

#### Key Findings:

- **Strengths:**
  - Basic FastAPI implementation with proper response models
  - Logging of key operations for troubleshooting

- **Weaknesses:**
  - Synchronous processing blocks the event loop during I/O operations
  - Services instantiated on each request rather than reused
  - No response time measurement or tracking
  - No use of background tasks for long-running operations
  - Absence of parallel processing for independent operations
  - Missing connection pooling for external services

#### Recommendations:

1. **Convert endpoints to async functions:**

```python
@router.post("/retrieve_and_generate", response_model=RetrieveAndGenerateResponse)
async def retrieve_and_generate_api(body: RetrieveAndGenerateRequest) -> RetrieveAndGenerateResponse:
    logger.info("/retrieve_and_generate called for query: %s", body.query)
    
    # Use dependency injection instead of direct instantiation
    embedding_service = get_embedding_service()
    
    # Move heavy processing to background task or run in thread pool
    from concurrent.futures import ThreadPoolExecutor
    
    with ThreadPoolExecutor() as executor:
        result_future = executor.submit(
            embedding_service.retrieve_and_generate,
            body.query, 
            top_k=body.top_k, 
            document_ids=body.document_ids
        )
        answer, relevant_chunks, images = await asyncio.to_thread(lambda: result_future.result())
    
    return RetrieveAndGenerateResponse(
        answer=answer,
        relevant_chunks=relevant_chunks,
        images=images
    )
```

2. **Implement background task processing for long operations:**

```python
@router.post("/scrape_and_embed")
async def scrape_and_embed_api(
    data: ScrapeAndEmbedRequest,
    background_tasks: BackgroundTasks
) -> dict:
    # Start the scraping process immediately
    url = data.url
    initial_response = {"status": "processing", "url": url}
    
    # Schedule the resource-intensive operations in the background
    background_tasks.add_task(
        embedding_service.scrape_and_embed,
        url,
        data.capture_images
    )
    
    return initial_response
```

3. **Add response time measurement and tracking:**

```python
from time import perf_counter
import statistics

# Store response time metrics in memory (replace with proper metrics storage in production)
response_time_metrics = {
    "retrieve_and_generate": [],
    "scrape_and_embed": [],
    "status": []
}

@app.middleware("http")
async def track_response_time(request: Request, call_next):
    # Start timer
    start_time = perf_counter()
    
    # Process the request
    response = await call_next(request)
    
    # Calculate duration
    duration_ms = (perf_counter() - start_time) * 1000
    
    # Record metrics for endpoints we're tracking
    path = request.url.path
    for endpoint in response_time_metrics:
        if endpoint in path:
            response_time_metrics[endpoint].append(duration_ms)
            # Keep only the last 100 measurements
            if len(response_time_metrics[endpoint]) > 100:
                response_time_metrics[endpoint].pop(0)
    
    # Add timing header to response
    response.headers["X-Process-Time"] = f"{duration_ms:.2f}ms"
    return response
```

### Cost Management & Optimization

**Score: 2/10 (CRITICAL)**

The application shows minimal consideration for cost optimization with OpenAI API calls:

```python
# From llm_service.py
def generate_answer(query: str, context: str) -> str:
    try:
        prompt = f"Context:\n{context}\n\nQuestion: {query}\nAnswer:"
        messages=[
            {"role": "system", "content": "You are a helpful web content analyst."},
            {"role": "user", "content": prompt}
        ]
        
        response = openai_client.chat.completions.create(
            model=MODEL,
            messages=messages,
            temperature=0.7,
            max_tokens=500,
        )
        return response.choices[0].message.content.strip()
    except Exception as e:
        logger.exception("Error generating answer: %s", e)
        return "Sorry, I couldn't generate an answer due to an error."
```

#### Key Findings:

- **Strengths:**
  - Basic error handling for failed API calls
  - Environment variable configuration for API key

- **Weaknesses:**
  - No token usage tracking or logging
  - Missing cost optimization strategies
  - Absent budget management or quota systems
  - No caching of similar requests to reduce API calls
  - Lack of cost-effective model selection logic
  - Repeated API calls for similar content

#### Recommendations:

1. **Implement token usage tracking and logging:**

```python
def generate_answer(query: str, context: str) -> dict:
    try:
        prompt = f"Context:\n{context}\n\nQuestion: {query}\nAnswer:"
        messages=[
            {"role": "system", "content": "You are a helpful web content analyst."},
            {"role": "user", "content": prompt}
        ]
        
        response = openai_client.chat.completions.create(
            model=MODEL,
            messages=messages,
            temperature=0.7,
            max_tokens=500,
        )
        
        # Extract and log token usage
        prompt_tokens = response.usage.prompt_tokens
        completion_tokens = response.usage.completion_tokens
        total_tokens = response.usage.total_tokens
        
        # Log token usage for cost monitoring
        logger.info(
            "LLM API usage - prompt_tokens: %d, completion_tokens: %d, total_tokens: %d",
            prompt_tokens, completion_tokens, total_tokens
        )
        
        # Record metrics in database for analysis
        record_api_usage(
            model=MODEL,
            prompt_tokens=prompt_tokens,
            completion_tokens=completion_tokens,
            total_tokens=total_tokens,
            query_type="generate_answer"
        )
        
        return {
            "content": response.choices[0].message.content.strip(),
            "usage": {
                "prompt_tokens": prompt_tokens,
                "completion_tokens": completion_tokens,
                "total_tokens": total_tokens
            }
        }
    except Exception as e:
        logger.exception("Error generating answer: %s", e)
        return {
            "content": "Sorry, I couldn't generate an answer due to an error.",
            "usage": None
        }
```

2. **Add dynamic model selection based on complexity:**

```python
def select_optimal_model(query: str, context: str) -> str:
    """Select the most cost-effective model based on input complexity."""
    combined_length = len(query) + len(context)
    
    # For simple queries with small context, use a smaller model
    if combined_length < 1000 and not requires_complex_reasoning(query):
        return "gpt-3.5-turbo"
    
    # For moderately complex queries
    if combined_length < 4000:
        return "gpt-3.5-turbo-16k"
    
    # For complex queries with large context
    return "gpt-4" if os.getenv("ENABLE_GPT4", "false").lower() == "true" else "gpt-3.5-turbo-16k"

def requires_complex_reasoning(query: str) -> bool:
    """Determine if a query requires complex reasoning."""
    # Simple heuristic based on question words and patterns
    complex_indicators = [
        "why", "how", "explain", "compare", "analyze", "difference between",
        "relationship", "impact of", "evaluate", "synthesis"
    ]
    
    query_lower = query.lower()
    return any(indicator in query_lower for indicator in complex_indicators)
```

3. **Implement a budget management system:**

```python
class CostManager:
    def __init__(self):
        self.daily_budget = float(os.getenv("DAILY_API_BUDGET", "10.0"))  # Default $10/day
        self.current_spend = 0.0
        self.reset_date = datetime.now().date()
        self.cost_per_1k_tokens = {
            "gpt-3.5-turbo": 0.002,  # $0.002 per 1K input tokens
            "gpt-3.5-turbo-16k": 0.004,  # $0.004 per 1K input tokens
            "gpt-4": 0.03,  # $0.03 per 1K input tokens
        }
        # Load today's spend from persistent storage
        self._load_current_spend()
    
    def calculate_cost(self, model: str, prompt_tokens: int, completion_tokens: int) -> float:
        """Calculate the cost of an API call."""
        # Simplified - actual pricing depends on input vs output tokens
        prompt_cost = (prompt_tokens / 1000) * self.cost_per_1k_tokens.get(model, 0.002)
        completion_cost = (completion_tokens / 1000) * (self.cost_per_1k_tokens.get(model, 0.002) * 2)  # Output tokens often cost more
        return prompt_cost + completion_cost
    
    def record_usage(self, model: str, prompt_tokens: int, completion_tokens: int) -> dict:
        """Record API usage and return budget status."""
        # Reset daily budget if it's a new day
        current_date = datetime.now().date()
        if current_date > self.reset_date:
            self.reset_date = current_date
            self.current_spend = 0.0
        
        # Calculate and add cost
        cost = self.calculate_cost(model, prompt_tokens, completion_tokens)
        self.current_spend += cost
        
        # Save to persistent storage
        self._save_current_spend()
        
        # Return budget status
        remaining = max(0, self.daily_budget - self.current_spend)
        percentage_used = min(100, (self.current_spend / self.daily_budget) * 100)
        
        return {
            "cost": cost,
            "daily_spend": self.current_spend,
            "budget_remaining": remaining,
            "percentage_used": percentage_used,
            "budget_exceeded": self.current_spend > self.daily_budget
        }
    
    def check_budget(self) -> bool:
        """Check if we're within budget."""
        # Reset if it's a new day
        if datetime.now().date() > self.reset_date:
            self.reset_date = datetime.now().date()
            self.current_spend = 0.0
            self._save_current_spend()
            return True
        
        return self.current_spend < self.daily_budget
    
    def _load_current_spend(self):
        """Load current spend from persistent storage."""
        try:
            # In production, load from database or file
            pass
        except Exception:
            logger.exception("Failed to load current spend")
    
    def _save_current_spend(self):
        """Save current spend to persistent storage."""
        try:
            # In production, save to database or file
            pass
        except Exception:
            logger.exception("Failed to save current spend")
```

### Caching Strategy & Implementation

**Score: 2/10 (CRITICAL)**

The application has minimal caching, limited to FAISS index persistence:

```python
# From vectorstore.py
def get_faiss_index() -> faiss.Index:
    global _faiss_index
    if _faiss_index is None:
        if os.path.exists(FAISS_INDEX_PATH):
            _faiss_index = faiss.read_index(FAISS_INDEX_PATH)
        else:
            _faiss_index = faiss.IndexFlatL2(EMBEDDING_DIM)
    return _faiss_index
```

#### Key Findings:

- **Strengths:**
  - Basic caching of FAISS index to avoid reloading

- **Weaknesses:**
  - No caching for API responses or query results
  - Missing embedding result caching despite repeated text
  - No cache key design or invalidation strategy
  - Absence of TTL-based expiration
  - No multi-level caching architecture
  - Missing cache performance monitoring

#### Recommendations:

1. **Implement LRU cache for embeddings:**

```python
from functools import lru_cache

class EmbeddingService:
    # Other methods...
    
    @lru_cache(maxsize=1000)
    def get_cached_embedding(self, text: str) -> List[float]:
        """Get embedding for a single text with caching."""
        result = self.model.encode([text], convert_to_tensor=False)
        return self._to_float_lists(result)[0]
    
    def get_embeddings(self, texts: List[str]) -> List[List[float]]:
        """Get embeddings with single-text cache optimization."""
        if len(texts) == 1:
            return [self.get_cached_embedding(texts[0])]
        
        # For multiple texts, compute without caching
        result = self.model.encode(texts, convert_to_tensor=False)
        return self._to_float_lists(result)
```

2. **Add API response caching with TTL:**

```python
import time
from cachetools import TTLCache

# Initialize cache with TTL (time-to-live) in seconds
api_response_cache = TTLCache(maxsize=1000, ttl=300)  # 5-minute cache

def get_cache_key(query: str, context_id: str = None, top_k: int = 3) -> str:
    """Generate a consistent cache key for similar queries."""
    # Normalize query for better cache hits
    normalized_query = query.lower().strip()
    return f"query:{normalized_query}:context:{context_id}:top_k:{top_k}"

@router.post("/retrieve_and_generate", response_model=RetrieveAndGenerateResponse)
async def retrieve_and_generate_api(body: RetrieveAndGenerateRequest) -> RetrieveAndGenerateResponse:
    logger.info("/retrieve_and_generate called for query: %s", body.query)
    
    # Generate cache key
    cache_key = get_cache_key(
        body.query, 
        ",".join(body.document_ids) if body.document_ids else None,
        body.top_k
    )
    
    # Check cache first
    cached_result = api_response_cache.get(cache_key)
    if cached_result:
        logger.info("Cache hit for query: %s", body.query)
        return cached_result
    
    # If not in cache, compute the result
    embedding_service = EmbeddingService()
    answer, relevant_chunks, images = embedding_service.retrieve_and_generate(
        body.query, top_k=body.top_k, document_ids=body.document_ids
    )
    
    # Create response
    response = RetrieveAndGenerateResponse(
        answer=answer,
        relevant_chunks=relevant_chunks,
        images=images
    )
    
    # Store in cache for future use
    api_response_cache[cache_key] = response
    
    return response
```

3. **Implement multi-level caching architecture:**

```python
import redis
import json
import pickle
from typing import Optional, Any, Dict, List

class CacheManager:
    def __init__(self):
        # Level 1: In-memory cache (fastest, limited size)
        self.memory_cache = TTLCache(maxsize=500, ttl=300)  # 5-minute TTL
        
        # Level 2: Redis cache (medium speed, larger capacity)
        redis_url = os.getenv("REDIS_URL")
        if redis_url:
            try:
                self.redis_client = redis.from_url(redis_url)
                self.redis_enabled = True
            except Exception as e:
                logger.error("Failed to connect to Redis: %s", e)
                self.redis_enabled = False
        else:
            self.redis_enabled = False
        
        # Cache statistics
        self.stats = {
            "memory_hits": 0,
            "memory_misses": 0,
            "redis_hits": 0,
            "redis_misses": 0,
            "database_hits": 0
        }
    
    def get(self, key: str, default: Any = None) -> Optional[Any]:
        """Get item from cache using multi-level lookup."""
        # Try memory cache first (fastest)
        if key in self.memory_cache:
            self.stats["memory_hits"] += 1
            return self.memory_cache[key]
        
        self.stats["memory_misses"] += 1
        
        # If not in memory, try Redis if enabled
        if self.redis_enabled:
            try:
                redis_value = self.redis_client.get(key)
                if redis_value:
                    # Deserialize and also update memory cache
                    value = pickle.loads(redis_value)
                    self.memory_cache[key] = value
                    self.stats["redis_hits"] += 1
                    return value
                self.stats["redis_misses"] += 1
            except Exception:
                logger.exception("Error retrieving from Redis cache")
        
        return default
    
    def set(self, key: str, value: Any, memory_ttl: int = 300, redis_ttl: int = 3600) -> bool:
        """Set item in multi-level cache with different TTLs."""
        # Always set in memory cache
        self.memory_cache[key] = value
        
        # If Redis is enabled, also cache there with longer TTL
        if self.redis_enabled:
            try:
                serialized = pickle.dumps(value)
                self.redis_client.setex(key, redis_ttl, serialized)
                return True
            except Exception:
                logger.exception("Error storing in Redis cache")
                return False
        
        return True
    
    def invalidate(self, key: str) -> None:
        """Invalidate a cache entry from all levels."""
        if key in self.memory_cache:
            del self.memory_cache[key]
        
        if self.redis_enabled:
            try:
                self.redis_client.delete(key)
            except Exception:
                logger.exception("Error deleting from Redis cache")
    
    def get_stats(self) -> Dict[str, int]:
        """Get cache statistics."""
        memory_total = self.stats["memory_hits"] + self.stats["memory_misses"]
        redis_total = self.stats["redis_hits"] + self.stats["redis_misses"]
        
        memory_hit_rate = (self.stats["memory_hits"] / memory_total * 100) if memory_total > 0 else 0
        redis_hit_rate = (self.stats["redis_hits"] / redis_total * 100) if redis_total > 0 else 0
        
        return {
            **self.stats,
            "memory_hit_rate": memory_hit_rate,
            "redis_hit_rate": redis_hit_rate
        }
```

### Scaling & Load Management

**Score: 3/10 (POOR)**

The application has minimal scaling capabilities with architectural limitations:

```python
# From main.py
app = FastAPI(title="Smart Knowledge Repository API")

@app.middleware("http")
async def log_requests(request: Request, call_next):
    logger.info(f"Request: {request.method} {request.url}")
    response = await call_next(request)
    logger.info(f"Response status: {response.status_code}")
    return response

app.include_router(v1_router)
```

#### Key Findings:

- **Strengths:**
  - FastAPI framework supports asynchronous operations
  - Basic logging middleware for request tracking

- **Weaknesses:**
  - No auto-scaling configuration or guidelines
  - Missing load balancing considerations
  - Absent rate limiting implementation
  - No circuit breaker patterns for service resilience
  - SQLite database creates scaling bottleneck
  - Global singletons prevent proper horizontal scaling

#### Recommendations:

1. **Implement rate limiting:**

```python
from fastapi import Depends, HTTPException, status
from fastapi_limiter import FastAPILimiter
from fastapi_limiter.depends import RateLimiter
import redis.asyncio as redis

@app.on_event("startup")
async def startup():
    # Initialize Redis for rate limiting
    redis_url = os.getenv("REDIS_URL", "redis://localhost:6379/0")
    r = await redis.from_url(redis_url, encoding="utf8")
    await FastAPILimiter.init(r)

# Rate limit applied to specific endpoints
@router.post("/retrieve_and_generate", response_model=RetrieveAndGenerateResponse)
async def retrieve_and_generate_api(
    body: RetrieveAndGenerateRequest,
    rate_limit: str = Depends(RateLimiter(times=10, seconds=60))  # 10 requests per minute
) -> RetrieveAndGenerateResponse:
    # ... endpoint implementation ...
```

2. **Add circuit breaker pattern for external services:**

```python
import time
from functools import wraps

class CircuitBreaker:
    def __init__(self, name, failure_threshold=5, reset_timeout=60, fallback_function=None):
        self.name = name
        self.failure_threshold = failure_threshold
        self.reset_timeout = reset_timeout
        self.fallback_function = fallback_function
        self.state = "CLOSED"  # CLOSED, OPEN, HALF-OPEN
        self.failures = 0
        self.last_failure_time = 0
    
    def __call__(self, func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            if self.state == "OPEN":
                # Check if reset timeout has elapsed
                if time.time() - self.last_failure_time >= self.reset_timeout:
                    logger.info(f"Circuit {self.name} trying half-open state")
                    self.state = "HALF-OPEN"
                else:
                    logger.warning(f"Circuit {self.name} is OPEN, using fallback")
                    if self.fallback_function:
                        return self.fallback_function(*args, **kwargs)
                    raise HTTPException(status_code=503, detail="Service temporarily unavailable")
            
            try:
                result = func(*args, **kwargs)
                
                # Success in HALF-OPEN state closes the circuit
                if self.state == "HALF-OPEN":
                    logger.info(f"Circuit {self.name} closing after success in half-open state")
                    self.failures = 0
                    self.state = "CLOSED"
                
                return result
                
            except Exception as e:
                # Record failure
                self.failures += 1
                self.last_failure_time = time.time()
                
                # Check if failure threshold reached
                if self.state == "CLOSED" and self.failures >= self.failure_threshold:
                    logger.warning(f"Circuit {self.name} tripped OPEN after {self.failures} failures")
                    self.state = "OPEN"
                
                # If in HALF-OPEN, immediately go back to OPEN
                if self.state == "HALF-OPEN":
                    logger.warning(f"Circuit {self.name} returning to OPEN after failure in half-open state")
                    self.state = "OPEN"
                
                # Use fallback or raise
                if self.fallback_function:
                    return self.fallback_function(*args, **kwargs)
                raise e
                
        return wrapper

# Apply to OpenAI API calls
def fallback_llm_response(query: str, context: str) -> str:
    """Fallback function when OpenAI API is unavailable."""
    return "I'm sorry, our AI service is currently unavailable. Please try again in a few minutes."

# In llm_service.py
openai_circuit = CircuitBreaker(
    name="openai_api", 
    failure_threshold=3,
    reset_timeout=60,
    fallback_function=fallback_llm_response
)

@openai_circuit
def generate_answer(query: str, context: str) -> str:
    # ... existing implementation ...
```

3. **Create horizontal scaling guidelines:**

```python
"""
Horizontal Scaling Configuration Guide

1. Database Migration
   - Migrate from SQLite to PostgreSQL
   - Configure connection pooling in SQLAlchemy
   - Add read replicas for scaling read operations

2. Stateless API Design
   - Ensure all endpoints are truly stateless
   - Move state to Redis or database
   - Replace global singletons with dependency injection

3. Load Balancer Configuration
   - Set up Nginx or similar as load balancer
   - Configure health checks (/status endpoint)
   - Enable sticky sessions if needed

4. Container Orchestration
   - Dockerize the application with proper health checks
   - Use Kubernetes for container orchestration
   - Configure auto-scaling based on CPU/memory metrics

5. External Services
   - Implement circuit breakers for all external services
   - Add connection pooling for external service clients
   - Configure appropriate timeouts and retries
"""
```

### Efficiency & Resource Optimization

**Score: 3/10 (POOR)**

The application has basic resource utilization but lacks optimization:

```python
# From embedding.py
def generate_embeddings(chunks: List[str]) -> List[List[float]]:
    """Generate embeddings for text chunks."""
    sentence_model = SentenceTransformer(EMBEDDING_MODEL)
    embeddings = sentence_model.encode(chunks, convert_to_tensor=False)
    # Convert from numpy array to list of lists
    return embeddings.tolist()
```

#### Key Findings:

- **Strengths:**
  - Basic model loading works functionally

- **Weaknesses:**
  - No memory optimization for embedding generation
  - Inefficient CPU utilization with sequential processing
  - Missing network bandwidth optimization
  - No background processing for non-critical tasks
  - Absent resource cleanup for models and connections
  - Missing profiling or resource monitoring

#### Recommendations:

1. **Implement batched processing for embeddings:**

```python
def generate_embeddings(chunks: List[str], batch_size: int = 8) -> List[List[float]]:
    """Generate embeddings for text chunks with batching for memory efficiency."""
    sentence_model = SentenceTransformer(EMBEDDING_MODEL)
    all_embeddings = []
    
    # Process in batches to optimize memory usage
    for i in range(0, len(chunks), batch_size):
        batch = chunks[i:min(i + batch_size, len(chunks))]
        batch_embeddings = sentence_model.encode(batch, convert_to_tensor=False)
        all_embeddings.extend(batch_embeddings.tolist())
        
        # Log progress for long operations
        if len(chunks) > batch_size * 2:
            logger.info(f"Embedding progress: {min(i + batch_size, len(chunks))}/{len(chunks)} chunks processed")
    
    return all_embeddings
```

2. **Add memory usage monitoring:**

```python
import os
import psutil
from datetime import datetime

def monitor_resource_usage(operation_name: str):
    """Decorator to monitor resource usage during operations."""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            # Get process
            process = psutil.Process(os.getpid())
            
            # Before measurements
            start_time = time.perf_counter()
            before_memory = process.memory_info().rss / (1024 * 1024)  # MB
            
            # Run function
            result = func(*args, **kwargs)
            
            # After measurements
            end_time = time.perf_counter()
            after_memory = process.memory_info().rss / (1024 * 1024)  # MB
            duration = end_time - start_time
            memory_diff = after_memory - before_memory
            
            # Log metrics
            logger.info(
                f"Resource usage - Operation: {operation_name}, "
                f"Duration: {duration:.2f}s, "
                f"Memory: {before_memory:.2f}MB -> {after_memory:.2f}MB "
                f"(Diff: {memory_diff:+.2f}MB)"
            )
            
            # Record metrics to database or monitoring system
            record_resource_metrics(
                operation=operation_name,
                duration=duration,
                memory_before=before_memory,
                memory_after=after_memory,
                timestamp=datetime.now()
            )
            
            return result
        return wrapper
    return decorator

# Apply to resource-intensive functions
@monitor_resource_usage("generate_embeddings")
def generate_embeddings(chunks: List[str], batch_size: int = 8) -> List[List[float]]:
    # Implementation...
```

3. **Implement proper resource cleanup:**

```python
class ModelManager:
    def __init__(self):
        self.models = {}
        self.last_used = {}
        self.cleanup_threshold = 600  # Seconds (10 minutes)
    
    def get_model(self, model_name: str):
        """Get a model, loading it if necessary."""
        current_time = time.time()
        
        # If model exists and is loaded, update last_used time
        if model_name in self.models:
            self.last_used[model_name] = current_time
            return self.models[model_name]
        
        # Load model based on type
        if model_name == "sentence-transformer":
            self.models[model_name] = SentenceTransformer(EMBEDDING_MODEL)
        elif model_name == "other-model":
            # Load other model types as needed
            pass
        
        # Record usage time
        self.last_used[model_name] = current_time
        return self.models[model_name]
    
    def cleanup_unused_models(self):
        """Cleanup models that haven't been used recently."""
        current_time = time.time()
        models_to_unload = []
        
        for model_name, last_used_time in self.last_used.items():
            if current_time - last_used_time > self.cleanup_threshold:
                models_to_unload.append(model_name)
        
        for model_name in models_to_unload:
            if model_name in self.models:
                # Some models need explicit cleanup
                if hasattr(self.models[model_name], "cleanup"):
                    self.models[model_name].cleanup()
                
                # Remove model from memory
                del self.models[model_name]
                del self.last_used[model_name]
                
                # Force garbage collection
                import gc
                gc.collect()
                
                logger.info(f"Unloaded unused model: {model_name}")

# Schedule periodic cleanup
@app.on_event("startup")
def schedule_model_cleanup():
    model_manager = ModelManager()
    
    async def cleanup_task():
        while True:
            await asyncio.sleep(300)  # Check every 5 minutes
            model_manager.cleanup_unused_models()
    
    asyncio.create_task(cleanup_task())
```

### Monitoring & Analytics

**Score: 2/10 (CRITICAL)**

The application has minimal logging but no structured performance monitoring:

```python
# From main.py
@app.middleware("http")
async def log_requests(request: Request, call_next):
    logger.info(f"Request: {request.method} {request.url}")
    response = await call_next(request)
    logger.info(f"Response status: {response.status_code}")
    return response
```

#### Key Findings:

- **Strengths:**
  - Basic request logging is implemented

- **Weaknesses:**
  - No performance metrics collection
  - Missing AI service usage analytics
  - No cost tracking or trend analysis
  - Absent performance alerting system
  - Missing capacity planning tools
  - Lack of service health monitoring

#### Recommendations:

1. **Implement comprehensive metrics collection:**

```python
import time
from contextlib import contextmanager
from prometheus_client import Counter, Histogram, Gauge

# Define metrics
REQUEST_COUNT = Counter(
    'request_total', 
    'Total number of requests by endpoint and status',
    ['endpoint', 'status']
)

REQUEST_LATENCY = Histogram(
    'request_latency_seconds', 
    'Request latency in seconds by endpoint',
    ['endpoint'],
    buckets=(0.1, 0.25, 0.5, 0.75, 1, 2.5, 5, 7.5, 10, 25, 50, 100)
)

EMBEDDING_LATENCY = Histogram(
    'embedding_latency_seconds',
    'Embedding generation latency in seconds',
    ['model'],
    buckets=(0.1, 0.25, 0.5, 0.75, 1, 2.5, 5, 7.5, 10, 25)
)

LLM_LATENCY = Histogram(
    'llm_latency_seconds',
    'LLM API call latency in seconds',
    ['model'],
    buckets=(0.5, 1, 2, 3, 4, 5, 10, 15, 20)
)

TOKEN_USAGE = Counter(
    'token_usage_total',
    'Total tokens used by model and type',
    ['model', 'token_type']  # token_type: prompt, completion, total
)

API_ERRORS = Counter(
    'api_error_total',
    'API error count by type',
    ['service', 'error_type']
)

MEMORY_USAGE = Gauge(
    'memory_usage_bytes',
    'Memory usage in bytes'
)

@contextmanager
def measure_time(metric, labels=None):
    """Measure execution time of a code block."""
    start_time = time.perf_counter()
    try:
        yield
    finally:
        duration = time.perf_counter() - start_time
        if labels:
            metric.labels(**labels).observe(duration)
        else:
            metric.observe(duration)

# FastAPI middleware for request metrics
@app.middleware("http")
async def metrics_middleware(request: Request, call_next):
    endpoint = request.url.path
    
    with measure_time(REQUEST_LATENCY, {"endpoint": endpoint}):
        response = await call_next(request)
    
    REQUEST_COUNT.labels(
        endpoint=endpoint,
        status=response.status_code
    ).inc()
    
    # Update memory usage periodically (1/100 requests)
    if random.randint(1, 100) == 1:
        process = psutil.Process()
        MEMORY_USAGE.set(process.memory_info().rss)
    
    return response
```

2. **Add cost and usage analytics:**

```python
class AIUsageAnalytics:
    def __init__(self):
        # Define models and their costs per 1000 tokens
        self.models = {
            "gpt-3.5-turbo": {
                "input_cost": 0.0015,
                "output_cost": 0.002,
                "name": "GPT-3.5 Turbo"
            },
            "gpt-4": {
                "input_cost": 0.03,
                "output_cost": 0.06,
                "name": "GPT-4"
            },
            "text-embedding-ada-002": {
                "input_cost": 0.0001,
                "output_cost": 0.0,
                "name": "Ada Embeddings"
            }
        }
        
        # Connect to database or initialize storage
        self.init_storage()
    
    def init_storage(self):
        """Initialize storage for analytics data."""
        # Create tables if not exist
        conn = get_db()
        conn.execute("""
            CREATE TABLE IF NOT EXISTS ai_usage (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
                model TEXT NOT NULL,
                operation TEXT NOT NULL,
                prompt_tokens INTEGER NOT NULL,
                completion_tokens INTEGER DEFAULT 0,
                total_tokens INTEGER NOT NULL,
                estimated_cost REAL NOT NULL,
                user_id TEXT,
                request_id TEXT
            )
        """)
        conn.commit()
        conn.close()
    
    def record_usage(self, model: str, operation: str, prompt_tokens: int, 
                    completion_tokens: int = 0, user_id: str = None, 
                    request_id: str = None) -> Dict:
        """Record API usage and calculate cost."""
        # Calculate cost
        model_info = self.models.get(model, {"input_cost": 0.001, "output_cost": 0.001, "name": "Unknown"})
        input_cost = (prompt_tokens / 1000) * model_info["input_cost"]
        output_cost = (completion_tokens / 1000) * model_info["output_cost"]
        total_cost = input_cost + output_cost
        total_tokens = prompt_tokens + completion_tokens
        
        # Record in database
        conn = get_db()
        conn.execute("""
            INSERT INTO ai_usage 
            (model, operation, prompt_tokens, completion_tokens, total_tokens, estimated_cost, user_id, request_id)
            VALUES (?, ?, ?, ?, ?, ?, ?, ?)
        """, (model, operation, prompt_tokens, completion_tokens, total_tokens, total_cost, user_id, request_id))
        conn.commit()
        conn.close()
        
        # Update Prometheus metrics
        TOKEN_USAGE.labels(model=model, token_type="prompt").inc(prompt_tokens)
        TOKEN_USAGE.labels(model=model, token_type="completion").inc(completion_tokens)
        TOKEN_USAGE.labels(model=model, token_type="total").inc(total_tokens)
        
        return {
            "model": model,
            "model_name": model_info["name"],
            "operation": operation,
            "tokens": {
                "prompt": prompt_tokens,
                "completion": completion_tokens,
                "total": total_tokens
            },
            "cost": {
                "input": input_cost,
                "output": output_cost,
                "total": total_cost
            }
        }
    
    def get_usage_report(self, start_date: datetime = None, end_date: datetime = None, 
                         user_id: str = None, group_by: str = "day") -> Dict:
        """Get usage report for a specified time period."""
        # Build query conditions
        conditions = []
        params = []
        
        if start_date:
            conditions.append("timestamp >= ?")
            params.append(start_date.isoformat())
        
        if end_date:
            conditions.append("timestamp <= ?")
            params.append(end_date.isoformat())
        
        if user_id:
            conditions.append("user_id = ?")
            params.append(user_id)
        
        where_clause = " AND ".join(conditions) if conditions else "1=1"
        
        # Define grouping based on requested granularity
        group_by_clause = {
            "day": "date(timestamp)",
            "hour": "strftime('%Y-%m-%d %H:00:00', timestamp)",
            "month": "strftime('%Y-%m-01', timestamp)",
            "model": "model",
            "operation": "operation"
        }.get(group_by, "date(timestamp)")
        
        # Execute query
        conn = get_db()
        cursor = conn.cursor()
        cursor.execute(f"""
            SELECT 
                {group_by_clause} as period,
                SUM(prompt_tokens) as prompt_tokens,
                SUM(completion_tokens) as completion_tokens,
                SUM(total_tokens) as total_tokens,
                SUM(estimated_cost) as total_cost,
                COUNT(*) as request_count
            FROM ai_usage
            WHERE {where_clause}
            GROUP BY period
            ORDER BY period
        """, params)
        
        results = []
        for row in cursor.fetchall():
            results.append({
                "period": row["period"],
                "prompt_tokens": row["prompt_tokens"],
                "completion_tokens": row["completion_tokens"],
                "total_tokens": row["total_tokens"],
                "total_cost": row["total_cost"],
                "request_count": row["request_count"]
            })
        
        conn.close()
        
        # Calculate totals
        total_prompt = sum(r["prompt_tokens"] for r in results)
        total_completion = sum(r["completion_tokens"] for r in results)
        total_tokens = sum(r["total_tokens"] for r in results)
        total_cost = sum(r["total_cost"] for r in results)
        total_requests = sum(r["request_count"] for r in results)
        
        return {
            "report_period": {
                "start": start_date.isoformat() if start_date else "all",
                "end": end_date.isoformat() if end_date else "all",
                "group_by": group_by
            },
            "totals": {
                "prompt_tokens": total_prompt,
                "completion_tokens": total_completion,
                "total_tokens": total_tokens,
                "total_cost": total_cost,
                "request_count": total_requests
            },
            "data": results
        }
```

3. **Add performance alerting system:**

```python
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart

class AlertManager:
    def __init__(self):
        self.alert_thresholds = {
            "response_time": 5.0,  # seconds
            "error_rate": 0.05,    # 5% errors
            "memory_usage": 0.85,  # 85% of available memory
            "daily_cost": float(os.getenv("COST_ALERT_THRESHOLD", "10.0"))  # dollars
        }
        self.alert_cooldown = {
            "response_time": 3600,  # 1 hour
            "error_rate": 1800,     # 30 minutes
            "memory_usage": 7200,   # 2 hours
            "daily_cost": 86400     # 24 hours
        }
        self.last_alert_time = {
            "response_time": 0,
            "error_rate": 0,
            "memory_usage": 0,
            "daily_cost": 0
        }
    
    def check_alert_threshold(self, metric: str, value: float) -> bool:
        """Check if a metric has crossed its alert threshold."""
        return value > self.alert_thresholds.get(metric, float('inf'))
    
    def should_send_alert(self, metric: str, current_time: float) -> bool:
        """Check if we should send an alert based on cooldown period."""
        cooldown = self.alert_cooldown.get(metric, 3600)  # Default: 1 hour
        return current_time - self.last_alert_time.get(metric, 0) > cooldown
    
    def send_alert(self, subject: str, message: str) -> bool:
        """Send an alert via configured channels."""
        # Log the alert
        logger.warning(f"ALERT: {subject} - {message}")
        
        # Record alert in database
        try:
            conn = get_db()
            conn.execute(
                "INSERT INTO alerts (subject, message, timestamp) VALUES (?, ?, ?)",
                (subject, message, datetime.now().isoformat())
            )
            conn.commit()
            conn.close()
        except Exception:
            logger.exception("Failed to record alert in database")
        
        # Send email if configured
        email_enabled = os.getenv("ALERT_EMAIL_ENABLED", "false").lower() == "true"
        if email_enabled:
            try:
                return self._send_email_alert(subject, message)
            except Exception:
                logger.exception("Failed to send email alert")
        
        return True
    
    def _send_email_alert(self, subject: str, message: str) -> bool:
        """Send an email alert."""
        smtp_server = os.getenv("SMTP_SERVER", "smtp.example.com")
        smtp_port = int(os.getenv("SMTP_PORT", "587"))
        smtp_user = os.getenv("SMTP_USER", "")
        smtp_password = os.getenv("SMTP_PASSWORD", "")
        
        from_email = os.getenv("ALERT_FROM_EMAIL", "alerts@example.com")
        to_emails = os.getenv("ALERT_TO_EMAILS", "admin@example.com").split(",")
        
        msg = MIMEMultipart()
        msg['From'] = from_email
        msg['To'] = ", ".join(to_emails)
        msg['Subject'] = f"[ALERT] Smart Knowledge Repository - {subject}"
        
        msg.attach(MIMEText(message, 'plain'))
        
        with smtplib.SMTP(smtp_server, smtp_port) as server:
            if smtp_user and smtp_password:
                server.starttls()
                server.login(smtp_user, smtp_password)
            server.send_message(msg)
        
        return True
    
    def check_response_time(self, endpoint: str, response_time: float) -> None:
        """Check response time and alert if needed."""
        if self.check_alert_threshold("response_time", response_time):
            current_time = time.time()
            if self.should_send_alert("response_time", current_time):
                self.last_alert_time["response_time"] = current_time
                self.send_alert(
                    f"High Response Time on {endpoint}",
                    f"Response time for {endpoint} is {response_time:.2f}s, "
                    f"which exceeds the threshold of {self.alert_thresholds['response_time']}s."
                )
```

## AI Performance Optimization Roadmap

### Phase 1: Critical Performance Fixes (Weeks 1-2)

1. **Implement Basic Caching Strategy**
   - Add LRU cache for embeddings using `functools.lru_cache`
   - Implement in-memory cache for API responses with TTL
   - Add caching for database query results
   - **Expected Improvement**: 40-60% response time reduction for repeated operations

2. **Convert to Asynchronous Processing**
   - Refactor API endpoints to use async/await
   - Implement background tasks for long-running operations
   - Move CPU-intensive operations to thread pools
   - **Expected Improvement**: 2-3x increase in concurrent request handling

3. **Add Basic Cost Tracking**
   - Implement token usage logging
   - Add cost calculation for API calls
   - Create simple cost reporting endpoint
   - **Expected Improvement**: Visibility into API costs and usage patterns

4. **Optimize Resource Usage**
   - Implement batched processing for embeddings
   - Add proper model cleanup and resource management
   - Optimize memory usage through more efficient processing
   - **Expected Improvement**: 30-50% reduction in memory usage for large operations

### Phase 2: Advanced Optimization (Weeks 3-4)

1. **Implement Multi-Level Caching**
   - Add Redis for distributed caching
   - Implement cache invalidation strategy
   - Add intelligent cache warming for common queries
   - **Expected Improvement**: 60-80% response time reduction and better horizontal scaling

2. **Add Comprehensive Cost Management**
   - Implement dynamic model selection based on complexity
   - Add budget management and usage quotas
   - Create detailed cost analytics dashboard
   - **Expected Improvement**: 30-50% reduction in API costs

3. **Implement Circuit Breakers**
   - Add circuit breaker pattern for external APIs
   - Implement retry logic with exponential backoff
   - Create fallback mechanisms for service disruptions
   - **Expected Improvement**: 90% reduction in cascading failures

4. **Add Performance Monitoring**
   - Implement Prometheus metrics collection
   - Add response time and resource usage tracking
   - Create performance dashboards in Grafana
   - **Expected Improvement**: Visibility into performance bottlenecks

### Phase 3: Strategic Enhancements (Month 2+)

1. **Implement Horizontal Scaling Architecture**
   - Migrate from SQLite to PostgreSQL
   - Implement proper connection pooling
   - Create stateless API design for load balancing
   - **Expected Improvement**: Support for 10-20x more concurrent users

2. **Add Advanced Batch Processing**
   - Implement job queuing system (Celery, RQ, etc.)
   - Add batch processing API endpoints
   - Optimize for throughput of bulk operations
   - **Expected Improvement**: 5-10x processing speed for bulk operations

3. **Implement Smart Rate Limiting**
   - Add user-based rate limiting
   - Implement adaptive throttling based on load
   - Create priority queues for critical operations
   - **Expected Improvement**: Better resource distribution and fairness

4. **Create Advanced Analytics**
   - Implement comprehensive usage and performance analytics
   - Add predictive scaling based on usage patterns
   - Create cost optimization recommendations
   - **Expected Improvement**: Data-driven optimization decisions

## Conclusion

The Smart Knowledge Repository application demonstrates significant performance and cost optimization opportunities. The current implementation provides basic functionality but lacks critical optimization strategies needed for production deployment. The most pressing issues include the absence of caching mechanisms, synchronous processing model, and lack of cost monitoring.

By implementing the recommended optimization roadmap, the application could achieve dramatic improvements in performance (50-80% response time reduction), cost efficiency (30-50% cost reduction), and scalability (10-20x concurrent user capacity). The most impactful immediate improvements would be implementing basic caching for embeddings and API responses, converting to asynchronous processing, and adding cost tracking.

The codebase has a clean architecture that provides a good foundation for these improvements, allowing for incremental implementation without major rewrites. Performance optimization should be prioritized as a critical aspect of making this application production-ready.

**Performance Optimization Recommendation: Requires significant improvement with implementation of caching, asynchronous processing, and cost monitoring mechanisms**
