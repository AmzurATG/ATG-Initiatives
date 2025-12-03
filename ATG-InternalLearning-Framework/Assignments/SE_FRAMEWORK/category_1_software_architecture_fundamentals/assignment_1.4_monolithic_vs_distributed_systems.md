# Assignment 1.4: Monolithic vs Distributed Systems

## Assignment Information
- **Category**: Software Architecture Fundamentals
- **Sub-category**: 1.4 Monolithic vs Distributed Systems
- **Team Members**: [Pair names to be assigned]
- **Presentation Date**: [To be scheduled]
- **Duration**: 30-45 minutes

---

## Learning Objectives
By the end of this session, the team should be able to:
1. Understand the monolith-first approach and when it's the right choice
2. Make informed decisions between monolithic and distributed architectures
3. Implement modular monolith patterns to maintain flexibility
4. Plan and execute migration strategies from monolith to microservices when needed

---

## Topics to Cover

### 1. Monolith-First Approach
- Benefits of starting with a monolith
- Faster initial development and deployment
- Simpler debugging and testing
- Lower operational complexity
- When monoliths make sense long-term

### 2. Choosing Between Monolith and Microservices
- Team size and organizational structure
- Scale requirements and traffic patterns
- Deployment frequency needs
- Technology diversity requirements
- Budget and operational capacity
- Decision framework and criteria

### 3. Modular Monoliths
- Architectural modules within a monolith
- Clear boundaries and interfaces
- Preventing "big ball of mud"
- Benefits: simplicity + modularity
- Tools and techniques for maintaining modularity

### 4. Migration Strategies
- Strangler Fig pattern
- Incremental extraction of services
- Data migration challenges
- Maintaining consistency during migration
- When to stop (good-enough architecture)

---

## Assignment Deliverables

### 1. Presentation (20-30 minutes)
- Comparison matrix: monolith vs distributed systems
- Real-world case studies of successful monoliths
- Modular monolith architecture patterns with diagrams
- Step-by-step migration strategy examples
- Common mistakes in both approaches

### 2. Code Demo/Example
- Well-structured modular monolith example
- Show module boundaries and communication
- Demonstrate how to extract a service from monolith
- Before/after: poorly structured vs modular
- Deployment comparison (monolith vs distributed)

### 3. Resources & References (Provide 3-5 curated)
- "Monolith to Microservices" by Sam Newman
- Martin Fowler's MonolithFirst article
- Modular monolith case studies (Shopify, etc.)
- Migration pattern documentation
- Cost comparison analyses

### 4. Q&A Session (10-15 minutes)
- Prepare for questions about team size thresholds
- Database strategy in monoliths
- When migration is premature vs overdue
- Hybrid approaches
- Impact on development velocity

---

## Preparation Guidelines

### Research
- Study successful monolithic applications at scale
- Review modular monolith architectures (Shopify, Basecamp)
- Learn about the Strangler Fig pattern in detail
- Research migration failures and lessons learned
- Understand Conway's Law implications

### Practice
- Create a modular monolith with clear boundaries
- Build a proof-of-concept service extraction
- Document module interfaces
- Set up CI/CD for both monolith and services
- Practice deploying both architectures

### Collaboration
- Audit current ATG projects: monolith or distributed?
- Discuss technical debt in existing architectures
- Identify candidates for refactoring
- Plan hypothetical migration scenarios
- Review team capacity for operational complexity

### Engagement
- Interactive decision tree for architecture choice
- Poll: "What architecture are you currently working with?"
- Live demo: deploying monolith vs microservice
- Group exercise: analyze a case study
- Discuss actual ATG client needs

---

## Success Criteria
- [ ] Clear comparison of monolithic and distributed architectures with pros/cons
- [ ] Working example of a well-structured modular monolith
- [ ] Practical decision framework for architecture selection
- [ ] Understanding of migration strategies and pitfalls
- [ ] Team can evaluate their current projects' architecture appropriateness

---

## Support & Resources
- Reference the lesson_plan_template.md for structure
- Consult with senior architects about past decisions
- Review existing ATG project architectures
- Software Engineering Excellence Framework document

---

## Notes
- This synthesizes concepts from Assignments 1.1-1.3
- Emphasize that "monolith" is not a dirty word
- Many successful companies run on monoliths at scale
- Premature distribution is a common and costly mistake
- Connect to team size, Conway's Law, and org structure
- Relate to AI/ML service architectures (could be monolithic or distributed)
- Consider serverless and Platform-as-a-Service as alternatives
