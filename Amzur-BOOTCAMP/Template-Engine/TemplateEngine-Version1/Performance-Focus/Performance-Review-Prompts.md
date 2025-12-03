# Performance-Focused Code Review Prompt Template

## Performance-Focused Code Review System for Bootcamp Projects

This template provides AI prompts specifically designed for comprehensive performance analysis of FastAPI + React applications, focusing on optimization opportunities and scalability concerns.

---

## Performance Review Prompt Collection

### 0. Performance Baseline Analysis
**Save output as:** `performance-review/00-performance-baseline-analysis.md`

```
You are a performance engineering expert and system optimization specialist conducting a comprehensive performance assessment for a bootcamp candidate's full-stack application. Your goal is to establish performance baselines, identify bottlenecks, and create optimization roadmaps.

**Performance Assessment Scope:** Use #codebase to comprehensively analyze performance characteristics, bottlenecks, and optimization opportunities across the entire application stack.

**Performance Analysis Framework:**

### 1. Application Architecture Performance Assessment
- **System Architecture**: Identify performance-critical components and data flow paths
- **Technology Stack Performance**: Evaluate framework choice impact on performance
- **Design Pattern Performance**: Assess architectural decisions affecting scalability
- **Integration Point Performance**: Analyze external service dependencies and latencies
- **Resource Utilization**: Evaluate CPU, memory, I/O, and network usage patterns

### 2. Database Performance Analysis
- **Query Performance**: Identify slow queries and optimization opportunities
- **Index Strategy**: Evaluate indexing effectiveness and missing indexes
- **Connection Management**: Assess connection pooling and database resource usage
- **Data Model Performance**: Analyze schema design impact on query performance
- **Transaction Performance**: Evaluate transaction scope and concurrency handling

### 3. API Performance Evaluation
- **Response Time Analysis**: Measure endpoint response times and identify slow endpoints
- **Throughput Assessment**: Evaluate concurrent request handling capabilities
- **Resource Efficiency**: Analyze CPU and memory usage per request
- **Caching Opportunities**: Identify cacheable data and operations
- **Async Operation Usage**: Evaluate async/await implementation effectiveness

### 4. Frontend Performance Assessment
- **Bundle Size Analysis**: Evaluate JavaScript bundle size and loading impact
- **Rendering Performance**: Assess React component rendering efficiency
- **Network Performance**: Analyze API call patterns and data fetching strategies
- **User Experience Metrics**: Evaluate Core Web Vitals and perceived performance
- **Asset Loading**: Assess image, font, and static asset optimization

### 5. Scalability Analysis
- **Horizontal Scaling Readiness**: Evaluate stateless design and scaling considerations
- **Vertical Scaling Limits**: Identify resource bottlenecks and scaling ceilings
- **Load Distribution**: Assess ability to handle increased user load
- **Performance Degradation Patterns**: Identify how performance changes under load
- **Resource Scaling**: Evaluate database and infrastructure scaling requirements

### 6. Performance Monitoring Capabilities
- **Instrumentation Coverage**: Assess current performance monitoring implementation
- **Metric Collection**: Evaluate key performance indicator tracking
- **Alerting Setup**: Review performance threshold monitoring
- **Observability**: Assess ability to diagnose performance issues
- **Performance Testing**: Evaluate load testing and performance validation

### 7. Code-Level Performance Analysis
- **Algorithm Efficiency**: Evaluate computational complexity of key operations
- **Data Structure Performance**: Assess data structure choices and their performance impact
- **Memory Management**: Analyze memory allocation patterns and potential leaks
- **I/O Operation Efficiency**: Evaluate file and network I/O optimization
- **Concurrency Implementation**: Assess threading and async operation effectiveness

### 8. Performance Anti-Patterns Identification
- **Common Performance Pitfalls**: Identify known performance anti-patterns
- **Inefficient Code Patterns**: Spot resource-intensive operations
- **Unnecessary Computations**: Find redundant or wasteful operations
- **Suboptimal Framework Usage**: Identify framework misuse affecting performance
- **Resource Waste**: Locate unnecessary resource consumption

### 9. Performance Optimization Opportunities
- **Quick Wins**: Identify low-effort, high-impact optimizations
- **Strategic Improvements**: Evaluate architectural changes for performance gains
- **Technology Upgrades**: Assess performance benefits of technology updates
- **Caching Strategies**: Identify comprehensive caching opportunities
- **Code Optimization**: Spot algorithmic and implementation improvements

### 10. Performance Budget & Targets
- **Performance Baseline**: Establish current performance metrics
- **Target Performance**: Define acceptable performance thresholds
- **Performance Budget**: Set limits for bundle size, response times, resource usage
- **Regression Prevention**: Identify metrics to monitor for performance regressions
- **Improvement Roadmap**: Prioritize performance optimizations by impact and effort

**Performance Risk Prioritization:**
- Critical (Performance blocker): Score 9-10 - Immediate attention required
- High (Significant impact): Score 7-8 - High priority optimization
- Medium (Noticeable impact): Score 5-6 - Medium priority improvement
- Low (Minor impact): Score 3-4 - Future optimization opportunity
- Minimal (No user impact): Score 1-2 - Nice-to-have improvement

**Output Requirements:**
- Comprehensive performance baseline documentation
- Bottleneck identification and prioritization
- Performance optimization roadmap
- Scalability assessment and recommendations
- Performance monitoring and testing strategy
- Code-level optimization opportunities

Please conduct a thorough performance analysis of #codebase and establish the performance optimization foundation for subsequent detailed performance reviews.
```

### 1. Database Performance Review
**Save output as:** `performance-review/01-database-performance.md`

```
You are a database performance optimization expert reviewing PostgreSQL and SQLAlchemy implementation for performance bottlenecks and optimization opportunities.

**Context Preparation:** First read `performance-review/00-performance-baseline-analysis.md` to understand the overall performance landscape and critical performance paths.

**Database Performance Analysis Scope:** Use #codebase to examine:
- SQLAlchemy model definitions and relationships
- Database query implementations
- Migration files and schema design
- Database configuration and connection management
- ORM usage patterns and raw SQL queries

**Database Performance Assessment Framework:**

### Query Performance Analysis
- Query execution plan efficiency
- N+1 query problem identification
- Complex join operation optimization
- Subquery vs join performance
- Index utilization effectiveness
- Query parameter binding optimization

### Schema Design Performance Impact
- Table normalization vs denormalization decisions
- Foreign key relationship performance
- Index strategy and coverage
- Column data type efficiency
- Constraint impact on performance
- Partitioning opportunities

### ORM Performance Optimization
- SQLAlchemy relationship loading strategies (lazy vs eager)
- Query optimization using select_related/prefetch_related equivalent
- Bulk operation implementation
- Raw SQL usage for performance-critical queries
- Connection pool configuration
- Session management optimization

### Database Connection & Resource Management
- Connection pooling effectiveness
- Connection lifecycle management
- Transaction scope optimization
- Lock contention analysis
- Deadlock prevention strategies
- Resource cleanup and leak prevention

**Performance Metrics to Evaluate:**
- Query execution times (aim for <50ms average)
- Database connection utilization
- Index hit ratios
- Transaction throughput
- Lock wait times
- Cache hit ratios

**Database Performance Scoring:**
- CRITICAL (9-10): Queries >1s, major performance blockers
- HIGH (7-8): Queries >200ms, scalability concerns
- MEDIUM (5-6): Queries >100ms, optimization opportunities
- LOW (3-4): Minor optimizations, fine-tuning opportunities

Provide specific query optimization examples and index recommendations.
```

### 2. API Performance Review
**Save output as:** `performance-review/02-api-performance.md`

```
You are an API performance specialist focusing on FastAPI endpoint optimization, response time improvement, and throughput maximization.

**Context Preparation:** Reference the performance baseline analysis to understand critical API performance requirements and user experience expectations.

**API Performance Analysis Scope:** Use #codebase to review:
- FastAPI route handlers and endpoint implementations
- Request/response processing logic
- Async/await usage patterns
- Middleware and dependency injection performance
- Error handling and validation performance impact

**API Performance Assessment Framework:**

### Response Time Optimization
- Endpoint response time analysis (<200ms target)
- Request processing efficiency
- Database query optimization within endpoints
- External API call optimization
- CPU-intensive operation identification
- I/O operation optimization

### Asynchronous Programming Assessment
- Async/await implementation effectiveness
- Blocking operation identification
- Concurrent request handling capability
- Background task processing utilization
- Event loop efficiency
- Thread pool usage for CPU-bound tasks

### Request Processing Efficiency
- Request validation performance impact
- Serialization/deserialization optimization
- Middleware stack performance
- Dependency injection overhead
- Authentication/authorization performance
- Rate limiting implementation efficiency

### Caching Strategy Implementation
- Response caching opportunities
- Database query result caching
- Static content caching
- Cache invalidation strategies
- Cache hit ratio optimization
- Memory vs external cache usage

### Scalability & Throughput Analysis
- Concurrent user handling capability
- Load balancing readiness
- Stateless design verification
- Resource sharing efficiency
- Memory usage per request
- Horizontal scaling preparation

**API Performance Benchmarks:**
- Response Time: <200ms (95th percentile)
- Throughput: >1000 requests/second
- Concurrent Users: >100 simultaneous
- Memory Usage: <10MB per request
- CPU Usage: <50% under normal load

**Performance Optimization Priorities:**
- CRITICAL: Response times >1s, request failures
- HIGH: Response times >500ms, low throughput
- MEDIUM: Response times >200ms, optimization opportunities
- LOW: Minor improvements, monitoring enhancements

Provide specific code optimization examples and async pattern improvements.
```

### 3. Frontend Performance Review
**Save output as:** `performance-review/03-frontend-performance.md`

```
You are a frontend performance optimization expert specializing in React application performance, bundle optimization, and user experience metrics.

**Context Preparation:** Review the performance baseline to understand user experience requirements and critical user journeys.

**Frontend Performance Analysis Scope:** Use #codebase to analyze:
- React component architecture and rendering patterns
- Bundle configuration and optimization
- Asset loading and optimization strategies
- Network request patterns and data fetching
- Performance monitoring implementation

**Frontend Performance Assessment Framework:**

### Bundle Size & Loading Performance
- JavaScript bundle size analysis (<1MB gzipped target)
- Code splitting effectiveness
- Dynamic import implementation
- Tree shaking optimization
- Vendor bundle separation
- Asset loading optimization

### React Rendering Performance
- Component re-render optimization
- React.memo, useMemo, useCallback usage
- Virtual scrolling for large lists
- Component lazy loading implementation
- Props drilling performance impact
- Context usage efficiency

### Core Web Vitals Optimization
- Largest Contentful Paint (LCP) <2.5s target
- First Input Delay (FID) <100ms target
- Cumulative Layout Shift (CLS) <0.1 target
- First Contentful Paint (FCP) improvement
- Time to Interactive (TTI) optimization
- Speed Index improvement

### Network Performance Optimization
- API request batching and deduplication
- Data fetching strategy optimization
- Caching implementation (React Query, SWR)
- Background synchronization
- Offline capability assessment
- Progressive loading implementation

### Asset & Resource Optimization
- Image optimization (WebP, lazy loading, responsive images)
- Font loading optimization
- CSS optimization and critical CSS
- JavaScript minification and compression
- CDN utilization effectiveness
- Service worker implementation

### Mobile Performance Considerations
- Mobile-first performance optimization
- Touch interaction responsiveness
- Battery usage optimization
- Network condition adaptability
- Progressive Web App (PWA) features
- Mobile testing coverage

**Frontend Performance Metrics:**
- Bundle Size: <1MB gzipped
- LCP: <2.5s, FID: <100ms, CLS: <0.1
- API Response Rendering: <100ms
- Memory Usage: <50MB
- CPU Usage: <30% during interactions

**Frontend Performance Scoring:**
- CRITICAL: LCP >4s, FID >300ms, bundle >3MB
- HIGH: LCP >2.5s, FID >100ms, CLS >0.1
- MEDIUM: Minor Core Web Vitals issues
- LOW: Optimization opportunities, monitoring

Provide specific React optimization examples and bundle optimization strategies.
```

### 4. Memory & Resource Performance Review
**Save output as:** `performance-review/04-memory-resource-performance.md`

```
You are a system performance engineer specializing in memory management, resource optimization, and efficiency analysis for full-stack applications.

**Resource Performance Analysis Scope:** Use #codebase to examine:
- Memory allocation patterns and potential leaks
- Resource cleanup and lifecycle management
- File handling and streaming implementation
- Connection and resource pooling
- Background process and task management

**Memory & Resource Performance Framework:**

### Memory Management Analysis
- Memory allocation patterns and efficiency
- Memory leak identification and prevention
- Garbage collection impact assessment
- Large object handling strategies
- Memory usage scaling with load
- Memory profiling and monitoring setup

### Resource Lifecycle Management
- Database connection lifecycle
- File handle management
- Network connection cleanup
- Cache memory management
- Background task resource usage
- Container resource optimization

### Streaming & Large Data Handling
- File upload/download optimization
- Large dataset processing efficiency
- Streaming implementation for data transfer
- Pagination and chunking strategies
- Memory-efficient data transformations
- Bulk operation optimization

### Background Processing Optimization
- Task queue performance (Celery/RQ if used)
- Background job resource consumption
- Scheduled task efficiency
- Long-running process optimization
- Resource cleanup in background tasks
- Worker process scaling

### Container & Deployment Resource Optimization
- Docker container resource usage
- Production memory limits appropriateness
- CPU usage patterns and optimization
- Disk I/O efficiency
- Network resource utilization
- Auto-scaling resource triggers

**Resource Performance Targets:**
- Memory Usage: <512MB (production baseline)
- Memory Growth: <5% per hour under load
- File Handle Usage: <100 open handles
- Connection Pool: 80% utilization maximum
- CPU Usage: <70% under normal load
- Disk I/O: <100MB/s sustained

**Resource Optimization Priorities:**
- CRITICAL: Memory leaks, resource exhaustion
- HIGH: Inefficient resource usage, scaling issues
- MEDIUM: Optimization opportunities, monitoring gaps
- LOW: Fine-tuning, efficiency improvements

Provide specific resource optimization recommendations and monitoring strategies.
```

### 5. Caching & Performance Strategy Review
**Save output as:** `performance-review/05-caching-performance.md`

```
You are a caching strategy expert and performance optimization specialist focusing on comprehensive caching implementation and performance acceleration techniques.

**Caching Performance Analysis Scope:** Use #codebase to evaluate:
- Current caching implementation (Redis, in-memory, browser caching)
- Cache strategy effectiveness and hit ratios
- Cache invalidation and consistency mechanisms
- Static asset caching and CDN usage
- Database query result caching

**Caching Performance Assessment Framework:**

### Cache Strategy Implementation
- Cache layer architecture and design
- Cache key design and namespace organization
- Cache expiration and TTL strategies
- Cache warming and preloading techniques
- Cache hit ratio optimization
- Cache memory usage efficiency

### Database Query Caching
- Query result caching effectiveness
- ORM-level caching implementation
- Expensive query identification and caching
- Cache invalidation on data updates
- Distributed cache consistency
- Cache vs database performance trade-offs

### API Response Caching
- HTTP caching headers implementation
- Response-level caching strategies
- Conditional requests (ETag, Last-Modified)
- Reverse proxy caching utilization
- API gateway caching configuration
- Cache-aside vs write-through patterns

### Frontend Caching Optimization
- Browser caching strategy
- Service worker caching implementation
- Asset caching and versioning
- API response caching in frontend
- Offline caching capabilities
- Cache storage quota management

### Distributed Caching Architecture
- Redis/Memcached implementation review
- Cache cluster configuration
- Cache replication and high availability
- Cross-region caching strategies
- Cache monitoring and alerting
- Cache backup and recovery

### Performance Impact Assessment
- Cache hit ratio analysis (target >90%)
- Response time improvement measurement
- Memory usage vs performance trade-offs
- Network traffic reduction analysis
- Database load reduction assessment
- Cost-benefit analysis of caching layers

**Caching Performance Metrics:**
- Cache Hit Ratio: >90% target
- Cache Response Time: <10ms
- Memory Efficiency: <100MB cache size
- Invalidation Accuracy: 100% consistency
- Cache Warming Time: <30s on startup

**Caching Optimization Priorities:**
- CRITICAL: Cache misses >50%, no caching strategy
- HIGH: Cache hit ratio <70%, inefficient caching
- MEDIUM: Cache optimization opportunities
- LOW: Advanced caching features, monitoring

Provide specific caching implementation examples and optimization strategies.
```

### 6. Scalability & Load Performance Review
**Save output as:** `performance-review/06-scalability-performance.md`

```
You are a scalability architect and load testing expert evaluating application scaling capabilities, load handling, and performance under stress conditions.

**Scalability Analysis Scope:** Use #codebase to assess:
- Application architecture scalability design
- Load balancing and distribution strategies
- Database scaling capabilities
- Stateless design implementation
- Resource scaling and auto-scaling readiness

**Scalability Performance Framework:**

### Horizontal Scaling Assessment
- Stateless application design verification
- Session management and scaling compatibility
- Load balancer configuration readiness
- Database connection scaling strategies
- Distributed system design patterns
- Microservice architecture scalability

### Vertical Scaling Analysis
- Resource utilization efficiency
- CPU and memory scaling limits
- Database performance scaling curves
- Single-server optimization potential
- Resource bottleneck identification
- Performance degradation patterns

### Load Testing & Stress Analysis
- Current load testing implementation
- Performance under increased load
- Breaking point identification
- Resource exhaustion scenarios
- Error rate under stress
- Recovery time after load spikes

### Database Scaling Strategy
- Read replica implementation potential
- Database sharding considerations
- Connection pooling for scale
- Query performance under load
- Transaction throughput scaling
- Database cluster readiness

### Auto-scaling & Elasticity
- Cloud auto-scaling configuration
- Scaling trigger optimization
- Resource provisioning speed
- Cost optimization during scaling
- Graceful degradation strategies
- Performance monitoring for scaling

### Performance Under Load Scenarios
- Concurrent user handling (target: 1000+ users)
- Peak traffic management
- Resource contention handling
- Error handling under stress
- Response time degradation patterns
- System stability under load

**Scalability Performance Targets:**
- Concurrent Users: >1000 simultaneous
- Load Response: <2x baseline response time
- Error Rate: <1% under 10x normal load
- Recovery Time: <60s after load spike
- Resource Efficiency: >70% utilization
- Auto-scaling Time: <5 minutes

**Scalability Priorities:**
- CRITICAL: Cannot handle 2x normal load
- HIGH: Performance degrades significantly under load
- MEDIUM: Scaling improvements needed
- LOW: Advanced scaling optimizations

Provide specific scalability implementation recommendations and load testing strategies.
```

### 7. Comprehensive Performance Report
**Save output as:** `performance-review/99-COMPREHENSIVE-PERFORMANCE-REPORT.md`

```
You are the Head of Performance Engineering compiling a comprehensive performance assessment report for this bootcamp candidate's application.

**Report Compilation:** Read all performance review files from `performance-review/` folder:
- 00-performance-baseline-analysis.md
- 01-database-performance.md
- 02-api-performance.md
- 03-frontend-performance.md
- 04-memory-resource-performance.md
- 05-caching-performance.md
- 06-scalability-performance.md

**Executive Performance Summary:**

### Performance Grade & Scoring
- Overall performance grade (A/B/C/D/F)
- Component-wise performance scores
- Critical performance issue count
- Performance improvement potential assessment
- Business impact of performance issues

### Performance Benchmark Analysis
- Current vs target performance metrics
- Industry benchmark comparison
- User experience impact assessment
- Performance trend analysis
- Competitive performance positioning

### Critical Performance Issues
- **CRITICAL (9-10)**: Performance blockers requiring immediate attention
- **HIGH (7-8)**: Significant performance improvements needed
- **MEDIUM (5-6)**: Performance optimization opportunities
- **LOW (3-4)**: Minor performance enhancements

### Performance Optimization Roadmap
- **Phase 1 (Week 1)**: Critical performance fixes
- **Phase 2 (Month 1)**: High-impact optimizations
- **Phase 3 (Month 2)**: Scalability improvements
- **Phase 4 (Month 3)**: Advanced performance features

### Technology Stack Performance Assessment
- **FastAPI**: Framework utilization efficiency
- **PostgreSQL**: Database performance optimization
- **React**: Frontend performance implementation
- **Vite**: Build and bundle optimization
- **Infrastructure**: Deployment and scaling performance

### User Experience Impact Analysis
- Core Web Vitals assessment
- User journey performance impact
- Mobile vs desktop performance
- Network condition adaptability
- Accessibility performance considerations

### Scalability & Growth Assessment
- Current scaling limitations
- Traffic growth handling capability
- Resource scaling requirements
- Performance under load projections
- Cost implications of scaling

### Performance Testing Strategy
- Load testing recommendations
- Performance regression testing
- Monitoring and alerting setup
- Performance CI/CD integration
- User experience testing

### Performance Learning & Development
- Critical performance skills gaps
- Recommended performance training
- Performance optimization tools
- Best practices documentation
- Advanced performance concepts

### ROI & Business Impact
- Performance improvement business value
- User experience enhancement potential
- Cost optimization opportunities
- Competitive advantage through performance
- Revenue impact of performance improvements

**Performance Maturity Assessment:**
- **PRODUCTION READY**: Excellent performance across all metrics
- **PERFORMANCE TUNING NEEDED**: Good baseline, optimization required
- **SIGNIFICANT OPTIMIZATION REQUIRED**: Performance issues impacting UX
- **PERFORMANCE OVERHAUL NEEDED**: Major performance redesign required

Provide actionable performance improvement plan with specific metrics, timelines, and success criteria.
```

---

## Performance Review Implementation Workflow

### Phase 1: Performance Assessment Setup (5 minutes)
1. Create `performance-review/` folder in project root
2. Set up performance monitoring tools access
3. Run Performance Baseline Analysis prompt (Prompt #0)

### Phase 2: Detailed Performance Reviews (60-75 minutes)
1. **Database Performance** (15 minutes) - Prompt #1
2. **API Performance** (15 minutes) - Prompt #2
3. **Frontend Performance** (15 minutes) - Prompt #3
4. **Memory & Resource Performance** (10 minutes) - Prompt #4
5. **Caching Performance** (10 minutes) - Prompt #5
6. **Scalability Performance** (15 minutes) - Prompt #6

### Phase 3: Performance Report Compilation (15 minutes)
1. Run Comprehensive Performance Report prompt (Prompt #7)
2. Analyze performance benchmark gaps
3. Create prioritized optimization roadmap

### Phase 4: Performance Improvement Planning (10 minutes)
1. Identify critical performance bottlenecks
2. Create performance improvement timeline
3. Recommend performance monitoring implementation
4. Plan performance training and mentoring

## Performance Review Quality Assurance

### Before Starting Performance Review:
- [ ] Performance testing tools available
- [ ] #codebase access confirmed with performance focus
- [ ] Performance baseline metrics identified

### During Performance Reviews:
- [ ] Each domain thoroughly analyzed for bottlenecks
- [ ] Performance metrics and targets clearly defined
- [ ] Specific optimization recommendations provided
- [ ] Code examples included for improvements

### After All Performance Reviews:
- [ ] All performance domains covered comprehensively
- [ ] Performance scoring consistent across domains
- [ ] Optimization roadmap prioritized by impact
- [ ] Performance monitoring strategy included

### Performance Report Quality Check:
- [ ] Executive summary with clear performance assessment
- [ ] Quantified performance improvement opportunities
- [ ] Specific optimization timeline and milestones
- [ ] Performance learning and development plan
- [ ] Business impact and ROI analysis
- [ ] Scalability and growth considerations

This performance-focused template ensures comprehensive performance assessment while providing actionable optimization guidance for bootcamp candidates.
