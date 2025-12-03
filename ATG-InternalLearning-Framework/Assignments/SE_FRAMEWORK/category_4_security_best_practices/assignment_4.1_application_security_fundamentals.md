# Assignment 4.1: Application Security Fundamentals

## Assignment Information
- **Category**: Security Best Practices
- **Sub-category**: 4.1 Application Security Fundamentals
- **Team Members**: [Pair names to be assigned]
- **Presentation Date**: [To be scheduled]
- **Duration**: 30-45 minutes

---

## Learning Objectives
By the end of this session, the team should be able to:
1. Understand and prevent OWASP Top 10 vulnerabilities
2. Implement proper input validation and sanitization
3. Prevent SQL injection attacks
4. Protect applications from Cross-Site Scripting (XSS)

---

## Topics to Cover

### 1. OWASP Top 10 Vulnerabilities
- Broken Access Control
- Cryptographic Failures
- Injection attacks
- Insecure Design
- Security Misconfiguration
- Vulnerable and Outdated Components
- Other critical vulnerabilities
- Real-world examples and impact

### 2. Input Validation and Sanitization
- Whitelist vs blacklist approaches
- Server-side validation (never trust client)
- Data type validation
- Length and format validation
- Encoding and escaping output
- Parameterized queries

### 3. SQL Injection Prevention
- How SQL injection works
- Parameterized queries/prepared statements
- ORM protection and pitfalls
- Stored procedures security
- Input validation for SQL
- Testing for SQL injection

### 4. Cross-Site Scripting (XSS) Prevention
- Reflected, Stored, and DOM-based XSS
- Output encoding for different contexts
- Content Security Policy (CSP)
- HTTPOnly and Secure cookie flags
- Framework-specific protections
- XSS testing and detection

---

## Assignment Deliverables

### 1. Presentation (20-30 minutes)
- OWASP Top 10 overview with examples
- Input validation best practices
- SQL injection demonstration and prevention
- XSS attack types and defenses
- Security testing tools and techniques

### 2. Code Demo/Example
- Demonstrate vulnerable code and exploits
- Show SQL injection attack and fix
- Demonstrate XSS attack and prevention
- Implement proper input validation
- Use security scanning tools (OWASP ZAP, Burp Suite)

### 3. Resources & References (Provide 3-5 curated)
- OWASP Top 10 documentation
- Security testing tools and guides
- Framework-specific security guides
- Security vulnerability databases
- Bug bounty reports and case studies

### 4. Q&A Session (10-15 minutes)
- Prepare for questions about WAF (Web Application Firewall)
- Security headers (CORS, CSP, etc.)
- Third-party library vulnerabilities
- Security vs usability trade-offs
- Compliance requirements

---

## Preparation Guidelines

### Research
- Study OWASP Top 10 in detail
- Learn about common attack vectors
- Understand security testing methodologies
- Review framework security features

### Practice
- Build intentionally vulnerable app
- Perform security testing
- Fix vulnerabilities
- Use security scanning tools
- Test input validation

### Collaboration
- Review ATG code for security issues
- Conduct security code reviews
- Share security incidents and lessons
- Discuss client security requirements

### Engagement
- Live hacking demonstration
- Interactive: find the vulnerability
- Group exercise: secure a code snippet
- Show real vulnerability reports

---

## Success Criteria
- [ ] Understanding of OWASP Top 10 vulnerabilities
- [ ] Ability to identify and fix SQL injection
- [ ] Knowledge of XSS prevention techniques
- [ ] Implementation of proper input validation
- [ ] Team can perform basic security code reviews

---

## Support & Resources
- Reference the lesson_plan_template.md for structure
- Consult with security team
- Use security testing tools
- Software Engineering Excellence Framework document

---

## Notes
- Foundation for all security topics
- Connect to API security (Assignment 2.3)
- Critical for all ATG applications
- Emphasize security by design
- Discuss how Copilot can introduce vulnerabilities
- Consider security training platforms (OWASP WebGoat)
