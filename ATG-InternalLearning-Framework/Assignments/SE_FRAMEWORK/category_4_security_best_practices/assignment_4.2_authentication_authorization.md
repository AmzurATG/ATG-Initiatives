# Assignment 4.2: Authentication & Authorization

## Assignment Information
- **Category**: Security Best Practices
- **Sub-category**: 4.2 Authentication & Authorization
- **Team Members**: [Pair names to be assigned]
- **Presentation Date**: [To be scheduled]
- **Duration**: 30-45 minutes

---

## Learning Objectives
By the end of this session, the team should be able to:
1. Implement secure password hashing and storage
2. Design effective session management strategies
3. Implement Role-Based Access Control (RBAC)
4. Apply Attribute-Based Access Control (ABAC) when needed

---

## Topics to Cover

### 1. Password Hashing and Storage
- Never store plaintext passwords
- BCrypt, Argon2, PBKDF2 comparison
- Salt and pepper concepts
- Password strength requirements
- Multi-factor authentication (MFA)

### 2. Session Management
- Session tokens vs JWT
- Session storage strategies
- Session timeout and renewal
- Session fixation and hijacking
- Secure session cookies

### 3. Role-Based Access Control (RBAC)
- Users, roles, and permissions
- Role hierarchies
- Principle of least privilege
- Implementation patterns
- Role management UI

### 4. Attribute-Based Access Control (ABAC)
- Policy-based access control
- Context and attributes
- When ABAC is better than RBAC
- XACML and policy languages
- Implementation challenges

---

## Assignment Deliverables

### 1. Presentation (20-30 minutes)
- Password security best practices
- Session management strategies
- RBAC vs ABAC comparison
- Real-world access control examples
- Common authentication mistakes

### 2. Code Demo/Example
- Implement password hashing with BCrypt
- Build session management
- Create RBAC system with roles and permissions
- Demonstrate MFA flow
- Show authorization middleware

### 3. Resources & References (Provide 3-5 curated)
- Password hashing guidelines
- Session management best practices
- RBAC implementation patterns
- MFA standards (TOTP, WebAuthn)
- Access control frameworks

### 4. Q&A Session (10-15 minutes)
- Prepare for questions about social login
- Biometric authentication
- Zero-trust architecture
- Federated identity
- GDPR and password retention

---

## Preparation Guidelines

### Research
- Study password hashing algorithms
- Learn session management techniques
- Understand RBAC and ABAC patterns
- Review authentication frameworks

### Practice
- Implement user authentication system
- Add role-based permissions
- Test session management
- Integrate MFA
- Build authorization middleware

### Collaboration
- Review ATG authentication systems
- Identify authorization gaps
- Discuss access control requirements
- Share authentication challenges

### Engagement
- Live demo: breaking weak passwords
- Interactive RBAC design exercise
- Show MFA in action
- Group discussion on access control

---

## Success Criteria
- [ ] Implementation of secure password handling
- [ ] Effective session management
- [ ] Working RBAC system
- [ ] Understanding of ABAC concepts
- [ ] Team can design secure auth systems

---

## Support & Resources
- Software Engineering Excellence Framework document
- Authentication library documentation
- Security best practices guides

---

## Notes
- Builds on API Security (Assignment 2.3)
- Critical for all applications
- Connect to compliance requirements
- Discuss passwordless authentication
- Consider SSO and identity providers
