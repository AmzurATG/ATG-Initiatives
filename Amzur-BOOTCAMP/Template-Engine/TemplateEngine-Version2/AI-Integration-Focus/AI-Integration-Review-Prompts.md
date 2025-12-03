# AI Integration Review - Condensed Prompt

## Single Comprehensive AI Integration Review
**Save output as:** `ai-integration-review/complete-ai-review.md`

```
You are a senior AI integration specialist reviewing a bootcamp full-stack application. Analyze #codebase for AI integration quality across all dimensions.

**Analysis Areas (Score each 1-10):**

1. **Service Architecture**: AI provider selection, integration patterns, abstraction quality, multi-provider strategy, client implementation, authentication
2. **Prompt Engineering**: Design quality, template management, context handling, dynamic generation, token optimization
3. **Feature Implementation**: Architecture, UX, performance, reliability, innovation, data flow (input/output processing, validation, security)
4. **Performance & Costs**: Response times, caching, batch/async processing, cost optimization strategies
5. **Security & Safety**: Input sanitization (prompt injection), output validation, content moderation, privacy protection, access control
6. **Resilience**: Error recovery, fallbacks, circuit breakers, retry logic, user feedback
7. **Monitoring**: Usage tracking, performance metrics, quality assessment, cost monitoring

**Output Format:**
- Score (X/10) + brief justification for each area
- Top 3 critical issues with file locations
- Top 3 improvement opportunities with specific recommendations
- Maturity level: Basic/Developing/Intermediate/Advanced/Expert

Focus on: specific code examples, file paths, actionable fixes.
```

## Quick AI Feature-Specific Review
**Use when reviewing specific AI features:**

```
Analyze #codebase AI feature: [FEATURE_NAME]. Evaluate:
1. Prompt quality & context management
2. Error handling & fallbacks  
3. Response processing & validation
4. Performance (caching, async)
5. Security (injection, output validation)
6. Cost efficiency

Provide: Score (X/10), 2-3 critical issues, 2-3 improvements with code locations.
```

## Token Usage Tips
- Use specific file patterns: `#file:**/ai_*.py` or `#file:**/components/AI*`
- Focus review: Omit areas not applicable (e.g., if no multi-provider setup, skip scoring)
- Split large codebases: Review backend AI separately from frontend AI features
