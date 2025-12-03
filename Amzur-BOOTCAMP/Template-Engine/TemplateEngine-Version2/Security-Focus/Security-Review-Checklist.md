# Security Review Checklist - CONDENSED

## Quick Assessment Form

**Project:** ________________  **Date:** ________  **Reviewer:** ________________  
**Overall Score:** ___/10  **Risk Level:** ☐ Critical  ☐ High  ☐ Medium  ☐ Low  ☐ Minimal

---

## 1. Authentication & Authorization ___/10
- [ ] Strong password policy (min length, complexity)
- [ ] Secure password hashing (bcrypt/argon2)
- [ ] JWT token security (expiration, refresh, secret)
- [ ] Secure session management
- [ ] MFA/2FA implemented (if applicable)
- [ ] RBAC/permissions properly enforced
- [ ] No privilege escalation vulnerabilities
- [ ] Account lockout after failed attempts

**Critical Issues:** ________________________________________________________

---

## 2. Input Validation & Injection Prevention ___/10
- [ ] Server-side input validation (all endpoints)
- [ ] SQL injection prevention (parameterized queries/ORM)
- [ ] NoSQL injection prevention
- [ ] XSS prevention (output encoding, CSP)
- [ ] Command injection prevention
- [ ] LDAP injection prevention
- [ ] Input sanitization for all user data
- [ ] File upload validation (type, size, content)

**Critical Issues:** ________________________________________________________

---

## 3. Data Protection & Privacy ___/10
- [ ] Sensitive data encrypted at rest
- [ ] Data encrypted in transit (HTTPS enforced)
- [ ] No sensitive data in logs/errors
- [ ] PII handled securely
- [ ] Secure credential storage (env vars, secrets manager)
- [ ] No hardcoded secrets/API keys
- [ ] GDPR/privacy compliance considerations
- [ ] Secure data deletion

**Critical Issues:** ________________________________________________________

---

## 4. API Security ___/10
- [ ] Rate limiting implemented
- [ ] API authentication required
- [ ] HTTPS enforced (no HTTP)
- [ ] CORS properly configured (not "*")
- [ ] Security headers configured (CSP, X-Frame-Options, etc.)
- [ ] No sensitive data in URLs/query params
- [ ] Request/response validation
- [ ] API keys rotated regularly

**Critical Issues:** ________________________________________________________

---

## 5. Database Security ___/10
- [ ] Database connection secured (SSL/TLS)
- [ ] Least privilege database access
- [ ] SQL injection prevention
- [ ] No credentials in code
- [ ] Database backups secured
- [ ] Prepared statements/ORM used
- [ ] Database audit logging
- [ ] Schema migration security

**Critical Issues:** ________________________________________________________

---

## 6. Frontend Security ___/10
- [ ] XSS prevention in React components
- [ ] No dangerouslySetInnerHTML (or sanitized)
- [ ] Secure token storage (httpOnly cookies preferred)
- [ ] Content Security Policy configured
- [ ] Third-party scripts reviewed
- [ ] No sensitive data in local/session storage
- [ ] CSRF protection (if stateful)
- [ ] Secure dependency versions

**Critical Issues:** ________________________________________________________

---

## 7. Infrastructure & Configuration ___/10
- [ ] Environment variables for secrets
- [ ] No secrets in version control (.env in .gitignore)
- [ ] Debug mode disabled in production
- [ ] Error messages don't leak info
- [ ] HTTPS/TLS properly configured
- [ ] Secure CORS configuration
- [ ] Dependency vulnerabilities checked (npm audit, safety)
- [ ] Docker security (if applicable)

**Critical Issues:** ________________________________________________________

---

## 8. Error Handling & Logging ___/10
- [ ] Generic error messages to users
- [ ] No stack traces exposed
- [ ] No sensitive data in logs
- [ ] Comprehensive error logging (server-side)
- [ ] Security events logged
- [ ] Log access restricted
- [ ] No debug info in production
- [ ] Rate limiting on error endpoints

**Critical Issues:** ________________________________________________________

---

## 9. Business Logic Security ___/10
- [ ] Proper authorization checks
- [ ] No race conditions
- [ ] Secure state management
- [ ] Transaction integrity
- [ ] Idempotency for critical operations
- [ ] Proper workflow validation
- [ ] Financial operation security (if applicable)
- [ ] File operation security

**Critical Issues:** ________________________________________________________

---

## 10. Monitoring & Incident Response ___/10
- [ ] Security event logging
- [ ] Failed login attempt monitoring
- [ ] Anomaly detection (if applicable)
- [ ] Audit trails for sensitive operations
- [ ] Security alerting configured
- [ ] Incident response plan (basic)
- [ ] Regular security reviews
- [ ] Vulnerability disclosure process

**Critical Issues:** ________________________________________________________

---

## OWASP Top 10 (2021) Assessment

| Vulnerability | Status | Severity | Notes |
|---------------|--------|----------|-------|
| A01: Broken Access Control | ☐ Pass ☐ Fail | | |
| A02: Cryptographic Failures | ☐ Pass ☐ Fail | | |
| A03: Injection | ☐ Pass ☐ Fail | | |
| A04: Insecure Design | ☐ Pass ☐ Fail | | |
| A05: Security Misconfiguration | ☐ Pass ☐ Fail | | |
| A06: Vulnerable Components | ☐ Pass ☐ Fail | | |
| A07: ID & Auth Failures | ☐ Pass ☐ Fail | | |
| A08: Software & Data Integrity | ☐ Pass ☐ Fail | | |
| A09: Security Logging Failures | ☐ Pass ☐ Fail | | |
| A10: Server-Side Request Forgery | ☐ Pass ☐ Fail | | |

---

## Critical Vulnerabilities (IMMEDIATE FIX REQUIRED)

| Severity | Vulnerability | Location | Exploit Risk | Fix Priority |
|----------|---------------|----------|--------------|--------------|
| 🔴 Critical | | | High/Med/Low | P0 |
| 🔴 Critical | | | High/Med/Low | P0 |
| 🟠 High | | | High/Med/Low | P1 |
| 🟠 High | | | High/Med/Low | P1 |
| 🟡 Medium | | | High/Med/Low | P2 |

---

## Security Issues by Category

### Authentication/Authorization Issues:
- [ ] _______________________________________________________________________
- [ ] _______________________________________________________________________

### Injection Vulnerabilities:
- [ ] _______________________________________________________________________
- [ ] _______________________________________________________________________

### Data Exposure Issues:
- [ ] _______________________________________________________________________
- [ ] _______________________________________________________________________

### Configuration Issues:
- [ ] _______________________________________________________________________
- [ ] _______________________________________________________________________

---

## Common Vulnerability Patterns Found

- [ ] Hardcoded secrets/credentials
- [ ] Missing authentication
- [ ] Missing authorization checks
- [ ] SQL injection vectors
- [ ] XSS vulnerabilities
- [ ] CSRF vulnerabilities
- [ ] Insecure direct object references
- [ ] Security misconfiguration
- [ ] Missing encryption
- [ ] Insufficient logging
- [ ] Vulnerable dependencies
- [ ] Missing rate limiting

---

## Dependency Vulnerabilities

**Backend (Python):**
- [ ] `pip-audit` or `safety` run: ☐ Yes ☐ No
- High/Critical vulnerabilities found: _____
- Packages to update: ___________________________________________________

**Frontend (JavaScript):**
- [ ] `npm audit` run: ☐ Yes ☐ No
- High/Critical vulnerabilities found: _____
- Packages to update: ___________________________________________________

---

## Top 5 Security Fixes (Prioritized)

1. **________________________________** (Severity: Critical/High/Med, Effort: H/M/L)
   - Exploit: __________________________________________________________________
   - Fix: ______________________________________________________________________

2. **________________________________** (Severity: Critical/High/Med, Effort: H/M/L)
   - Exploit: __________________________________________________________________
   - Fix: ______________________________________________________________________

3. **________________________________** (Severity: Critical/High/Med, Effort: H/M/L)
   - Exploit: __________________________________________________________________
   - Fix: ______________________________________________________________________

4. **________________________________** (Severity: Critical/High/Med, Effort: H/M/L)
   - Exploit: __________________________________________________________________
   - Fix: ______________________________________________________________________

5. **________________________________** (Severity: Critical/High/Med, Effort: H/M/L)
   - Exploit: __________________________________________________________________
   - Fix: ______________________________________________________________________

---

## Compliance Requirements

- [ ] **GDPR:** ☐ Compliant ☐ Partial ☐ Non-Compliant
  - Issues: _________________________________________________________________

- [ ] **OWASP Top 10:** ☐ All Pass ☐ Some Fail ☐ Many Fail
  - Failed: _________________________________________________________________

- [ ] **Security Headers:** ☐ Configured ☐ Partial ☐ Missing
  - Missing: ________________________________________________________________

---

## Security Maturity Assessment

### Current Level: [____________________]

**Critical (1-2):** Severe vulnerabilities, immediate risk  
**High (3-4):** Multiple high-risk vulnerabilities  
**Medium (5-6):** Some security issues, improvements needed  
**Good (7-8):** Good security posture, minor improvements  
**Strong (9-10):** Excellent security, production-ready

### Path to Next Level:
- [ ] _______________________________________________________________________
- [ ] _______________________________________________________________________
- [ ] _______________________________________________________________________

---

## Quick Security Wins

1. _______________________________________________________________________
2. _______________________________________________________________________
3. _______________________________________________________________________

---

## Must-Fix Before Production

- [ ] _______________________________________________________________________
- [ ] _______________________________________________________________________
- [ ] _______________________________________________________________________
- [ ] _______________________________________________________________________

---

## Sign-off

**Reviewer:** ________________  **Date:** ________  **Time Spent:** _______ min

**Production Ready:** ☐ Yes (with minor fixes)  ☐ No (critical issues present)
