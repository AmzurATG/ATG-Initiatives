# Assignment 3.1: Database Design Principles

## Assignment Information
- **Category**: Database Design & Management
- **Sub-category**: 3.1 Database Design Principles
- **Team Members**: [Pair names to be assigned]
- **Presentation Date**: [To be scheduled]
- **Duration**: 30-45 minutes

---

## Learning Objectives
By the end of this session, the team should be able to:
1. Apply normalization and denormalization techniques appropriately
2. Create effective entity-relationship models
3. Design database schemas following best practices
4. Select appropriate database types (SQL vs NoSQL) based on requirements

---

## Topics to Cover

### 1. Normalization and Denormalization
- Normal forms (1NF, 2NF, 3NF, BCNF)
- Benefits: data integrity, reduced redundancy
- Denormalization for performance
- When to normalize vs denormalize
- Trade-offs and practical considerations

### 2. Entity-Relationship Modeling
- Entities, attributes, and relationships
- One-to-one, one-to-many, many-to-many relationships
- Cardinality and participation
- ERD notation and tools
- Translating business requirements to ER models

### 3. Schema Design Best Practices
- Table and column naming conventions
- Primary keys and surrogate keys
- Foreign key constraints
- Indexes strategy (when and what to index)
- Data types selection
- NULL handling and default values

### 4. Database Selection (SQL vs NoSQL)
- Relational databases (PostgreSQL, MySQL, SQL Server)
- Document stores (MongoDB, CouchDB)
- Key-value stores (Redis, DynamoDB)
- Column-family stores (Cassandra, HBase)
- Graph databases (Neo4j, Amazon Neptune)
- Selection criteria based on use case

---

## Assignment Deliverables

### 1. Presentation (20-30 minutes)
- Normalization process with step-by-step example
- ER diagram creation walkthrough
- Schema design best practices with real examples
- SQL vs NoSQL comparison matrix
- Common database design mistakes

### 2. Code Demo/Example
- Design database for e-commerce system
- Create ER diagram and translate to schema
- Show normalized vs denormalized versions
- Demonstrate schema in both SQL and NoSQL
- Use database design tools (dbdiagram.io, etc.)

### 3. Resources & References (Provide 3-5 curated)
- Database design textbooks and guides
- Normalization tutorials and examples
- ER modeling tools and notations
- SQL vs NoSQL comparison articles
- Case studies of database choices

### 4. Q&A Session (10-15 minutes)
- Prepare for questions about over-normalization
- Composite keys vs surrogate keys
- Database migration strategies
- Multi-tenancy patterns
- Cloud database services

---

## Preparation Guidelines

### Research
- Study normal forms with examples
- Review ER modeling techniques
- Understand ACID properties
- Learn about CAP theorem for distributed databases

### Practice
- Design schemas for various scenarios
- Create ER diagrams
- Normalize a denormalized schema
- Compare SQL and NoSQL implementations
- Use database design tools

### Collaboration
- Review ATG project database designs
- Identify normalization opportunities
- Discuss database choice decisions
- Share schema evolution challenges

### Engagement
- Live schema design exercise
- Interactive normalization workshop
- Group discussion: SQL or NoSQL for scenario X?
- Show real production database schemas

---

## Success Criteria
- [ ] Clear explanation of normalization with practical examples
- [ ] Ability to create ER diagrams from requirements
- [ ] Understanding of schema design best practices
- [ ] Framework for choosing appropriate database type
- [ ] Team can review and improve existing database designs

---

## Support & Resources
- Reference the lesson_plan_template.md for structure
- Consult with database architects
- Review existing database schemas
- Software Engineering Excellence Framework document

---

## Notes
- Foundation for subsequent database topics
- Connect to data engineering for AI (from AI framework)
- Relate to performance optimization (Category 12)
- Emphasize that good design prevents future pain
- Discuss how Copilot can help with schema generation
- Consider polyglot persistence (using multiple databases)
