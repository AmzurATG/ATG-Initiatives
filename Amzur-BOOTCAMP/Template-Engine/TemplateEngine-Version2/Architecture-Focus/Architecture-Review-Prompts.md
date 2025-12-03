# Architecture Review - Condensed Prompt

## Single Comprehensive Architecture Review
**Save output as:** `architecture-review/complete-architecture-review.md`

```
You are a senior software architect reviewing a bootcamp FastAPI + React application. Analyze #codebase for architectural quality.

**Analysis Areas (Score each 1-10):**

1. **System Architecture**: Style (monolithic/layered/microservices), component boundaries, communication patterns, data flow, tech stack integration
2. **Design Patterns**: Creational/Structural/Behavioral patterns usage, anti-patterns identification
3. **Domain Design**: Business logic organization, domain modeling, entity/value object design, service layer
4. **Layer Architecture**: Presentation/Business/Data layers, separation of concerns, coupling analysis, cross-cutting concerns (logging, security, caching)
5. **Modularity**: Component cohesion/coupling, interface quality, dependency management
6. **Data Architecture**: Model design, access patterns, consistency strategies, validation architecture
7. **Integration**: External service integration, internal communication, transaction management, event-driven patterns
8. **Security Architecture**: Authentication/authorization patterns, security boundaries, data protection
9. **Performance Architecture**: Scalability design, caching strategy, async processing, resource management
10. **Maintainability**: Change accommodation, extension points, refactoring support, testing architecture

**Output Format:**
- Score (X/10) + key findings for each area
- Top 5 architectural concerns with file/component locations
- Top 3 recommended architectural improvements
- Architecture maturity: Poor(1-2)/Adequate(3-4)/Good(5-6)/Excellent(7-8)/Sophisticated(9-10)

Focus on: specific architectural decisions in code, design pattern usage examples, structural issues.
```

## Quick Pattern & Structure Review
**Use for focused architectural analysis:**

```
Analyze #codebase architecture focusing on:
1. Design pattern usage & effectiveness
2. Layer separation & dependencies
3. Component coupling issues
4. Scalability & maintainability concerns

Provide: Score (X/10), 3 structural issues, 3 pattern improvements with file paths.
```

## Token Usage Tips
- Review by layer: `#file:**/api/**` (backend), `#file:**/components/**` (frontend)
- Focus on critical paths: authentication, data flow, external integrations
- Skip areas with no code (e.g., if no event-driven features, skip that section)
