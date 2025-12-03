# Code Quality Review - Condensed Prompt

## Single Comprehensive Code Quality Review
**Save output as:** `code-quality-review/complete-quality-review.md`

```
You are a code quality expert reviewing a bootcamp FastAPI + React application. Analyze #codebase for code quality across all dimensions.

**Analysis Areas (Score each 1-10):**

1. **Readability**: Naming conventions, code organization, formatting, self-documenting code, complexity management
2. **Structure**: Single Responsibility Principle, DRY principle, function/class design, module organization
3. **Robustness**: Exception handling, input validation, edge cases, graceful degradation, resource management
4. **Consistency**: Style guide compliance (PEP 8, Airbnb), pattern consistency, API design uniformity, naming conventions
5. **Type Safety**: Type annotations, data validation, interface definitions, runtime safety
6. **Performance**: Algorithm efficiency, resource utilization, database queries, async usage, caching
7. **Testing**: Coverage, test quality, organization, mocking, integration tests
8. **Documentation**: Code comments, API docs, README, docstrings, architecture docs
9. **Security**: Best practices, input sanitization, auth/authz, data protection, vulnerability prevention
10. **Maintainability**: Refactoring readiness, change isolation, extension points, dependency management, technical debt

**Output Format:**
- Score (X/10) + critical findings for each area
- Top 5 code quality issues with file:line locations
- Top 5 improvement recommendations with examples
- Quality level: Poor(1-2)/Needs-Improvement(3-4)/Satisfactory(5-6)/Good(7-8)/Excellent(9-10)

Focus on: specific code examples, anti-patterns, quick wins, high-impact improvements.
```

## Quick Clean Code Review
**Use for focused quality check:**

```
Analyze #codebase for code quality focusing on:
1. Readability & naming issues
2. Code duplication & complexity
3. Error handling gaps
4. Type safety & validation issues
5. Major maintainability concerns

Provide: Score (X/10), top 5 issues with file:line, 3 quick wins.
```

## Token Usage Tips
- Review by concern: Focus on 2-3 areas at a time if codebase is large
- Use file patterns: `#file:**/*.py` for backend, `#file:**/*.tsx` for frontend
- Request specific analysis: "Focus on error handling and validation" to reduce scope
