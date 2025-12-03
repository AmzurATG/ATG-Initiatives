# Assignment 2.1: RESTful API Design

## Assignment Information
- **Category**: API Design & Development
- **Sub-category**: 2.1 RESTful API Design
- **Team Members**: [Pair names to be assigned]
- **Presentation Date**: [To be scheduled]
- **Duration**: 30-45 minutes

---

## Learning Objectives
By the end of this session, the team should be able to:
1. Understand REST principles and architectural constraints
2. Design resources and URIs following REST best practices
3. Use HTTP methods and status codes correctly and consistently
4. Implement effective API versioning strategies

---

## Topics to Cover

### 1. REST Principles and Constraints
- Client-server architecture
- Statelessness and scalability
- Cacheability and uniform interface
- Layered system and code on demand
- Richardson Maturity Model (Levels 0-3)

### 2. Resource Modeling and URI Design
- Resource identification and naming
- URI structure best practices (nouns vs verbs)
- Singular vs plural resources
- Nested resources and relationships
- Query parameters for filtering, sorting, pagination

### 3. HTTP Methods and Status Codes
- GET, POST, PUT, PATCH, DELETE semantics
- Idempotency and safety
- Appropriate status codes (200, 201, 204, 400, 401, 403, 404, 500, etc.)
- Custom vs standard status codes
- Response body design

### 4. API Versioning Strategies
- URI versioning (/v1/resources)
- Header versioning (Accept: application/vnd.api+json;version=1)
- Query parameter versioning
- Pros and cons of each approach
- Backward compatibility strategies

---

## Assignment Deliverables

### 1. Presentation (20-30 minutes)
- REST principles explained with real-world analogies
- Visual guide to proper URI design with examples
- HTTP method and status code decision tree
- API versioning comparison with recommendations
- Common REST anti-patterns

### 2. Code Demo/Example
- Build a simple RESTful API (products, orders, users)
- Demonstrate proper HTTP method usage
- Show pagination, filtering, and sorting
- Implement API versioning
- Use tools like Postman/curl to test endpoints

### 3. Resources & References (Provide 3-5 curated)
- Roy Fielding's REST dissertation (key sections)
- REST API design guidelines from major companies (Google, Microsoft)
- OpenAPI/Swagger specification
- Best practice articles and cheat sheets
- Common REST mistakes to avoid

### 4. Q&A Session (10-15 minutes)
- Prepare for questions about HATEOAS (hypermedia)
- REST vs RPC style APIs
- When to break REST principles
- Performance implications
- Framework-specific implementations

---

## Preparation Guidelines

### Research
- Study REST fundamentals and Richardson Maturity Model
- Review well-designed public APIs (Stripe, GitHub, Twitter)
- Understand HTTP specification basics
- Learn about content negotiation

### Practice
- Build a complete REST API with CRUD operations
- Test with various HTTP clients
- Implement pagination and filtering
- Add proper error responses
- Document with OpenAPI/Swagger

### Collaboration
- Review existing ATG APIs for REST compliance
- Identify inconsistencies in current projects
- Discuss API design decisions with team
- Share pain points from API integration experiences

### Engagement
- Live API design session: design an API together
- Interactive quiz on HTTP status codes
- Show real API responses from popular services
- Discuss ATG client API requirements

---

## Success Criteria
- [ ] Clear explanation of REST principles with practical examples
- [ ] Working REST API demonstrating best practices
- [ ] Guidelines for consistent URI and resource design
- [ ] Understanding of when and how to version APIs
- [ ] Team can identify and fix common REST mistakes in existing code

---

## Support & Resources
- Reference the lesson_plan_template.md for structure
- Consult with API team about current standards
- Review existing API documentation
- Software Engineering Excellence Framework document

---

## Notes
- Foundation for Assignment 2.2 (GraphQL) and 2.3 (API Security)
- Connect to microservices communication patterns (Category 1)
- Relate to integration patterns and error handling
- Discuss how GitHub Copilot can help generate REST endpoints
- Emphasize consistency across team and projects
