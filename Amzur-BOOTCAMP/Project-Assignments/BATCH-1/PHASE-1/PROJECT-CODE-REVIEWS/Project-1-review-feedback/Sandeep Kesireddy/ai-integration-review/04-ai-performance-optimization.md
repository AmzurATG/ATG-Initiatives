# AI Performance & Cost Optimization Review

## Overview

This review analyzes the performance and cost optimization aspects of the AIChatBot application, focusing on response time, resource utilization, and strategies to optimize AI service usage. Efficient AI service integration is critical for both user experience and operational costs.

## Response Time & Performance Optimization

### Current Implementation

The AIChatBot implements basic async handling for API calls but lacks comprehensive performance optimization:

```python
# Basic async API call with simple timeout
@app.post("/chat", response_model=ChatResponse)
@limiter.limit("10/minute")
async def chat_endpoint(request: Request, req: ChatRequest):
    # ...existing code...
    
    # Async API call without optimization
    reply = await get_llm_response(
        req.message, provider, conversation_history=user_history
    )
    
    # ...existing code...
```

The frontend implements a basic loading state with a spinner:

```python
# Frontend - synchronous request with spinner
with st.spinner("Thinking..."):
    try:
        resp = httpx.post(
            backend_url,
            json={
                "message": clean_prompt,
                "provider": llm_provider,
                "user_id": user_id,
            },
            timeout=20,
        )
        # ...process response...
```

### Key Issues

1. **Fixed Timeouts**: Static 20-second timeout without adaptation for different query complexities.

2. **No Performance Monitoring**: No tracking of response times or performance metrics.

3. **No Streaming Responses**: Complete responses are loaded before display, increasing perceived latency.

4. **Synchronous Frontend Requests**: Frontend uses blocking requests instead of async patterns.

5. **No Parallel Processing**: No optimization for concurrent requests or operations.

6. **Missing Performance Analysis**: No collection of performance data for optimization.

## Cost Management & Optimization

### Current Implementation

The application has no explicit cost management or token optimization:

```python
# Direct API call without token counting or optimization
async def get_llm_response(message: str, provider: str = "openai", conversation_history: list = None) -> str:
    # ...existing code...

    if provider == "openai":
        openai.api_key = Config.OPENAI_API_KEY
        # Prepare messages for OpenAI
        messages = []
        for history_item in conversation_history or []:
            role = "user" if history_item["role"] == "user" else "assistant"
            messages.append({"role": role, "content": history_item["message"]})
        
        messages.append({"role": "user", "content": message})
        
        # No token counting or optimization
        response = await openai.ChatCompletion.acreate(
            model="gpt-3.5-turbo",
            messages=messages,
        )
        
        return response.choices[0].message.content
    # ...existing code...
```

### Key Issues

1. **No Token Tracking**: No monitoring or tracking of token usage.

2. **No Token Optimization**: No attempts to optimize prompt length or token usage.

3. **No Cost Monitoring**: No reporting or alerting on API usage costs.

4. **No Model Selection Strategy**: Fixed model usage without cost/performance tradeoff consideration.

5. **Full History Inclusion**: Entire conversation history is sent without summarization or truncation.

6. **No Budget Controls**: No implementation of usage limits or budget constraints.

## Caching Strategy & Implementation

### Current Implementation

The application does not implement any caching for API responses:

```python
# No caching implementation for similar or repeat queries
reply = await get_llm_response(
    req.message, provider, conversation_history=user_history
)
```

### Key Issues

1. **Missing Response Caching**: No caching for frequent, similar, or identical queries.

2. **No Cache Invalidation**: No strategy for refreshing cached data when needed.

3. **Missing Cache Strategy**: No consideration of which responses are cacheable.

4. **No Memory Management**: No management of cache size or memory usage.

5. **Missing Cache Analytics**: No tracking of cache hit rates or performance impact.

6. **No Multi-Level Caching**: No implementation of tiered caching strategies.

## Scaling & Load Management

### Current Implementation

The application uses basic FastAPI features without explicit scaling optimizations:

```python
# Simple rate limiting without sophisticated load management
@app.post("/chat", response_model=ChatResponse)
@limiter.limit("10/minute")
async def chat_endpoint(request: Request, req: ChatRequest):
    # ...existing code...
```

### Key Issues

1. **Basic Rate Limiting**: Simple rate limiting without user-specific or dynamic adjustments.

2. **No Load Balancing**: No strategy for distributing load across services.

3. **Missing Circuit Breakers**: No protection against cascading failures.

4. **No Backoff Strategy**: Missing exponential backoff for retries.

5. **Limited Concurrency Control**: No management of concurrent API requests.

6. **No Queue Management**: Missing queue system for handling peak loads.

## Efficiency & Resource Optimization

### Current Implementation

The application loads the entire conversation history on each request:

```python
# Load entire history on every request
with _history_lock:
    history = load_history()
    user_history = history.setdefault(user_id, [])
    # Add the new user message to the history for context
    user_history.append({"role": "user", "message": req.message})
```

### Key Issues

1. **Inefficient History Loading**: Entire conversation history loaded for each request.

2. **No Background Processing**: No offloading of non-critical tasks.

3. **Synchronous File Operations**: File operations block the event loop.

4. **Fixed Worker Configuration**: No optimization of worker processes based on workload.

5. **No Resource Limits**: No explicit limits on memory or CPU usage.

6. **Missing Resource Cleanup**: No explicit cleanup of unused resources.

## Monitoring & Analytics

### Current Implementation

The application has minimal logging but no comprehensive performance monitoring:

```python
# Basic logging without performance metrics
logging.info(f"Received chat request: {req}")
# ...
logging.info(f"Reply: {reply}")
```

### Key Issues

1. **Limited Metrics Collection**: No comprehensive performance metrics collection.

2. **No Cost Tracking**: No monitoring of API usage costs.

3. **Missing Response Time Tracking**: No measurement of response times or latency.

4. **No Performance Dashboards**: No visualization of performance metrics.

5. **Missing Alerting**: No alerts for performance degradation or cost spikes.

6. **No Usage Analytics**: No analysis of patterns to guide optimization.

## Performance & Cost Optimization Metrics

| Metric | Current Score | Baseline | Assessment |
|--------|--------------|----------|------------|
| Response Time | 3/10 | <1s average | Poor - No optimization or measurement |
| Cost Efficiency | 2/10 | Optimized token usage | Poor - No token management or tracking |
| Caching Effectiveness | 1/10 | >50% cache hit ratio | Critical - No caching implemented |
| Resource Utilization | 4/10 | Efficient resource usage | Poor - Basic implementation without optimization |
| Load Handling | 3/10 | Stable under varying load | Poor - Simple rate limiting only |
| Monitoring | 2/10 | Comprehensive metrics | Poor - Minimal logging only |

**Overall Performance & Cost Optimization Score: 2.5/10 (POOR)**

## Performance & Cost Improvement Recommendations

### 1. Implement Token Counting & Optimization

Add token counting and optimization to manage AI service costs:

```python
# filepath: app/utils/token_manager.py

import tiktoken
from typing import List, Dict, Any, Optional, Tuple

class TokenManager:
    def __init__(self):
        # Initialize encoding for different models
        self.encoders = {
            "gpt-3.5-turbo": tiktoken.encoding_for_model("gpt-3.5-turbo"),
            "gpt-4": tiktoken.encoding_for_model("gpt-4"),
            # Default fallback encoding
            "default": tiktoken.get_encoding("cl100k_base")
        }
        
        # Token limits for different models
        self.token_limits = {
            "gpt-3.5-turbo": 4096,  # Context window for GPT-3.5
            "gpt-4": 8192,          # Context window for GPT-4
            "gemini-pro": 30720,    # Approximate for Gemini Pro
            "default": 4096
        }
        
        # Reserve tokens for the response
        self.response_token_reserve = 1000
        
    def get_encoding(self, model: str):
        """Get the appropriate encoding for a model"""
        return self.encoders.get(model, self.encoders["default"])
        
    def count_tokens(self, text: str, model: str = "gpt-3.5-turbo") -> int:
        """Count the number of tokens in a text string"""
        encoding = self.get_encoding(model)
        return len(encoding.encode(text))
        
    def count_message_tokens(self, messages: List[Dict[str, str]], model: str = "gpt-3.5-turbo") -> int:
        """Count tokens in a message list (OpenAI format)"""
        encoding = self.get_encoding(model)
        
        # Base tokens for the messages format
        num_tokens = 0
        
        for message in messages:
            # Add tokens for message format (varies by model)
            num_tokens += 4  # Every message follows <im_start>{role/name}\n{content}<im_end>\n format
            
            for key, value in message.items():
                num_tokens += len(encoding.encode(value))
                if key == "name":  # If there's a name, the role is omitted
                    num_tokens += 1  # Role is always required and always 1 token
                    
        num_tokens += 2  # Every reply is primed with <im_start>assistant
        return num_tokens
        
    def optimize_conversation_history(
        self, 
        history: List[Dict[str, str]], 
        max_tokens: Optional[int] = None,
        model: str = "gpt-3.5-turbo"
    ) -> Tuple[List[Dict[str, str]], int]:
        """
        Optimize conversation history to fit within token limits
        Returns: (optimized_history, token_count)
        """
        if not history:
            return [], 0
            
        # Determine max tokens based on model
        if max_tokens is None:
            max_tokens = self.token_limits.get(model, self.token_limits["default"]) - self.response_token_reserve
            
        # Convert history to OpenAI message format for token counting
        messages = []
        for item in history:
            role = "user" if item.get("role") == "user" else "assistant"
            content = item.get("message", "")
            messages.append({"role": role, "content": content})
            
        # Count tokens in full history
        total_tokens = self.count_message_tokens(messages, model)
        
        # If within limits, return the full history
        if total_tokens <= max_tokens:
            return history, total_tokens
            
        # Otherwise, trim history from the beginning until it fits
        while total_tokens > max_tokens and len(messages) > 1:
            # Remove oldest message
            messages.pop(0)
            
            # Recalculate tokens
            total_tokens = self.count_message_tokens(messages, model)
            
        # Convert back to original format
        optimized_history = []
        for msg in messages:
            role = "user" if msg["role"] == "user" else "assistant"
            optimized_history.append({
                "role": role, 
                "message": msg["content"]
            })
            
        return optimized_history, total_tokens
        
    def estimate_cost(self, input_tokens: int, output_tokens: int = 0, model: str = "gpt-3.5-turbo") -> float:
        """Estimate the cost of a request based on token count"""
        # Cost per 1K tokens (approximate as of last update - check current pricing)
        costs = {
            "gpt-3.5-turbo": {"input": 0.0015, "output": 0.002},  # per 1K tokens
            "gpt-4": {"input": 0.03, "output": 0.06},             # per 1K tokens
            "gemini-pro": {"input": 0.00125, "output": 0.00125},  # approximate
            "default": {"input": 0.0015, "output": 0.002}
        }
        
        model_costs = costs.get(model, costs["default"])
        
        # Calculate cost
        input_cost = (input_tokens / 1000) * model_costs["input"]
        output_cost = (output_tokens / 1000) * model_costs["output"]
        
        return input_cost + output_cost

# Initialize token manager
token_manager = TokenManager()

# In app/services/llm_service.py:
async def get_llm_response(message: str, provider: str = "openai", conversation_history: list = None) -> str:
    model = "gpt-3.5-turbo" if provider == "openai" else "gemini-pro"
    
    # Optimize conversation history to fit token limits
    optimized_history, history_tokens = token_manager.optimize_conversation_history(
        conversation_history or [], 
        model=model
    )
    
    # Count tokens in the current message
    message_tokens = token_manager.count_tokens(message, model=model)
    total_input_tokens = history_tokens + message_tokens
    
    # Log token usage for monitoring
    logging.info(f"Token usage: {total_input_tokens} input tokens for model {model}")
    
    # Estimate cost
    estimated_cost = token_manager.estimate_cost(total_input_tokens, model=model)
    logging.info(f"Estimated request cost: ${estimated_cost:.6f}")
    
    # Proceed with the API call using optimized history
    if provider == "openai":
        # ... existing OpenAI code using optimized_history ...
    elif provider == "gemini":
        # ... existing Gemini code using optimized_history ...
```

### 2. Implement Response Caching

Add caching for AI responses to improve performance and reduce costs:

```python
# filepath: app/services/cache_service.py

import hashlib
import json
import time
from typing import Dict, Any, Optional, Tuple
from datetime import datetime
import logging

class CacheService:
    def __init__(self, max_size: int = 1000, ttl: int = 3600):
        """
        Initialize the cache service
        
        Args:
            max_size: Maximum number of items in cache
            ttl: Default time-to-live in seconds
        """
        self.cache: Dict[str, Dict[str, Any]] = {}
        self.max_size = max_size
        self.default_ttl = ttl
        self.hit_count = 0
        self.miss_count = 0
        
    def _generate_cache_key(self, message: str, provider: str) -> str:
        """Generate a cache key from the request parameters"""
        # Create a normalized representation for cache key generation
        key_data = {
            "message": message.strip().lower(),  # Normalize case and whitespace
            "provider": provider
        }
        
        # Convert to string and hash
        key_string = json.dumps(key_data, sort_keys=True)
        return hashlib.md5(key_string.encode()).hexdigest()
        
    def get(self, message: str, provider: str) -> Tuple[bool, Optional[str]]:
        """
        Try to get a response from cache
        
        Returns:
            Tuple[bool, Optional[str]]: (hit, cached_response)
        """
        cache_key = self._generate_cache_key(message, provider)
        
        if cache_key in self.cache:
            cache_entry = self.cache[cache_key]
            current_time = time.time()
            
            # Check if entry has expired
            if cache_entry["expires_at"] > current_time:
                # Cache hit
                self.hit_count += 1
                logging.info(f"Cache hit for key {cache_key[:8]}...")
                return True, cache_entry["response"]
            else:
                # Expired entry, remove it
                del self.cache[cache_key]
                
        # Cache miss
        self.miss_count += 1
        return False, None
        
    def set(self, message: str, provider: str, response: str, ttl: Optional[int] = None) -> None:
        """Store a response in the cache"""
        # Manage cache size
        if len(self.cache) >= self.max_size:
            # Remove oldest entries (25% of cache)
            entries_to_remove = max(1, int(self.max_size * 0.25))
            oldest_keys = sorted(
                self.cache.keys(), 
                key=lambda k: self.cache[k]["created_at"]
            )[:entries_to_remove]
            
            for key in oldest_keys:
                del self.cache[key]
                
        cache_key = self._generate_cache_key(message, provider)
        current_time = time.time()
        expiration = current_time + (ttl if ttl is not None else self.default_ttl)
        
        self.cache[cache_key] = {
            "response": response,
            "created_at": current_time,
            "expires_at": expiration,
            "provider": provider
        }
        
        logging.info(f"Cached response for key {cache_key[:8]}...")
        
    def get_stats(self) -> Dict[str, Any]:
        """Get cache statistics"""
        total_requests = self.hit_count + self.miss_count
        hit_rate = (self.hit_count / total_requests) * 100 if total_requests > 0 else 0
        
        return {
            "size": len(self.cache),
            "max_size": self.max_size,
            "hit_count": self.hit_count,
            "miss_count": self.miss_count,
            "hit_rate": hit_rate,
            "total_requests": total_requests
        }
        
    def clear(self) -> int:
        """Clear the cache and return number of items cleared"""
        count = len(self.cache)
        self.cache.clear()
        return count
        
    def remove_expired(self) -> int:
        """Remove expired cache entries and return count of removed items"""
        current_time = time.time()
        expired_keys = [
            key for key, entry in self.cache.items() 
            if entry["expires_at"] <= current_time
        ]
        
        for key in expired_keys:
            del self.cache[key]
            
        return len(expired_keys)

# Initialize cache service
cache_service = CacheService()

# In app/services/llm_service.py:
async def get_llm_response(
    message: str, 
    provider: str = "openai", 
    conversation_history: list = None,
    use_cache: bool = True
) -> str:
    # Check if this is a cacheable request
    if use_cache and not conversation_history:
        # Simple queries without conversation context can be cached
        cache_hit, cached_response = cache_service.get(message, provider)
        if cache_hit:
            return cached_response
    
    # Not in cache or not cacheable, proceed with API call
    # ... existing API call code ...
    
    # Cache the response if appropriate
    if use_cache and not conversation_history:
        cache_service.set(message, provider, response_text)
    
    return response_text
```

### 3. Implement Response Time Monitoring

Add response time tracking to monitor and optimize performance:

```python
# filepath: app/services/performance_monitor.py

import time
import statistics
from typing import Dict, Any, List
from datetime import datetime
import logging

class PerformanceMonitor:
    def __init__(self, window_size: int = 100):
        """
        Initialize the performance monitor
        
        Args:
            window_size: Number of requests to keep in the rolling window
        """
        self.window_size = window_size
        self.response_times: Dict[str, List[float]] = {}
        self.token_counts: Dict[str, List[int]] = {}
        self.costs: Dict[str, List[float]] = {}
        
    def record_request(
        self, 
        provider: str, 
        response_time: float, 
        input_tokens: int = 0,
        output_tokens: int = 0,
        cost: float = 0.0,
        cached: bool = False
    ) -> None:
        """Record performance metrics for a request"""
        # Initialize provider entries if needed
        if provider not in self.response_times:
            self.response_times[provider] = []
        if provider not in self.token_counts:
            self.token_counts[provider] = []
        if provider not in self.costs:
            self.costs[provider] = []
            
        # Add metrics
        self.response_times[provider].append(response_time)
        self.token_counts[provider].append(input_tokens + output_tokens)
        self.costs[provider].append(cost)
        
        # Keep only the last window_size entries
        if len(self.response_times[provider]) > self.window_size:
            self.response_times[provider] = self.response_times[provider][-self.window_size:]
        if len(self.token_counts[provider]) > self.window_size:
            self.token_counts[provider] = self.token_counts[provider][-self.window_size:]
        if len(self.costs[provider]) > self.window_size:
            self.costs[provider] = self.costs[provider][-self.window_size:]
            
    def get_metrics(self, provider: Optional[str] = None) -> Dict[str, Any]:
        """Get performance metrics, either for a specific provider or all"""
        metrics = {}
        
        providers = [provider] if provider else self.response_times.keys()
        
        for p in providers:
            if p not in self.response_times:
                continue
                
            # Calculate response time metrics
            p_times = self.response_times[p]
            p_metrics = {
                "count": len(p_times),
                "response_time": {
                    "avg": statistics.mean(p_times) if p_times else 0,
                    "p50": statistics.median(p_times) if p_times else 0,
                    "p90": statistics.quantiles(p_times, n=10)[-1] if len(p_times) >= 10 else None,
                    "p95": statistics.quantiles(p_times, n=20)[-1] if len(p_times) >= 20 else None,
                    "min": min(p_times) if p_times else 0,
                    "max": max(p_times) if p_times else 0
                }
            }
            
            # Add token metrics if available
            if p in self.token_counts and self.token_counts[p]:
                p_metrics["tokens"] = {
                    "avg": statistics.mean(self.token_counts[p]),
                    "total": sum(self.token_counts[p])
                }
                
            # Add cost metrics if available
            if p in self.costs and self.costs[p]:
                p_metrics["cost"] = {
                    "avg": statistics.mean(self.costs[p]),
                    "total": sum(self.costs[p])
                }
                
            metrics[p] = p_metrics
            
        return metrics

# Initialize performance monitor
performance_monitor = PerformanceMonitor()

# In app/main.py:
@app.post("/chat", response_model=ChatResponse)
@limiter.limit("10/minute")
async def chat_endpoint(request: Request, req: ChatRequest):
    # ...existing code...
    
    # Track request start time
    start_time = time.time()
    
    try:
        # Get response from LLM
        reply, meta = await get_llm_response(
            req.message, provider, conversation_history=user_history
        )
        
        # Calculate response time
        response_time = time.time() - start_time
        
        # Record performance metrics
        performance_monitor.record_request(
            provider=provider,
            response_time=response_time,
            input_tokens=meta.get("input_tokens", 0),
            output_tokens=meta.get("output_tokens", 0),
            cost=meta.get("cost", 0.0),
            cached=meta.get("cached", False)
        )
        
        # Log performance data
        logging.info(f"Request processed in {response_time:.2f}s, provider={provider}")
        
        # ...continue with the rest of the function...
        
    except Exception as e:
        # Record failed request
        response_time = time.time() - start_time
        logging.error(f"Request failed after {response_time:.2f}s: {e}")
        raise
```

### 4. Implement Streaming Responses

Add streaming API support to improve user experience and optimize memory usage:

```python
# filepath: app/services/streaming_service.py

from fastapi import WebSocket
from fastapi.responses import StreamingResponse
import json
import asyncio
import logging
from typing import AsyncGenerator, Dict, Any

class StreamingService:
    def __init__(self):
        """Initialize the streaming service"""
        pass
        
    async def stream_llm_response(
        self, 
        message: str, 
        provider: str, 
        conversation_history: list = None
    ) -> AsyncGenerator[str, None]:
        """Stream LLM responses in chunks"""
        try:
            if provider == "openai":
                # Import OpenAI here to avoid circular imports
                import openai
                from app.core.config import Config
                
                openai.api_key = Config.OPENAI_API_KEY
                
                # Prepare messages for OpenAI
                messages = []
                for history_item in conversation_history or []:
                    role = "user" if history_item["role"] == "user" else "assistant"
                    messages.append({"role": role, "content": history_item["message"]})
                
                messages.append({"role": "user", "content": message})
                
                # Create streaming response
                response = await openai.ChatCompletion.acreate(
                    model="gpt-3.5-turbo",
                    messages=messages,
                    stream=True,
                )
                
                collected_chunks = []
                collected_message = ""
                
                async for chunk in response:
                    chunk_message = chunk["choices"][0]["delta"].get("content", "")
                    collected_chunks.append(chunk_message)
                    collected_message += chunk_message
                    
                    # Yield each chunk as it arrives
                    if chunk_message:
                        yield chunk_message
                        
            elif provider == "gemini":
                # For Gemini we need to simulate streaming for now
                # In production, use the Gemini streaming API when available
                import httpx
                from app.core.config import Config
                
                content = []
                for history_item in conversation_history or []:
                    role = "user" if history_item["role"] == "user" else "model"
                    content.append({"role": role, "parts": [{"text": history_item["message"]}]})
                
                content.append({"role": "user", "parts": [{"text": message}]})
                
                # Get full response
                async with httpx.AsyncClient() as client:
                    response = await client.post(
                        "https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent",
                        json={"contents": content},
                        headers={"Authorization": f"Bearer {Config.GEMINI_API_KEY}"}
                    )
                    response.raise_for_status()
                    full_text = response.json()["candidates"][0]["content"]["parts"][0]["text"]
                
                # Simulate streaming by yielding chunks of the text
                chunk_size = max(1, len(full_text) // 20)  # Divide into ~20 chunks
                for i in range(0, len(full_text), chunk_size):
                    chunk = full_text[i:i+chunk_size]
                    yield chunk
                    await asyncio.sleep(0.05)  # Small delay to simulate streaming
                    
            else:
                yield f"Unsupported provider: {provider}"
                
        except Exception as e:
            logging.error(f"Error in streaming: {str(e)}")
            yield f"Error during streaming: {str(e)}"
            
    async def handle_websocket(self, websocket: WebSocket, message: str, provider: str, conversation_history: list = None):
        """Handle streaming responses through WebSocket"""
        await websocket.accept()
        
        try:
            # Stream response chunks through WebSocket
            async for chunk in self.stream_llm_response(message, provider, conversation_history):
                await websocket.send_text(json.dumps({
                    "chunk": chunk,
                    "done": False
                }))
                
            # Send completion signal
            await websocket.send_text(json.dumps({
                "chunk": "",
                "done": True
            }))
            
        except Exception as e:
            logging.error(f"WebSocket error: {str(e)}")
            await websocket.send_text(json.dumps({
                "error": str(e),
                "done": True
            }))
            
        finally:
            await websocket.close()

# Initialize streaming service
streaming_service = StreamingService()

# Add to main.py:
@app.websocket("/ws/chat/{user_id}")
async def websocket_chat(websocket: WebSocket, user_id: str):
    await websocket.accept()
    
    try:
        # Get initial message data
        data = await websocket.receive_text()
        message_data = json.loads(data)
        
        message = message_data.get("message", "")
        provider = message_data.get("provider", "openai")
        
        # Load conversation history
        with _history_lock:
            history = load_history()
            user_history = history.setdefault(user_id, [])
            # Add the new user message
            user_history.append({"role": "user", "message": message})
            history[user_id] = user_history
            save_history(history)
        
        # Start tracking performance
        start_time = time.time()
        
        # Stream the response
        full_response = ""
        async for chunk in streaming_service.stream_llm_response(message, provider, user_history):
            full_response += chunk
            await websocket.send_text(json.dumps({"chunk": chunk, "done": False}))
            
        # Record completion
        response_time = time.time() - start_time
        performance_monitor.record_request(
            provider=provider,
            response_time=response_time
        )
            
        # Update conversation history with full response
        with _history_lock:
            user_history.append({"role": "assistant", "message": full_response})
            history[user_id] = user_history
            save_history(history)
            
        # Send completion message
        await websocket.send_text(json.dumps({"chunk": "", "done": True}))
        
    except Exception as e:
        logging.error(f"WebSocket error: {str(e)}")
        try:
            await websocket.send_text(json.dumps({"error": str(e), "done": True}))
        except:
            pass
            
@app.get("/chat/stream")
async def stream_chat(
    message: str, 
    provider: str = "openai", 
    user_id: str = "default"
):
    """HTTP endpoint for streaming responses"""
    
    async def response_generator():
        # Load conversation history
        with _history_lock:
            history = load_history()
            user_history = history.setdefault(user_id, [])
            # Add the new user message
            user_history.append({"role": "user", "message": message})
            
        # Track performance
        start_time = time.time()
        full_response = ""
            
        # Stream the response
        async for chunk in streaming_service.stream_llm_response(message, provider, user_history):
            full_response += chunk
            yield f"data: {json.dumps({'chunk': chunk, 'done': False})}\n\n"
            
        # Record completion
        response_time = time.time() - start_time
        performance_monitor.record_request(
            provider=provider,
            response_time=response_time
        )
            
        # Update conversation history with full response
        with _history_lock:
            user_history.append({"role": "assistant", "message": full_response})
            history[user_id] = user_history
            save_history(history)
            
        # Send completion message
        yield f"data: {json.dumps({'chunk': '', 'done': True})}\n\n"
        
    return StreamingResponse(
        response_generator(),
        media_type="text/event-stream"
    )
```

### 5. Add Performance Analytics and Dashboard

Implement an analytics endpoint to track and optimize AI usage:

```python
# filepath: app/routes/analytics.py

from fastapi import APIRouter, Depends, HTTPException, Query
from typing import Dict, Any, Optional
from datetime import datetime, timedelta
import json

from app.services.performance_monitor import performance_monitor
from app.services.cache_service import cache_service
from app.utils.token_manager import token_manager

router = APIRouter(prefix="/analytics", tags=["analytics"])

@router.get("/performance")
async def get_performance_metrics(
    provider: Optional[str] = Query(None, description="Filter by provider"),
    period: str = Query("day", description="Time period (hour, day, week, month)")
):
    """Get performance metrics for AI services"""
    metrics = performance_monitor.get_metrics(provider)
    
    # Add cache stats
    cache_stats = cache_service.get_stats()
    
    # Combine data
    return {
        "performance": metrics,
        "cache": cache_stats,
        "timestamp": datetime.utcnow().isoformat(),
        "period": period
    }

@router.get("/cost")
async def get_cost_metrics(
    start_date: Optional[str] = Query(None, description="Start date (YYYY-MM-DD)"),
    end_date: Optional[str] = Query(None, description="End date (YYYY-MM-DD)"),
    provider: Optional[str] = Query(None, description="Filter by provider")
):
    """Get cost metrics for AI service usage"""
    # In a real application, this would query a database of stored metrics
    # For this demo, we'll return the data from the performance monitor
    
    metrics = performance_monitor.get_metrics(provider)
    
    cost_data = {}
    total_cost = 0.0
    
    for p, data in metrics.items():
        if "cost" in data:
            cost_data[p] = data["cost"]
            total_cost += data["cost"].get("total", 0)
    
    return {
        "cost_by_provider": cost_data,
        "total_cost": total_cost,
        "start_date": start_date or (datetime.utcnow() - timedelta(days=30)).strftime("%Y-%m-%d"),
        "end_date": end_date or datetime.utcnow().strftime("%Y-%m-%d"),
        "timestamp": datetime.utcnow().isoformat()
    }

@router.get("/usage")
async def get_usage_metrics(
    period: str = Query("day", description="Time period (day, week, month)"),
    provider: Optional[str] = Query(None, description="Filter by provider")
):
    """Get usage metrics for AI services"""
    metrics = performance_monitor.get_metrics(provider)
    
    usage_data = {}
    total_tokens = 0
    total_requests = 0
    
    for p, data in metrics.items():
        usage_data[p] = {
            "requests": data.get("count", 0),
            "tokens": data.get("tokens", {}).get("total", 0)
        }
        total_tokens += data.get("tokens", {}).get("total", 0)
        total_requests += data.get("count", 0)
    
    return {
        "usage_by_provider": usage_data,
        "total_tokens": total_tokens,
        "total_requests": total_requests,
        "period": period,
        "timestamp": datetime.utcnow().isoformat()
    }

@router.get("/dashboard")
async def get_dashboard_metrics():
    """Get combined metrics for the dashboard"""
    # Get all metrics
    performance = performance_monitor.get_metrics()
    cache_stats = cache_service.get_stats()
    
    # Calculate summary metrics
    total_requests = sum(data.get("count", 0) for data in performance.values())
    avg_response_time = sum(
        data.get("response_time", {}).get("avg", 0) * data.get("count", 0) 
        for data in performance.values()
    ) / total_requests if total_requests > 0 else 0
    
    total_cost = sum(
        data.get("cost", {}).get("total", 0) 
        for data in performance.values()
    )
    
    total_tokens = sum(
        data.get("tokens", {}).get("total", 0) 
        for data in performance.values()
    )
    
    # Cost saved through caching
    estimated_cache_savings = cache_stats.get("hit_count", 0) * (total_cost / total_requests if total_requests > 0 else 0)
    
    return {
        "summary": {
            "total_requests": total_requests,
            "avg_response_time": avg_response_time,
            "total_cost": total_cost,
            "estimated_savings": estimated_cache_savings,
            "total_tokens": total_tokens
        },
        "performance": performance,
        "cache": cache_stats,
        "timestamp": datetime.utcnow().isoformat()
    }

# Add this router to main.py:
from app.routes import analytics
app.include_router(analytics.router)
```

## Performance & Cost Optimization Summary

The AIChatBot application currently has minimal performance optimization and cost management, scoring a low 2.5/10 in these areas. The implementation lacks token counting, response caching, streaming responses, and comprehensive performance monitoring, leading to inefficient resource usage and potentially higher operational costs.

By implementing the recommended improvements:

1. **Token Management**: Add token counting and optimization to control costs and prevent context window overflows.

2. **Response Caching**: Implement caching for similar queries to reduce API calls and improve response times.

3. **Performance Monitoring**: Add comprehensive metrics collection to identify bottlenecks and optimization opportunities.

4. **Streaming Responses**: Implement streaming for AI responses to improve user experience and memory efficiency.

5. **Analytics Dashboard**: Create performance and cost analytics to track usage and guide optimization efforts.

These improvements would transform the application from a basic implementation with poor performance and cost optimization to a sophisticated system capable of efficiently managing AI resources. The optimization strategies would significantly reduce operational costs while improving user experience through faster response times and more responsive interactions.

## Implementation Priorities

1. **CRITICAL**: Token management - prevent excessive costs and context window issues
2. **HIGH**: Response caching - reduce API calls and improve performance
3. **HIGH**: Streaming responses - enhance user experience and memory efficiency
4. **MEDIUM**: Performance monitoring - gain visibility into system performance
5. **MEDIUM**: Analytics dashboard - track usage patterns and optimize resource allocation

With these improvements, the application's performance and cost optimization score could improve from the current 2.5/10 to a projected 8/10, making it not only more cost-effective but also providing a significantly better user experience.