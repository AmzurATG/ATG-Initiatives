# Performance Review - Condensed Prompt

## Single Comprehensive Performance Review
**Save output as:** `performance-review/complete-performance-review.md`

```
You are a performance engineering expert reviewing a bootcamp FastAPI + React application. Analyze #codebase for performance optimization opportunities.

**Analysis Areas (Score each 1-10):**

1. **Architecture Performance**: System design, tech stack choices, integration latencies, resource utilization patterns
2. **Database**: Query performance, indexing strategy, connection pooling, schema design, transaction handling
3. **API Performance**: Response times, throughput, resource efficiency, caching opportunities, async implementation
4. **Frontend**: Bundle size, rendering efficiency, network performance, Core Web Vitals, asset optimization
5. **Scalability**: Horizontal/vertical scaling readiness, load handling, performance degradation patterns
6. **Monitoring**: Instrumentation, metrics collection, alerting, observability, performance testing
7. **Code-Level**: Algorithm efficiency, data structure choices, memory management, I/O operations, concurrency
8. **Anti-Patterns**: Common pitfalls, inefficient patterns, unnecessary computations, suboptimal framework usage, resource waste
9. **Optimization Opportunities**: Quick wins, strategic improvements, technology upgrades, caching strategies

**Output Format:**
- Score (X/10) + key findings for each area
- Top 5 performance bottlenecks with file locations & impact (Critical/High/Medium/Low)
- Top 3 quick wins with implementation guidance
- Top 3 strategic optimizations with effort/impact analysis
- Performance budget recommendations (response times, bundle size, resource limits)

Focus on: measurable issues, specific code locations, quantifiable improvements.
```

## Quick Performance Hotspot Analysis
**Use for targeted performance review:**

```
Analyze #codebase for performance issues focusing on:
1. Database queries (N+1, missing indexes, slow queries)
2. API endpoints (slow responses, resource-heavy operations)
3. Frontend (large bundles, render bottlenecks, unnecessary re-renders)
4. Caching opportunities (repeated computations, cacheable data)

Provide: Top 5 bottlenecks with severity (Critical/High/Medium/Low), file:line, quick fix suggestions.
```

## Token Usage Tips
- Analyze by layer: Backend performance separate from frontend performance
- Use file patterns: `#file:**/api/**` for API, `#file:**/queries.py` for DB
- Request specific metrics: "Focus on endpoints >500ms" or "Find N+1 query issues"
