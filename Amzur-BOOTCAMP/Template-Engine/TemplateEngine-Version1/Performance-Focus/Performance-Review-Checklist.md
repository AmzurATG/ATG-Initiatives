# Performance-Focused Code Review Template - Bootcamp Projects

## Project Information
- **Candidate Name:** [Name]
- **Project Title:** [Project Name]
- **Review Date:** [Date]
- **Reviewer:** [Reviewer Name]
- **Performance Grade:** [A/B/C/D/F]
- **Overall Performance Score:** [X/10]

---

## Backend Performance Review (Python FastAPI + PostgreSQL)

### 1. Database Performance & Optimization
**Score: [X/10]**

#### Performance Checklist:
- [ ] Database query optimization and indexing strategy
- [ ] N+1 query problem prevention
- [ ] Efficient join operations and relationships
- [ ] Database connection pooling implementation
- [ ] Query result caching mechanisms
- [ ] Pagination implementation for large datasets
- [ ] Database transaction optimization
- [ ] Bulk operations instead of individual queries

#### Performance Metrics:
```
[Database query performance analysis - execution times, query plans, etc.]
```

#### Optimization Recommendations:
- [Specific database performance improvements]

---

### 2. API Response Time & Throughput
**Score: [X/10]**

#### Performance Checklist:
- [ ] API response time optimization (< 200ms for simple queries)
- [ ] Async/await implementation for I/O operations
- [ ] Background task processing for heavy operations
- [ ] Request batching and bulk operations
- [ ] Response compression (gzip) implementation
- [ ] HTTP caching headers utilization
- [ ] Rate limiting for performance protection
- [ ] Load balancing readiness

#### Performance Metrics:
```
[API performance benchmarks - response times, throughput, concurrent users]
```

#### Optimization Recommendations:
- [Specific API performance improvements]

---

### 3. Memory Management & Resource Optimization
**Score: [X/10]**

#### Performance Checklist:
- [ ] Memory efficient data structures usage
- [ ] Large file handling optimization
- [ ] Memory leak prevention in long-running processes
- [ ] Garbage collection optimization
- [ ] Resource cleanup and connection management
- [ ] Streaming for large data processing
- [ ] Memory profiling and monitoring
- [ ] Container resource limits consideration

#### Performance Metrics:
```
[Memory usage analysis - heap size, memory leaks, resource utilization]
```

#### Optimization Recommendations:
- [Specific memory optimization improvements]

---

### 4. Caching Strategy & Implementation
**Score: [X/10]**

#### Performance Checklist:
- [ ] Redis/in-memory caching implementation
- [ ] Cache key design and expiration strategy
- [ ] Database query result caching
- [ ] API response caching where appropriate
- [ ] Static file caching optimization
- [ ] Cache invalidation strategy
- [ ] Cache hit ratio monitoring
- [ ] Distributed caching considerations

#### Performance Metrics:
```
[Caching effectiveness - hit ratios, cache performance, storage efficiency]
```

#### Optimization Recommendations:
- [Specific caching strategy improvements]

---

### 5. Concurrent Processing & Scalability
**Score: [X/10]**

#### Performance Checklist:
- [ ] Proper async/await pattern implementation
- [ ] Thread pool optimization for CPU-bound tasks
- [ ] Queue-based task processing (Celery/RQ)
- [ ] Microservice architecture considerations
- [ ] Horizontal scaling preparation
- [ ] Load testing and stress testing
- [ ] Performance monitoring and alerting
- [ ] Auto-scaling configuration readiness

#### Performance Metrics:
```
[Concurrency performance - concurrent user handling, task processing speed]
```

#### Optimization Recommendations:
- [Specific scalability improvements]

---

### 6. AI/ML Performance Optimization
**Score: [X/10]**

#### Performance Checklist:
- [ ] Model inference optimization
- [ ] Batch processing for AI requests
- [ ] Model caching and warm-up strategies
- [ ] GPU utilization optimization (if applicable)
- [ ] Async AI service integration
- [ ] Response streaming for long AI operations
- [ ] AI service timeout and fallback handling
- [ ] Cost optimization for AI API calls

#### Performance Metrics:
```
[AI performance analysis - inference times, batch processing efficiency, cost per request]
```

#### Optimization Recommendations:
- [Specific AI performance improvements]

---

## Frontend Performance Review (React + Vite + Tailwind)

### 1. Bundle Size & Loading Performance
**Score: [X/10]**

#### Performance Checklist:
- [ ] Bundle size optimization and analysis
- [ ] Code splitting implementation
- [ ] Dynamic imports for route-based splitting
- [ ] Tree shaking effectiveness
- [ ] Dead code elimination
- [ ] Vendor bundle optimization
- [ ] Module federation (if applicable)
- [ ] Progressive web app (PWA) optimizations

#### Performance Metrics:
```
[Bundle analysis - size breakdown, loading times, compression ratios]
```

#### Optimization Recommendations:
- [Specific bundle optimization improvements]

---

### 2. Runtime Performance & Rendering
**Score: [X/10]**

#### Performance Checklist:
- [ ] React rendering optimization (useMemo, useCallback, React.memo)
- [ ] Virtual scrolling for large lists
- [ ] Efficient re-render prevention
- [ ] Component lazy loading
- [ ] Image optimization and lazy loading
- [ ] DOM manipulation minimization
- [ ] Event handler optimization
- [ ] Memory leak prevention in components

#### Performance Metrics:
```
[Runtime performance - render times, memory usage, FPS, interaction delays]
```

#### Optimization Recommendations:
- [Specific rendering performance improvements]

---

### 3. Network Performance & Data Fetching
**Score: [X/10]**

#### Performance Checklist:
- [ ] API call optimization and batching
- [ ] Data fetching library usage (React Query, SWR)
- [ ] Request deduplication implementation
- [ ] Optimistic updates for better UX
- [ ] Background data synchronization
- [ ] Offline functionality and caching
- [ ] Progressive data loading
- [ ] Error retry with exponential backoff

#### Performance Metrics:
```
[Network performance - request times, cache hit rates, data transfer efficiency]
```

#### Optimization Recommendations:
- [Specific network performance improvements]

---

### 4. User Experience Performance
**Score: [X/10]**

#### Performance Checklist:
- [ ] Core Web Vitals optimization (LCP, FID, CLS)
- [ ] First Contentful Paint (FCP) optimization
- [ ] Time to Interactive (TTI) improvement
- [ ] Perceived performance through loading states
- [ ] Smooth animations and transitions
- [ ] Responsive design performance
- [ ] Touch/click responsiveness
- [ ] Accessibility performance considerations

#### Performance Metrics:
```
[UX performance - Core Web Vitals scores, user interaction metrics]
```

#### Optimization Recommendations:
- [Specific UX performance improvements]

---

### 5. Asset Optimization & Delivery
**Score: [X/10]**

#### Performance Checklist:
- [ ] Image optimization (WebP, AVIF, responsive images)
- [ ] Font loading optimization
- [ ] CSS optimization and critical CSS
- [ ] JavaScript minification and compression
- [ ] CDN utilization for static assets
- [ ] Service worker implementation
- [ ] HTTP/2 and HTTP/3 optimization
- [ ] Resource hints (preload, prefetch, preconnect)

#### Performance Metrics:
```
[Asset performance - loading times, compression ratios, cache effectiveness]
```

#### Optimization Recommendations:
- [Specific asset optimization improvements]

---

### 6. Mobile Performance Optimization
**Score: [X/10]**

#### Performance Checklist:
- [ ] Mobile-first performance considerations
- [ ] Touch interaction optimization
- [ ] Battery usage optimization
- [ ] Network condition adaptability
- [ ] Reduced motion preferences
- [ ] Mobile-specific image optimization
- [ ] App-like experience (PWA features)
- [ ] Mobile testing and profiling

#### Performance Metrics:
```
[Mobile performance - mobile loading times, touch responsiveness, battery impact]
```

#### Optimization Recommendations:
- [Specific mobile performance improvements]

---

## Performance Testing & Monitoring

### 1. Performance Testing Strategy
**Score: [X/10]**

#### Testing Checklist:
- [ ] Load testing implementation (backend)
- [ ] Stress testing for breaking points
- [ ] Performance regression testing
- [ ] Frontend performance testing (Lighthouse, WebPageTest)
- [ ] Database performance testing
- [ ] API performance benchmarking
- [ ] User journey performance testing
- [ ] Performance CI/CD integration

#### Performance Testing Results:
```
[Performance testing outcomes - load test results, stress test findings]
```

#### Testing Recommendations:
- [Performance testing improvements needed]

---

### 2. Performance Monitoring & Observability
**Score: [X/10]**

#### Monitoring Checklist:
- [ ] Application Performance Monitoring (APM) setup
- [ ] Real User Monitoring (RUM) implementation
- [ ] Performance alerting and thresholds
- [ ] Performance dashboard creation
- [ ] Error rate and performance correlation
- [ ] Business metric performance impact
- [ ] Performance regression detection
- [ ] Performance budget enforcement

#### Monitoring Implementation:
```
[Current monitoring setup and gaps]
```

#### Monitoring Recommendations:
- [Performance monitoring improvements]

---

## Performance Improvement Roadmap

### Critical Performance Issues (Fix Immediately)
- [Performance issues causing user experience problems]

### High-Impact Optimizations (Fix within 1 week)
- [Performance improvements with significant user impact]

### Medium-Impact Optimizations (Fix within 1 month)
- [Performance improvements with moderate benefits]

### Long-term Performance Strategy (2-3 months)
- [Architectural changes and advanced optimization techniques]

---

## Performance Benchmarks & Targets

### Backend Performance Targets
- API Response Time: < 200ms (95th percentile)
- Database Query Time: < 50ms (average)
- Concurrent Users: > 100 simultaneous users
- Memory Usage: < 512MB (production)
- CPU Usage: < 70% (under normal load)

### Frontend Performance Targets
- First Contentful Paint: < 1.5s
- Largest Contentful Paint: < 2.5s
- First Input Delay: < 100ms
- Cumulative Layout Shift: < 0.1
- Bundle Size: < 1MB (gzipped)

### Current vs Target Performance
```
[Performance comparison table showing current metrics vs targets]
```

---

## Performance Learning Resources

### Recommended Performance Training
- [Specific performance optimization courses and tutorials]
- [Performance testing tools and methodologies]
- [Monitoring and observability best practices]

### Performance Tools & Libraries
- [Recommended performance optimization tools]
- [Profiling and debugging tools]
- [Performance testing frameworks]

---

**Overall Performance Assessment:** [Comprehensive performance evaluation summary]

**Performance Recommendation:** [Excellent/Good/Needs Improvement/Poor - with performance-specific justification]

**Performance Focus Areas:** [Key areas requiring immediate attention for performance improvement]
