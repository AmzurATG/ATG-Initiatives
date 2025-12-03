# Performance Review Checklist - CONDENSED

## Quick Assessment Form

**Project:** ________________  **Date:** ________  **Reviewer:** ________________  
**Overall Score:** ___/10  **Performance:** ☐ Critical  ☐ Poor  ☐ Adequate  ☐ Good  ☐ Excellent

---

## 1. Database Performance ___/10
- [ ] Efficient queries (no N+1 problems)
- [ ] Proper indexes on frequently queried columns
- [ ] Connection pooling configured
- [ ] Optimal schema design
- [ ] Efficient transaction handling
- [ ] Query execution plans reviewed
- [ ] Pagination implemented for large datasets
- [ ] Database monitoring in place

**Issues:** _________________________________________________________________

---

## 2. API Performance ___/10
- [ ] Fast response times (< 200ms target)
- [ ] Good throughput handling
- [ ] Async operations used appropriately
- [ ] Efficient request processing
- [ ] Response caching implemented
- [ ] No blocking operations
- [ ] Pagination for list endpoints
- [ ] Rate limiting configured

**Issues:** _________________________________________________________________

---

## 3. Frontend Performance ___/10
- [ ] Small bundle size (< 250KB target)
- [ ] Code splitting implemented
- [ ] Lazy loading for routes/components
- [ ] Efficient rendering (no unnecessary re-renders)
- [ ] Optimized images & assets
- [ ] Fast initial load time (< 3s)
- [ ] Good Core Web Vitals (LCP, FID, CLS)
- [ ] Memoization used appropriately

**Issues:** _________________________________________________________________

---

## 4. Caching Strategy ___/10
- [ ] API response caching
- [ ] Database query caching
- [ ] Static asset caching
- [ ] Browser caching configured
- [ ] Cache invalidation strategy
- [ ] Redis/Memcached (if applicable)
- [ ] React Query/SWR for client-side caching
- [ ] Appropriate cache TTL

**Issues:** _________________________________________________________________

---

## 5. Code-Level Performance ___/10
- [ ] Efficient algorithms (appropriate complexity)
- [ ] Optimal data structures
- [ ] No memory leaks
- [ ] Efficient loops & iterations
- [ ] Minimal I/O operations
- [ ] Async/await used correctly
- [ ] No unnecessary computations
- [ ] Proper resource cleanup

**Issues:** _________________________________________________________________

---

## 6. Scalability ___/10
- [ ] Stateless design (horizontal scaling ready)
- [ ] No hard resource limits
- [ ] Handles concurrent requests well
- [ ] Database can scale
- [ ] No single points of failure
- [ ] Load balancing ready
- [ ] Efficient resource utilization
- [ ] Graceful degradation under load

**Issues:** _________________________________________________________________

---

## 7. Network Performance ___/10
- [ ] Minimal HTTP requests
- [ ] Request/response compression (gzip/brotli)
- [ ] CDN usage (if applicable)
- [ ] HTTP/2 enabled
- [ ] Efficient payload sizes
- [ ] WebSocket for real-time (if needed)
- [ ] API batching/GraphQL (if beneficial)
- [ ] Optimized CORS configuration

**Issues:** _________________________________________________________________

---

## 8. Monitoring & Observability ___/10
- [ ] Performance metrics collected
- [ ] Endpoint response time tracking
- [ ] Error rate monitoring
- [ ] Database query monitoring
- [ ] Resource usage tracking (CPU, memory)
- [ ] Alerting configured
- [ ] Logging performance issues
- [ ] APM tool integrated (if available)

**Issues:** _________________________________________________________________

---

## Performance Bottlenecks

| Priority | Bottleneck | Location | Impact | Current | Target |
|----------|------------|----------|--------|---------|--------|
| P__ | | | | | |
| P__ | | | | | |
| P__ | | | | | |
| P__ | | | | | |
| P__ | | | | | |

---

## Slow Endpoints (> 500ms)

| Endpoint | Current Time | Target Time | Issue | Fix |
|----------|--------------|-------------|-------|-----|
| | ms | ms | | |
| | ms | ms | | |
| | ms | ms | | |

---

## Database Query Issues

| Query | Location | Time | Issue | Optimization |
|-------|----------|------|-------|--------------|
| | | ms | | |
| | | ms | | |
| | | ms | | |

---

## Frontend Performance Issues

| Issue | Component/Page | Metric | Current | Target | Fix |
|-------|----------------|--------|---------|--------|-----|
| | | | | | |
| | | | | | |
| | | | | | |

---

## Performance Anti-Patterns Found

- [ ] N+1 query problem
- [ ] Missing database indexes
- [ ] Synchronous blocking calls
- [ ] No caching strategy
- [ ] Large payload sizes
- [ ] Unnecessary re-renders
- [ ] Memory leaks
- [ ] Inefficient algorithms (O(n²) or worse)
- [ ] No connection pooling
- [ ] No pagination
- [ ] Excessive API calls
- [ ] Large bundle sizes

---

## Top 5 Performance Improvements

1. **________________________________** (Impact: H/M/L, Effort: H/M/L)
   - Current: ________  Target: ________  Expected Gain: ________
   - _______________________________________________________________________

2. **________________________________** (Impact: H/M/L, Effort: H/M/L)
   - Current: ________  Target: ________  Expected Gain: ________
   - _______________________________________________________________________

3. **________________________________** (Impact: H/M/L, Effort: H/M/L)
   - Current: ________  Target: ________  Expected Gain: ________
   - _______________________________________________________________________

4. **________________________________** (Impact: H/M/L, Effort: H/M/L)
   - Current: ________  Target: ________  Expected Gain: ________
   - _______________________________________________________________________

5. **________________________________** (Impact: H/M/L, Effort: H/M/L)
   - Current: ________  Target: ________  Expected Gain: ________
   - _______________________________________________________________________

---

## Performance Budget

| Metric | Current | Target | Status |
|--------|---------|--------|--------|
| API Response Time (P95) | ___ms | < 500ms | ☐ Pass ☐ Fail |
| Database Query Time (avg) | ___ms | < 100ms | ☐ Pass ☐ Fail |
| Initial Page Load | ___s | < 3s | ☐ Pass ☐ Fail |
| Time to Interactive | ___s | < 5s | ☐ Pass ☐ Fail |
| Bundle Size (JS) | ___KB | < 250KB | ☐ Pass ☐ Fail |
| LCP (Largest Contentful Paint) | ___s | < 2.5s | ☐ Pass ☐ Fail |
| FID (First Input Delay) | ___ms | < 100ms | ☐ Pass ☐ Fail |
| CLS (Cumulative Layout Shift) | ___ | < 0.1 | ☐ Pass ☐ Fail |

---

## Load Testing Results (if available)

- **Concurrent Users Tested:** _____
- **Requests per Second:** _____
- **Error Rate:** _____%
- **Average Response Time:** _____ms
- **P95 Response Time:** _____ms
- **P99 Response Time:** _____ms
- **Throughput:** _____ req/s
- **Failed at:** _____ concurrent users

---

## Quick Wins (Low effort, High impact)

1. _______________________________________________________________________
2. _______________________________________________________________________
3. _______________________________________________________________________

---

## Performance Maturity Assessment

### Current Level: [____________________]

**Critical (1-2):** Severe performance issues blocking usability  
**Poor (3-4):** Significant performance problems affecting UX  
**Adequate (5-6):** Acceptable performance, room for improvement  
**Good (7-8):** Good performance, minor optimizations needed  
**Excellent (9-10):** Optimized, production-grade performance

### Path to Next Level:
- [ ] _______________________________________________________________________
- [ ] _______________________________________________________________________
- [ ] _______________________________________________________________________

---

## Sign-off

**Reviewer:** ________________  **Date:** ________  **Time Spent:** _______ min
