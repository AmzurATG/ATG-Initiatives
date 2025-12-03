# Assignment 1.1: Layered Architecture Patterns

## Assignment Information
- **Category**: Software Architecture Fundamentals
- **Sub-category**: 1.1 Layered Architecture Patterns
- **Team Members**: [Pair names to be assigned]
- **Presentation Date**: [To be scheduled]
- **Duration**: 30-45 minutes

---

## Learning Objectives
By the end of this session, the team should be able to:
1. Understand the difference between N-tier and N-layer architecture and when to use each approach
2. Apply separation of concerns principles to organize code effectively across layers
3. Design presentation, business logic, and data access layers with clear responsibilities

---

## Topics to Cover

### 1. N-tier vs N-layer Architecture
- Physical vs logical separation
- When to use multi-tier deployment
- Monolithic vs distributed layering
- Performance and scalability considerations

### 2. Separation of Concerns
- Single Responsibility at the layer level
- Dependency direction and layer coupling
- Cross-cutting concerns handling
- Common violations and anti-patterns

### 3. Layer Responsibilities
- Presentation layer: UI, user input, display logic
- Business layer: business rules, validation, workflows
- Data access layer: database operations, ORM usage
- Infrastructure and cross-cutting concerns

### 4. When to Use Layered Architecture
- Application size and complexity considerations
- Team structure and skill distribution
- Maintenance and evolution requirements
- Performance vs maintainability trade-offs

---

## Assignment Deliverables

### 1. Presentation (20-30 minutes)
- Clear explanation of layered architecture concepts with diagrams
- Real-world examples from web applications
- Comparison of properly layered vs poorly layered code
- Best practices and common pitfalls

### 2. Code Demo/Example
- Simple application demonstrating 3-layer architecture
- Show code organization in folders/packages
- Demonstrate how layers communicate
- Example of refactoring from mixed concerns to layered structure

### 3. Resources & References (Provide 3-5 curated)
- Architecture documentation and articles
- Sample repositories with good layering
- Comparison articles (N-tier vs N-layer)
- Anti-pattern examples to avoid

### 4. Q&A Session (10-15 minutes)
- Prepare for questions about layer granularity
- When to add or remove layers
- How this relates to microservices
- Integration with modern frameworks (Spring, .NET, Django)

---

## Preparation Guidelines

### Research
- Study classic N-tier architecture patterns
- Review modern interpretations in your tech stack
- Understand the evolution from layered to other patterns
- Look at open-source projects with clear layering

### Practice
- Create a simple CRUD application with clear layers
- Try refactoring a "monolithic" file into layers
- Draw architecture diagrams
- Prepare side-by-side code comparisons

### Collaboration
- Discuss with team members about current project structure
- Review existing code for layering practices
- Identify areas where layering could improve maintainability

### Engagement
- Include interactive elements (poll on current practices)
- Show before/after refactoring examples
- Discuss real ATG project scenarios

---

## Success Criteria
- [ ] Clear explanation of N-tier vs N-layer with visual diagrams
- [ ] Working code example demonstrating proper layer separation
- [ ] Practical guidelines for applying layered architecture in ATG projects
- [ ] Identification of common anti-patterns and how to avoid them
- [ ] Team can articulate when layered architecture is appropriate vs other patterns

---

## Support & Resources
- Reference the lesson_plan_template.md for structure
- Consult with senior developers about architectural decisions
- Review existing ATG projects for examples
- Software Engineering Excellence Framework document

---

## Notes
- This is a foundational topic that connects to many other SE concepts
- Emphasize that layering is one approach, not the only approach
- Connect to Clean Architecture and Hexagonal Architecture (covered in 1.2)
- Relate to practical scenarios: "How would Copilot help/hinder proper layering?"
