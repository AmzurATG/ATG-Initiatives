# Assignment 2.3: API Security

## Assignment Information
- **Category**: API Design & Development
- **Sub-category**: 2.3 API Security
- **Team Members**: [Pair names to be assigned]
- **Presentation Date**: [To be scheduled]
- **Duration**: 30-45 minutes

---

## Learning Objectives
By the end of this session, the team should be able to:
1. Understand the difference between authentication and authorization in APIs
2. Implement OAuth 2.0 and OpenID Connect flows correctly
3. Secure APIs using API keys, tokens, and other authentication methods
4. Apply rate limiting and throttling to protect API resources

---

## Topics to Cover

### 1. Authentication vs Authorization
- Authentication: verifying identity
- Authorization: verifying permissions
- Common confusion and mistakes
- Separation of concerns
- Multi-factor authentication considerations

### 2. OAuth 2.0 and OpenID Connect
- OAuth 2.0 grant types (Authorization Code, Client Credentials, etc.)
- Access tokens and refresh tokens
- OpenID Connect for identity layer
- JWT (JSON Web Tokens) structure and validation
- Common security vulnerabilities (CSRF, token leakage)

### 3. API Key Management
- API key vs OAuth tokens
- Key generation and storage
- Key rotation strategies
- Scope and permissions
- Compromised key handling

### 4. Rate Limiting and Throttling
- Why rate limiting is necessary
- Rate limiting strategies (fixed window, sliding window, token bucket)
- HTTP headers for rate limits (X-RateLimit-*)
- DDoS protection
- Per-user vs per-IP rate limiting

---

## Assignment Deliverables

### 1. Presentation (20-30 minutes)
- Authentication and authorization concepts with diagrams
- OAuth 2.0 flows visualized (especially Authorization Code flow)
- API key management best practices
- Rate limiting implementation strategies
- Real-world security breaches and lessons learned

### 2. Code Demo/Example
- Implement JWT-based authentication
- Add OAuth 2.0 flow (use auth provider like Auth0, Okta)
- Protect endpoints with role-based authorization
- Implement rate limiting middleware
- Demonstrate token refresh mechanism

### 3. Resources & References (Provide 3-5 curated)
- OAuth 2.0 specification and guides
- JWT best practices and security considerations
- OWASP API Security Top 10
- Rate limiting algorithm explanations
- Security headers and HTTPS enforcement

### 4. Q&A Session (10-15 minutes)
- Prepare for questions about token storage (cookies vs localStorage)
- Single Sign-On (SSO) implementation
- API security testing
- Compliance requirements (GDPR, HIPAA)
- Mobile app authentication patterns

---

## Preparation Guidelines

### Research
- Study OAuth 2.0 specification and common flows
- Understand JWT structure and claims
- Review OWASP API Security Top 10
- Learn about common API attacks (injection, broken auth)

### Practice
- Build API with JWT authentication
- Implement OAuth 2.0 integration
- Add role-based access control
- Create rate limiting middleware
- Test security with tools like OWASP ZAP

### Collaboration
- Review security practices in current ATG projects
- Identify security gaps or vulnerabilities
- Discuss client security requirements
- Share experiences with auth providers

### Engagement
- Live demo: breaking and fixing insecure API
- Interactive OAuth flow walkthrough
- Group exercise: design auth for a scenario
- Show rate limiting in action with load testing

---

## Success Criteria
- [ ] Clear explanation of auth vs authz with practical examples
- [ ] Working API with proper authentication and authorization
- [ ] Implementation of rate limiting middleware
- [ ] Understanding of OAuth 2.0 flows and JWT validation
- [ ] Team can identify and fix common API security vulnerabilities

---

## Support & Resources
- Reference the lesson_plan_template.md for structure
- Consult with security team about standards
- Review existing authentication implementations
- Software Engineering Excellence Framework document

---

## Notes
- Critical security topic that applies to all APIs
- Builds on REST/GraphQL knowledge from Assignments 2.1 and 2.2
- Connect to broader security topics in Category 4
- Emphasize that security is not optional
- Discuss how Copilot can introduce security vulnerabilities
- Relate to compliance requirements for client projects
- Consider API gateway solutions (Kong, AWS API Gateway)
