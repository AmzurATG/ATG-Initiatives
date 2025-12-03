# Assignment 1.3: Microservices Architecture

## Assignment Information
- **Category**: Software Architecture Fundamentals
- **Sub-category**: 1.3 Microservices Architecture
- **Team Members**: [Pair names to be assigned]
- **Presentation Date**: [To be scheduled]
- **Duration**: 30-45 minutes

---

## Learning Objectives
By the end of this session, the team should be able to:
1. Understand microservices principles, benefits, and challenges
2. Apply service decomposition strategies to identify appropriate service boundaries
3. Evaluate different inter-service communication patterns and when to use each
4. Recognize common microservices anti-patterns and how to avoid them

---

## Topics to Cover

### 1. Microservices Principles and Benefits
- Single responsibility at service level
- Independent deployability and scalability
- Technology diversity and team autonomy
- Resilience and fault isolation
- Trade-offs: complexity vs benefits

### 2. Service Boundaries and Decomposition
- Domain-Driven Design concepts
- Bounded contexts and aggregates
- Data ownership per service
- Service size considerations
- Decomposition strategies (by business capability, subdomain)

### 3. Inter-service Communication Patterns
- Synchronous: REST, gRPC
- Asynchronous: message queues, event streams
- Service mesh and API gateway patterns
- Circuit breakers and retry strategies
- Saga pattern for distributed transactions

### 4. Microservices Anti-patterns
- Distributed monolith
- Chatty services and network overhead
- Shared databases across services
- Microservices for everything
- Premature decomposition

---

## Assignment Deliverables

### 1. Presentation (20-30 minutes)
- Microservices architecture overview with visual diagrams
- Service decomposition methodology
- Communication patterns comparison
- Real-world examples of successful and failed implementations
- When NOT to use microservices

### 2. Code Demo/Example
- Simple e-commerce system with 3-4 microservices
- Demonstrate REST and message-based communication
- Show service discovery and API gateway
- Example of handling distributed failures
- Docker Compose setup for local development

### 3. Resources & References (Provide 3-5 curated)
- Sam Newman's "Building Microservices" resources
- Martin Fowler's microservices articles
- Example microservices projects on GitHub
- Tools and frameworks (Spring Boot, Express, etc.)
- Service mesh documentation (Istio, Linkerd)

### 4. Q&A Session (10-15 minutes)
- Prepare for questions about when to use microservices
- Database per service vs shared database
- Testing microservices systems
- Deployment and DevOps challenges
- Cost implications

---

## Preparation Guidelines

### Research
- Study microservices case studies (Netflix, Amazon, Uber)
- Understand Domain-Driven Design basics
- Learn about service communication technologies
- Research microservices failures and lessons learned

### Practice
- Build a simple multi-service application
- Implement both REST and async communication
- Set up service-to-service authentication
- Create Docker containers for services
- Implement health checks and monitoring

### Collaboration
- Discuss monolithic vs microservices for ATG projects
- Identify services in existing systems
- Review current inter-service communication
- Debate migration strategies

### Engagement
- Live architecture diagram session
- Interactive: "Should this be a microservice?" quiz
- Show real monitoring dashboards
- Discuss ATG client scenarios

---

## Success Criteria
- [ ] Clear explanation of microservices principles with architecture diagrams
- [ ] Working multi-service demo with different communication patterns
- [ ] Practical guidelines for service decomposition
- [ ] Understanding of anti-patterns and how to avoid them
- [ ] Team can evaluate when microservices are appropriate vs monolithic

---

## Support & Resources
- Reference the lesson_plan_template.md for structure
- Consult with DevOps team about deployment strategies
- Review microservices architecture patterns documentation
- Software Engineering Excellence Framework document

---

## Notes
- This is an advanced topic that requires solid architectural foundation
- Connect to API Design (Category 2) and CI/CD (Category 11)
- Emphasize the complexity cost of microservices
- Relate to actual ATG project scale and team size
- Discuss how microservices relate to AI/ML service deployment
- Consider serverless as an alternative approach
