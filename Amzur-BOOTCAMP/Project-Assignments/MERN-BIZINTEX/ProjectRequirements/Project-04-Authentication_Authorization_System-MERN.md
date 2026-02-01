# **Project 4: Complete Authentication & Authorization System (MERN Stack)**

## **✅ COMPREHENSIVE COVERAGE CONFIRMATION**

**This project provides complete coverage of both Authentication AND Authorization:**

### **🔐 Authentication Components (User Identity Verification):**
- **Standard Login**: Username/password authentication with secure validation
- **Google SSO**: OAuth 2.0 integration with Google Sign-In for social authentication
- **Password Security**: bcrypt hashing with 12+ rounds for secure password storage
- **JWT Token Management**: Secure token generation, validation, and refresh mechanisms
- **Session Management**: User session tracking with secure token storage
- **Multi-Provider Support**: Google, Facebook, and LinkedIn OAuth integration

### **🛡️ Authorization Components (Access Control & Permissions):**
- **User and Admin Roles**: Two-tier role system (user/admin) as requested
- **Role-Based Access Control (RBAC)**: Complete RBAC implementation with role validation
- **Backend Authorization**: Admin-only API endpoints with proper 403 responses
- **Frontend Authorization**: Conditional UI rendering based on user roles
- **Admin Dashboard**: Separate administrative interface with management features
- **Privilege Protection**: Prevention of unauthorized role elevation attacks

## **Objective (Why?)**

Build a complete user authentication and authorization system with registration, login, role-based access control (RBAC), and protected routes in 6 days. This is your transition to production-ready full-stack development with the MERN stack. You will practice:

* **🔐 Authentication & Security**: Password hashing, JWT tokens, Google SSO, protected routes
* **🛡️ Authorization Systems**: Role-Based Access Control (RBAC) with user/admin roles and conditional UI
* **🏗️ Full-Stack Architecture**: Express.js backend + React frontend separation
* **💾 Database Management**: MongoDB with user data, sessions, roles, and social accounts
* **🎨 Modern Frontend**: React + Vite + Tailwind CSS development
* **🌐 Social Authentication**: OAuth 2.0 integration with Google, Facebook, LinkedIn

## **Core Requirements (Must-have)**

| Layer | Authentication Requirements | Authorization Requirements |
| ----- | -------------------------- | ------------------------- |
| **Backend** | **Express.js + MongoDB**<br/>• User registration endpoint with validation<br/>• Login endpoint with JWT token generation<br/>• Password hashing using bcrypt (12+ rounds)<br/>• Protected routes requiring authentication<br/>• OAuth 2.0 integration for Google SSO<br/>• JWT tokens containing user information<br/>• Token refresh mechanism | **Role-Based Access Control**<br/>• User roles (user/admin) in database<br/>• JWT tokens containing role claims<br/>• Admin-only API endpoints with proper authorization<br/>• Role-based permissions middleware<br/>• Privilege escalation prevention<br/>• Social login with role assignment<br/>• Role modification only by admins |
| **Frontend** | **React + Vite + Tailwind CSS**<br/>• Registration form with validation<br/>• Login form with error handling<br/>• Google Sign-In button and OAuth flow<br/>• JWT token management and storage<br/>• Protected route system<br/>• User profile management<br/>• Logout functionality | **Role-Based UI & Navigation**<br/>• Conditional UI rendering based on roles<br/>• Admin dashboard accessible only to admin users<br/>• Role-based navigation and menu systems<br/>• Admin-only features and components<br/>• User role display and management<br/>• Different views for different roles |
| **Database** | **MongoDB Schema**<br/>• Users collection with authentication data<br/>• Password storage (hashed with bcrypt)<br/>• Social accounts sub-document for OAuth<br/>• User sessions/token management<br/>• Proper indexing for email lookups | **Role & Permission Storage**<br/>• Role field in users collection (user/admin)<br/>• Role-based data access patterns<br/>• Proper indexing for role-based queries<br/>• Role inheritance for social accounts<br/>• Session tracking with role information<br/>• Default role assignment on registration |
| **Security** | **Authentication Security**<br/>• Password hashing (bcrypt, 12+ rounds)<br/>• JWT token authentication<br/>• OAuth 2.0 state validation and CSRF protection<br/>• Input validation and sanitization<br/>• Secure cookie and session handling<br/>• Token expiration and refresh | **Authorization Security**<br/>• Role validation in JWT tokens<br/>• Protected API endpoints with role checks<br/>• Prevention of privilege escalation attacks<br/>• Proper 403 Forbidden responses<br/>• Frontend authorization with security<br/>• Middleware-based role verification |

## **Technology Stack (MERN)**

### **Backend Stack**
- **Runtime**: Node.js 18+ (LTS)
- **Framework**: Express.js 4.x
- **Database**: MongoDB 6+ with Mongoose ODM
- **Authentication**: Passport.js (local strategy + OAuth strategies)
- **Password Hashing**: bcrypt
- **JWT**: jsonwebtoken library
- **OAuth**: Passport-Google-OAuth20, Passport-Facebook, Passport-LinkedIn-OAuth2
- **Validation**: express-validator
- **Security**: Helmet.js, express-rate-limit, cors
- **Environment**: dotenv

### **Frontend Stack**
- **Library**: React 18+
- **Build Tool**: Vite 5+
- **Styling**: Tailwind CSS 3+
- **Routing**: React Router v6
- **HTTP Client**: Axios
- **State Management**: Context API + useReducer
- **Form Handling**: React Hook Form (optional)
- **JWT Handling**: jwt-decode

### **Database**
- **MongoDB**: Document-based NoSQL database
- **Mongoose**: ODM for schema definition and validation
- **Indexing**: Unique indexes on email, compound indexes for queries
- **Validation**: Mongoose schema validation

## **Project Structure**

```
mern-auth-system/
├── client/                                    # React frontend
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Auth/
│   │   │   │   ├── LoginForm.jsx             # Login form component
│   │   │   │   ├── RegisterForm.jsx          # Registration form
│   │   │   │   ├── GoogleSignIn.jsx          # Google SSO button
│   │   │   │   ├── SocialLogin.jsx           # Social login buttons
│   │   │   │   └── ProtectedRoute.jsx        # Route protection HOC
│   │   │   ├── Admin/
│   │   │   │   ├── AdminDashboard.jsx        # Admin-only dashboard
│   │   │   │   ├── UserManagement.jsx        # Manage users
│   │   │   │   ├── RoleManager.jsx           # Role assignment
│   │   │   │   └── Analytics.jsx             # Admin analytics
│   │   │   ├── User/
│   │   │   │   ├── UserDashboard.jsx         # User dashboard
│   │   │   │   ├── UserProfile.jsx           # Profile management
│   │   │   │   └── Settings.jsx              # User settings
│   │   │   ├── Layout/
│   │   │   │   ├── Navbar.jsx                # Role-based navigation
│   │   │   │   ├── Sidebar.jsx               # Sidebar with role filtering
│   │   │   │   └── Footer.jsx
│   │   │   └── Common/
│   │   │       ├── LoadingSpinner.jsx
│   │   │       ├── ErrorBoundary.jsx
│   │   │       └── RoleBasedComponent.jsx    # Conditional rendering
│   │   ├── context/
│   │   │   └── AuthContext.jsx               # Auth state management
│   │   ├── services/
│   │   │   ├── authService.js                # Auth API calls
│   │   │   ├── userService.js                # User management API
│   │   │   └── adminService.js               # Admin API calls
│   │   ├── hooks/
│   │   │   ├── useAuth.js                    # Auth hook
│   │   │   ├── useRole.js                    # Role checking hook
│   │   │   └── useProtectedRoute.js          # Route protection
│   │   ├── utils/
│   │   │   ├── tokenManager.js               # JWT token handling
│   │   │   ├── validators.js                 # Form validation
│   │   │   └── roleHelpers.js                # Role checking utilities
│   │   ├── pages/
│   │   │   ├── Login.jsx
│   │   │   ├── Register.jsx
│   │   │   ├── Dashboard.jsx                 # Role-based dashboard
│   │   │   ├── Profile.jsx
│   │   │   ├── AdminPanel.jsx                # Admin-only page
│   │   │   └── Unauthorized.jsx              # 403 page
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   └── index.css
│   ├── .env.example
│   ├── .gitignore
│   ├── package.json
│   ├── tailwind.config.js
│   └── vite.config.js
│
├── server/                                    # Express backend
│   ├── config/
│   │   ├── database.js                       # MongoDB connection
│   │   ├── passport.js                       # Passport strategies
│   │   └── env.js                            # Environment config
│   ├── models/
│   │   ├── User.js                           # User Mongoose model
│   │   └── Session.js                        # Session model (optional)
│   ├── controllers/
│   │   ├── authController.js                 # Auth logic
│   │   ├── userController.js                 # User management
│   │   └── adminController.js                # Admin operations
│   ├── routes/
│   │   ├── authRoutes.js                     # Auth endpoints
│   │   ├── userRoutes.js                     # User endpoints
│   │   └── adminRoutes.js                    # Admin endpoints
│   ├── middleware/
│   │   ├── auth.js                           # JWT verification
│   │   ├── roleCheck.js                      # RBAC middleware
│   │   ├── validation.js                     # Input validation
│   │   ├── errorHandler.js                   # Error handling
│   │   └── rateLimiter.js                    # Rate limiting
│   ├── utils/
│   │   ├── jwt.js                            # JWT utilities
│   │   ├── password.js                       # Password hashing
│   │   └── validators.js                     # Validation helpers
│   ├── services/
│   │   ├── authService.js                    # Auth business logic
│   │   ├── userService.js                    # User operations
│   │   └── oauthService.js                   # OAuth handling
│   ├── scripts/
│   │   └── seedAdmin.js                      # Create admin users
│   ├── .env.example
│   ├── .gitignore
│   ├── package.json
│   └── server.js                              # Entry point
│
├── .gitignore
├── README.md
└── package.json                               # Root scripts (optional)
```

## **Development Approach: Milestone-Based Progression**

**Philosophy**: Focus on **deliverable quality** and **comprehensive review compliance** rather than rigid timelines. Each milestone must pass all relevant review templates from our Templates folder before proceeding.

### **Milestone 1: Core Authentication Foundation**

#### **Deliverables:**
- [ ] MongoDB database setup with user schema design
- [ ] Express.js backend with Mongoose models
- [ ] React + Vite frontend with Tailwind CSS styling
- [ ] Basic API endpoints for user registration and authentication
- [ ] Environment configuration and security setup
- [ ] JWT token generation and validation system
- [ ] Password hashing with bcrypt (12+ rounds)

#### **Review Requirements (Must Pass to Proceed):**
- [ ] **Security Review**: Database security, environment setup, input validation
- [ ] **Architecture Review**: Full-stack architecture and component separation
- [ ] **Code Quality Review**: Clean code organization and documentation

### **Milestone 2: Authentication System & JWT Implementation**

#### **Deliverables:**
- [ ] Complete JWT authentication system with token management
- [ ] Password hashing with bcrypt and security best practices
- [ ] Protected API routes with authentication middleware
- [ ] React authentication context and route protection
- [ ] Login/registration forms with comprehensive validation
- [ ] Protected dashboard with user profile functionality
- [ ] Token refresh mechanism
- [ ] Logout functionality with token invalidation

#### **Review Requirements (Must Pass to Proceed):**
- [ ] **Security Review**: Authentication security, JWT implementation, password handling
- [ ] **Code Quality Review**: Clean authentication patterns and error handling
- [ ] **Performance Review**: Efficient authentication flows and token management

### **Milestone 3: Role-Based Access Control (RBAC) Implementation**

#### **Deliverables:**
- [ ] User model updated with role field (default: 'user', options: 'user', 'admin')
- [ ] JWT tokens enhanced with role claims
- [ ] Role-protected API endpoints (admin-only routes)
- [ ] Backend authorization middleware and role checking
- [ ] Admin user seeding script for testing
- [ ] Role validation on all protected endpoints
- [ ] Privilege escalation prevention mechanisms

#### **Review Requirements (Must Pass to Proceed):**
- [ ] **Security Review**: Authorization security, role validation, privilege escalation prevention
- [ ] **Architecture Review**: Clean RBAC implementation and scalability
- [ ] **Code Quality Review**: Maintainable authorization patterns

### **Milestone 4: Frontend Authorization & Admin Features**

#### **Deliverables:**
- [ ] Updated React authentication context with role management
- [ ] Conditional UI rendering based on user roles
- [ ] Admin dashboard accessible only to admin users
- [ ] Role-based navigation and menu systems
- [ ] Admin-only features and components (user management)
- [ ] Comprehensive role-based testing scenarios
- [ ] Unauthorized access handling (403 page)
- [ ] Different dashboard views for user vs admin

#### **Review Requirements (Must Pass to Proceed):**
- [ ] **Security Review**: Frontend authorization security, UI access control
- [ ] **User Experience Review**: Seamless role-based user experience
- [ ] **Code Quality Review**: Clean conditional rendering and state management

### **Milestone 5: Social Authentication Integration**

#### **Deliverables:**
- [ ] OAuth 2.0 implementation using Passport.js
- [ ] Integration with specific providers:
  - [ ] Google Sign-In implementation (Passport-Google-OAuth20)
  - [ ] Facebook Login integration (Passport-Facebook)
  - [ ] LinkedIn OAuth authentication (Passport-LinkedIn-OAuth2)
- [ ] Social account storage in user model
- [ ] Account linking and profile data synchronization
- [ ] Social login buttons in frontend UI
- [ ] User profile merging strategy with role preservation
- [ ] OAuth callback handling and error management

#### **Review Requirements (Must Pass to Proceed):**
- [ ] **Security Review**: OAuth implementation, state validation, CSRF protection
- [ ] **Code Quality Review**: Clean provider integration patterns
- [ ] **User Experience Review**: Seamless social login flows and error handling

### **Milestone 6: Production Readiness & Advanced Features**

#### **Deliverables:**
- [ ] Production-ready dashboard interface with role-appropriate features
- [ ] Advanced user management for admin users
- [ ] Session management and comprehensive logout functionality
- [ ] Comprehensive error handling and user feedback
- [ ] Production deployment preparation and documentation
- [ ] Role-based analytics and monitoring capabilities
- [ ] Performance optimization (database indexes, caching)
- [ ] Security hardening (rate limiting, helmet.js)

#### **Review Requirements (Must Pass for Project Completion):**
- [ ] **Architecture Review**: Complete full-stack architecture assessment
- [ ] **Security Review**: Production security assessment and penetration testing
- [ ] **Code Quality Review**: Production-ready code quality standards
- [ ] **Performance Review**: Frontend/backend performance optimization
- [ ] **User Experience Review**: Complete user journey testing for all roles

### **Milestone Progression Rules:**
- **Cannot advance** to next milestone without passing all review requirements
- **Flexible timing** allows for learning at individual pace
- **Quality gates** ensure each milestone meets professional standards
- **Mentor support** available for concept clarification and review failures

## **Technical Specifications**

### **Database Schema Design (MongoDB + Mongoose)**

#### **User Model**

**Required Fields:**
Your User schema should include:

**Core Fields:**
- `email` (String, required, unique, lowercase, trim): User's email address
- `password` (String, required for local auth): Hashed password (bcrypt)
- `firstName` (String, required): User's first name
- `lastName` (String, required): User's last name
- `role` (String, enum: ['user', 'admin'], default: 'user'): User role for RBAC
- `isActive` (Boolean, default: true): Account activation status
- `isEmailVerified` (Boolean, default: false): Email verification status

**Social Authentication:**
- `googleId` (String, unique, sparse): Google OAuth ID
- `facebookId` (String, unique, sparse): Facebook OAuth ID
- `linkedinId` (String, unique, sparse): LinkedIn OAuth ID
- `profilePicture` (String): URL to profile photo
- `authProvider` (String, enum: ['local', 'google', 'facebook', 'linkedin']): Primary auth method

**Timestamps:**
- `createdAt` (Date): Account creation
- `updatedAt` (Date): Last profile update
- `lastLogin` (Date): Last login timestamp

**Security:**
- `refreshToken` (String): For token refresh (optional)
- `resetPasswordToken` (String): Password reset token
- `resetPasswordExpires` (Date): Token expiration

**Schema Validation:**
- Email: valid email format, minimum 5 characters
- Password: minimum 8 characters (before hashing)
- Names: minimum 2 characters, maximum 50
- Role: must be 'user' or 'admin'

**Schema Methods:**
- `comparePassword(candidatePassword)`: Compare entered password with hashed
- `generateJWT()`: Generate JWT token with user info and role
- `toJSON()`: Remove password and sensitive fields when converting to JSON
- `can(action)`: Check if user has permission for action (based on role)

**Schema Statics:**
- `findByEmail(email)`: Find user by email
- `createAdmin(userData)`: Create admin user with validation
- `updateRole(userId, newRole)`: Update user role (admin only)

**Indexes:**
- Unique index on: email
- Sparse unique indexes on: googleId, facebookId, linkedinId
- Compound index on: role + isActive (for role-based queries)
- Index on: lastLogin (for analytics)

### **Backend API Structure**

#### **Authentication Endpoints**

**1. Register**
```
POST /api/auth/register
Content-Type: application/json

Request:
{
  "email": "user@example.com",
  "password": "SecurePass123!",
  "firstName": "John",
  "lastName": "Doe"
}

Success Response (201):
{
  "success": true,
  "data": {
    "user": {
      "id": "...",
      "email": "user@example.com",
      "firstName": "John",
      "lastName": "Doe",
      "role": "user"
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": "24h"
  },
  "message": "Registration successful"
}

Error Response (400):
{
  "success": false,
  "error": "Email already registered",
  "code": "EMAIL_EXISTS"
}
```

**2. Login**
```
POST /api/auth/login
Content-Type: application/json

Request:
{
  "email": "user@example.com",
  "password": "SecurePass123!"
}

Success Response (200):
{
  "success": true,
  "data": {
    "user": {
      "id": "...",
      "email": "user@example.com",
      "firstName": "John",
      "lastName": "Doe",
      "role": "user"
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "...",  // optional
    "expiresIn": "24h"
  },
  "message": "Login successful"
}

Error Response (401):
{
  "success": false,
  "error": "Invalid credentials",
  "code": "INVALID_CREDENTIALS"
}
```

**3. Google OAuth**
```
GET /api/auth/google
→ Redirects to Google OAuth consent screen

GET /api/auth/google/callback?code=...
→ Handles OAuth callback, creates/updates user, returns JWT

Success: Redirects to frontend with token in URL
Failure: Redirects to frontend login with error
```

**4. Logout**
```
POST /api/auth/logout
Authorization: Bearer {token}

Success Response (200):
{
  "success": true,
  "message": "Logged out successfully"
}
```

**5. Refresh Token (Optional)**
```
POST /api/auth/refresh
Content-Type: application/json

Request:
{
  "refreshToken": "..."
}

Success Response (200):
{
  "success": true,
  "data": {
    "token": "new_access_token",
    "expiresIn": "24h"
  }
}
```

**6. Get Current User**
```
GET /api/auth/me
Authorization: Bearer {token}

Success Response (200):
{
  "success": true,
  "data": {
    "user": {
      "id": "...",
      "email": "user@example.com",
      "firstName": "John",
      "lastName": "Doe",
      "role": "user",
      "profilePicture": "...",
      "authProvider": "local",
      "createdAt": "2024-01-30T10:00:00.000Z"
    }
  }
}
```

#### **User Endpoints (Protected)**

**1. Get Profile**
```
GET /api/users/profile
Authorization: Bearer {token}

Success Response (200):
{
  "success": true,
  "data": {
    "profile": { ...user details... }
  }
}
```

**2. Update Profile**
```
PUT /api/users/profile
Authorization: Bearer {token}
Content-Type: application/json

Request:
{
  "firstName": "Jane",
  "lastName": "Smith"
}

Success Response (200):
{
  "success": true,
  "data": {
    "user": { ...updated user... }
  },
  "message": "Profile updated successfully"
}
```

**3. Change Password**
```
PUT /api/users/password
Authorization: Bearer {token}
Content-Type: application/json

Request:
{
  "currentPassword": "OldPass123!",
  "newPassword": "NewPass456!"
}

Success Response (200):
{
  "success": true,
  "message": "Password changed successfully"
}
```

#### **Admin Endpoints (Role-Protected)**

**1. Get All Users (Admin Only)**
```
GET /api/admin/users?page=1&limit=10&role=user
Authorization: Bearer {token}
Required Role: admin

Success Response (200):
{
  "success": true,
  "data": {
    "users": [
      {
        "id": "...",
        "email": "user@example.com",
        "firstName": "John",
        "lastName": "Doe",
        "role": "user",
        "isActive": true,
        "createdAt": "..."
      }
    ],
    "pagination": {
      "total": 50,
      "page": 1,
      "limit": 10,
      "pages": 5
    }
  }
}

Error Response (403 - Non-admin):
{
  "success": false,
  "error": "Access denied. Admin privileges required.",
  "code": "FORBIDDEN"
}
```

**2. Update User Role (Admin Only)**
```
PUT /api/admin/users/:userId/role
Authorization: Bearer {token}
Required Role: admin
Content-Type: application/json

Request:
{
  "role": "admin"
}

Success Response (200):
{
  "success": true,
  "data": {
    "user": {
      "id": "...",
      "role": "admin"
    }
  },
  "message": "User role updated successfully"
}

Error Response (403):
{
  "success": false,
  "error": "Cannot modify own role",
  "code": "SELF_ROLE_MODIFICATION"
}
```

**3. Delete User (Admin Only)**
```
DELETE /api/admin/users/:userId
Authorization: Bearer {token}
Required Role: admin

Success Response (200):
{
  "success": true,
  "message": "User deleted successfully"
}
```

**4. Get Admin Analytics (Admin Only)**
```
GET /api/admin/analytics
Authorization: Bearer {token}
Required Role: admin

Success Response (200):
{
  "success": true,
  "data": {
    "totalUsers": 150,
    "activeUsers": 125,
    "adminUsers": 5,
    "newUsersThisMonth": 25,
    "authProviders": {
      "local": 100,
      "google": 40,
      "facebook": 8,
      "linkedin": 2
    }
  }
}
```

### **Authentication Architecture**

#### **Password Security**

**Hashing Requirements:**
- Use bcrypt with minimum 12 salt rounds
- Hash passwords before storing in database
- Never store plain text passwords
- Implement password strength validation

**Password Policies:**
Your application should enforce:
- Minimum 8 characters length
- At least one uppercase letter
- At least one lowercase letter
- At least one number
- At least one special character (optional but recommended)

**Password Comparison:**
- Use bcrypt.compare() for verification
- Implement constant-time comparison
- Handle timing attacks appropriately
- Log failed login attempts (for security monitoring)

#### **JWT Token Management**

**Token Structure:**
Your JWT payload should include:
```json
{
  "userId": "user_id_here",
  "email": "user@example.com",
  "role": "user",
  "iat": 1234567890,
  "exp": 1234654290
}
```

**Token Generation:**
- Use strong secret key (minimum 32 characters, store in environment variable)
- Set appropriate expiration (recommended: 1-24 hours for access tokens)
- Include necessary user claims (id, email, role)
- Sign with HS256 algorithm
- Generate refresh tokens for long-term sessions (optional)

**Token Validation:**
- Verify signature with secret key
- Check expiration time
- Validate token structure
- Extract and validate user claims
- Handle expired tokens gracefully

**Token Storage (Frontend):**
- Store in httpOnly cookies (most secure) OR
- Store in localStorage (simpler but less secure)
- Never store in sessionStorage for persistent login
- Clear on logout
- Implement token refresh before expiration

#### **Authentication Middleware**

**JWT Verification Middleware:**
Your middleware should:
1. Extract token from Authorization header (Bearer token)
2. Verify token signature and expiration
3. Decode payload and extract user information
4. Attach user object to request (req.user)
5. Call next() if valid, return 401 if invalid
6. Handle missing tokens appropriately

**Protected Route Pattern:**
```javascript
// Example middleware usage (for reference only)
router.get('/protected-endpoint', authenticateJWT, (req, res) => {
  // req.user is now available
  // Contains: { userId, email, role, ... }
});
```

### **Authorization Architecture (RBAC)**

#### **Role-Based Access Control Requirements**

**Role Definitions:**
- **user** (default): Standard user with basic permissions
- **admin**: Administrator with elevated permissions

**Permission Matrix:**

| Feature | User | Admin |
|---------|------|-------|
| View own profile | ✅ | ✅ |
| Edit own profile | ✅ | ✅ |
| Change own password | ✅ | ✅ |
| View all users | ❌ | ✅ |
| Edit other users | ❌ | ✅ |
| Delete users | ❌ | ✅ |
| Assign roles | ❌ | ✅ |
| View analytics | ❌ | ✅ |
| Access admin dashboard | ❌ | ✅ |

#### **Authorization Middleware**

**Role Checking Middleware:**
Your role middleware should:
1. Verify user is authenticated (check req.user exists)
2. Extract user role from req.user
3. Compare against required role(s)
4. Return 403 if unauthorized, call next() if authorized
5. Log unauthorized access attempts

**Flexible Role Checking:**
Support multiple authorization patterns:
- Require specific role: `requireRole('admin')`
- Require any of multiple roles: `requireAnyRole(['admin', 'moderator'])`
- Custom permission check: `requirePermission('edit_users')`

**Privilege Escalation Prevention:**
- Users cannot change their own role
- Only admins can modify roles
- Validate role in JWT matches database
- Implement audit logging for role changes
- Rate limit role modification endpoints

#### **Frontend Authorization**

**Role-Based Component Rendering:**
Create reusable components that:
- Check user role from auth context
- Conditionally render based on role
- Hide/show navigation items
- Disable buttons for unauthorized actions
- Display appropriate UI for each role

**Protected Routes:**
Implement route guards that:
- Check authentication status
- Check user role
- Redirect to login if not authenticated
- Redirect to 403 page if authenticated but wrong role
- Allow access if authorized

**Examples of Conditional Rendering:**
- Show "Admin Panel" link only to admins
- Display user count to admins only
- Enable "Delete User" button for admins
- Show different dashboard layouts by role

### **OAuth 2.0 Integration**

#### **Passport.js Strategy Configuration**

**Google OAuth Strategy:**
Configure with:
- Client ID (from Google Cloud Console)
- Client Secret (from Google Cloud Console)
- Callback URL (e.g., http://localhost:5000/api/auth/google/callback)
- Scope: profile, email
- State parameter for CSRF protection

**OAuth Flow:**
1. User clicks "Sign in with Google"
2. Frontend redirects to `/api/auth/google`
3. Backend redirects to Google OAuth consent
4. User authorizes application
5. Google redirects to callback URL with code
6. Backend exchanges code for access token
7. Backend retrieves user profile from Google
8. Backend creates/updates user in database
9. Backend generates JWT token
10. Backend redirects to frontend with token
11. Frontend stores token and authenticates user

**Profile Data Handling:**
Extract from OAuth provider:
- Email (primary identifier)
- First name and last name
- Profile picture URL
- Provider-specific ID (googleId, facebookId, etc.)

**Account Linking:**
- If email exists: link OAuth account to existing user
- If email doesn't exist: create new user
- Store provider ID for future logins
- Preserve existing role when linking

**Security Considerations:**
- Validate state parameter to prevent CSRF
- Verify redirect_uri matches configured URL
- Validate token signature from provider
- Handle OAuth errors gracefully
- Log authentication events

### **Security Requirements**

#### **Input Validation**

**Backend Validation:**
Validate all user inputs:
- Email: valid format, not empty, maximum length
- Password: strength requirements, minimum length
- Names: no special characters, appropriate length
- Role: must be valid enum value
- All inputs: sanitize to prevent injection

**Frontend Validation:**
- Real-time validation feedback
- Clear error messages
- Prevent form submission with invalid data
- Client-side regex validation
- Confirm password matching

#### **CORS Configuration**

Configure CORS to:
- Allow requests from frontend origin only
- Allow credentials (for cookies)
- Specify allowed methods (GET, POST, PUT, DELETE)
- Set appropriate headers
- Restrict to specific origins in production


#### **Security Headers (Helmet.js)**

Configure security headers:
- Content Security Policy
- X-Frame-Options: DENY
- X-Content-Type-Options: nosniff
- Strict-Transport-Security
- X-XSS-Protection

#### **Error Handling**

**Secure Error Messages:**
- Don't expose internal errors to users
- Generic messages for authentication failures
- Log detailed errors server-side
- Don't reveal whether email exists during login
- Sanitize error responses

**Error Response Format:**
```json
{
  "success": false,
  "error": "User-friendly error message",
  "code": "ERROR_CODE",
  "timestamp": "2024-01-30T10:30:00.000Z"
}
```

### **Frontend Architecture**

#### **Authentication Context**

**Auth Context State:**
Manage global authentication state:
- `user`: Current user object (null if not authenticated)
- `token`: JWT access token
- `isAuthenticated`: Boolean authentication status
- `isLoading`: Loading state during auth operations
- `error`: Authentication error messages

**Auth Context Actions:**
- `login(email, password)`: Authenticate user
- `register(userData)`: Create new account
- `logout()`: Clear authentication state
- `googleLogin()`: Initiate Google OAuth
- `updateProfile(data)`: Update user information
- `refreshToken()`: Refresh access token

**Context Provider Pattern:**
- Wrap application with AuthProvider
- Provide auth state and methods to all components
- Persist authentication across page reloads
- Handle token expiration
- Auto-logout on invalid token

#### **Protected Routes Implementation**

**Route Protection Strategies:**

**1. ProtectedRoute Component:**
- Wrap routes that require authentication
- Check authentication status
- Redirect to login if not authenticated
- Allow access if authenticated

**2. RoleProtectedRoute Component:**
- Wrap routes that require specific roles
- Check authentication AND role
- Redirect to login if not authenticated
- Redirect to 403 page if wrong role
- Allow access if authorized

**3. ConditionalRoute:**
- Different routes based on role
- Redirect admins to admin dashboard
- Redirect users to user dashboard
- Automatic routing based on permissions

#### **Role-Based UI Components**

**RoleBasedComponent:**
Create wrapper component that:
- Accepts allowedRoles prop
- Checks current user role
- Renders children if authorized
- Returns null if unauthorized

**Usage Examples:**
- Wrap admin features in RoleBasedComponent
- Show/hide navigation items
- Enable/disable action buttons
- Display different content by role

#### **Form Handling**

**Registration Form:**
- Email input with validation
- Password input with strength indicator
- Confirm password field
- First name and last name
- Terms acceptance checkbox
- Submit button with loading state
- Error display for validation issues

**Login Form:**
- Email input
- Password input
- Remember me checkbox (optional)
- Forgot password link
- Submit button
- Social login buttons
- Error display

**Validation:**
- Real-time validation feedback
- Clear error messages
- Prevent submission with errors
- Show success states
- Handle backend validation errors

### **Environment Variables**

#### **Server (.env)**
```
# Server Configuration
PORT=5000
NODE_ENV=development

# MongoDB Configuration
MONGODB_URI=mongodb://localhost:27017/auth-system
MONGODB_DB_NAME=auth_system

# JWT Configuration
JWT_SECRET=your_super_secret_jwt_key_min_32_characters
JWT_EXPIRES_IN=24h
JWT_REFRESH_SECRET=your_refresh_token_secret
JWT_REFRESH_EXPIRES_IN=7d

# bcrypt Configuration
BCRYPT_SALT_ROUNDS=12

# OAuth - Google
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
GOOGLE_CALLBACK_URL=http://localhost:5000/api/auth/google/callback

# OAuth - Facebook (optional)
FACEBOOK_APP_ID=your_facebook_app_id
FACEBOOK_APP_SECRET=your_facebook_app_secret
FACEBOOK_CALLBACK_URL=http://localhost:5000/api/auth/facebook/callback

# OAuth - LinkedIn (optional)
LINKEDIN_CLIENT_ID=your_linkedin_client_id
LINKEDIN_CLIENT_SECRET=your_linkedin_client_secret
LINKEDIN_CALLBACK_URL=http://localhost:5000/api/auth/linkedin/callback

# Frontend URL
CLIENT_URL=http://localhost:5173

# CORS Configuration
CORS_ORIGIN=http://localhost:5173

# Rate Limiting
RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=100

# Email Configuration (optional, for email verification)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASS=your_app_password
```

#### **Client (.env)**
```
# API Configuration
VITE_API_BASE_URL=http://localhost:5000

# OAuth Configuration
VITE_GOOGLE_CLIENT_ID=your_google_client_id

# App Configuration
VITE_APP_NAME=Auth System
VITE_TOKEN_STORAGE=localStorage
```

### **Documentation Requirements**

#### **README.md Structure**

**Project Overview:**
- Description of the authentication system
- Technologies used (MERN stack)
- Key features (auth, authz, OAuth, RBAC)
- Screenshots of application

**Prerequisites:**
- Node.js 18+
- MongoDB 6+
- Google OAuth credentials
- Git

**Installation:**
- Clone repository
- Install dependencies (client + server)
- Setup MongoDB
- Configure environment variables
- Seed admin user
- Run application

**OAuth Setup:**
- Google Cloud Console setup
- Facebook Developer setup
- LinkedIn Developer setup
- Callback URL configuration

**Usage Guide:**
- Registration process
- Login process
- Role management
- OAuth login
- Admin features

**API Documentation:**
- All endpoints with examples
- Authentication requirements
- Role requirements
- Request/response formats

**Security Features:**
- Password hashing details
- JWT implementation
- RBAC system
- OAuth security

**Testing:**
- Test credentials
- Admin account setup
- Role testing scenarios
- OAuth testing

**Deployment:**
- MongoDB Atlas setup
- Backend deployment
- Frontend deployment
- Environment configuration

## **Deliverables**

1. **GitHub Repository** (public or invite @mentor)
   - Complete MERN stack application
   - Comprehensive documentation
   - Clean commit history
   - Proper .gitignore

2. **Live Demo** (choose one)
   - Local demo with multiple user roles
   - Deployed application URL
   - Screen recording

3. **SECURITY_DEMO.md**
   - Test user accounts (user and admin)
   - Security features demonstration
   - RBAC testing scenarios
   - OAuth flow screenshots
   - Penetration testing results (basic)

4. **Technical_Learnings.md**
   - Authentication implementation insights
   - Authorization/RBAC learnings
   - OAuth integration experience
   - Security best practices learned
   - Challenges and solutions
   - Time breakdown by milestone

## **Evaluation Rubric (100 Points)**

| Criterion | Points | Details |
| ----- | :---: | ----- |
| **Authentication** | 30 pts | ✓ Registration and login working (10 pts) <br> ✓ JWT token implementation (10 pts) <br> ✓ Password security with bcrypt (5 pts) <br> ✓ Protected routes (5 pts) |
| **Authorization (RBAC)** | 25 pts | ✓ Role-based access control working (10 pts) <br> ✓ Admin-only endpoints protected (8 pts) <br> ✓ Frontend role-based UI (7 pts) |
| **OAuth Integration** | 20 pts | ✓ Google OAuth working (12 pts) <br> ✓ Account linking (5 pts) <br> ✓ Social login UI (3 pts) |
| **Security** | 15 pts | ✓ Input validation (5 pts) <br> ✓ Security headers (5 pts) <br> ✓ No vulnerabilities (5 pts) |
| **Code Quality** | 10 pts | ✓ Clean architecture (4 pts) <br> ✓ Documentation (3 pts) <br> ✓ Error handling (3 pts) |

### **Minimum Passing Requirements**
- Total score ≥ 70/100
- Authentication score ≥ 20/30
- Authorization score ≥ 15/25
- Security review passed
- All core features working

## **Testing Scenarios**

### **Authentication Testing**
- [ ] User can register with valid credentials
- [ ] Email uniqueness enforced
- [ ] Password strength validation working
- [ ] User can login with correct credentials
- [ ] Invalid credentials rejected
- [ ] JWT token generated on successful login
- [ ] Protected routes require authentication
- [ ] Logout clears authentication
- [ ] Token expiration handled properly

### **Authorization Testing**
- [ ] Users have 'user' role by default
- [ ] Admin users can access admin endpoints
- [ ] Regular users blocked from admin endpoints (403)
- [ ] JWT contains role claims
- [ ] Frontend shows role-appropriate UI
- [ ] Admin dashboard only accessible to admins
- [ ] Users cannot elevate own privileges
- [ ] Role changes reflected immediately

### **OAuth Testing**
- [ ] Google Sign-In button displayed
- [ ] OAuth flow completes successfully
- [ ] User created/updated in database
- [ ] Profile data synced from Google
- [ ] JWT token generated after OAuth
- [ ] Account linking works correctly
- [ ] Role preserved during OAuth login

### **Security Testing**
- [ ] Passwords hashed with bcrypt (12+ rounds)
- [ ] JWT tokens signed with strong secret
- [ ] Input validation prevents injection
- [ ] CORS configured correctly
- [ ] Rate limiting active
- [ ] Security headers present
- [ ] No sensitive data exposed in errors

### **Integration Testing**
- [ ] Complete user registration flow
- [ ] Complete login flow
- [ ] OAuth complete flow
- [ ] Admin managing users
- [ ] Role-based dashboard access
- [ ] Token refresh (if implemented)

## **Common Pitfalls to Avoid**

### **Authentication Issues**
- ❌ Storing passwords in plain text
- ❌ Weak JWT secrets
- ❌ Not validating token expiration
- ❌ Exposing sensitive user data
- ❌ No password strength requirements
- ❌ Revealing whether email exists

### **Authorization Issues**
- ❌ Not validating roles in JWT
- ❌ Client-side only authorization
- ❌ Allowing users to modify own role
- ❌ Not protecting all admin endpoints
- ❌ Missing 403 responses
- ❌ Inconsistent role checking

### **OAuth Issues**
- ❌ Not validating state parameter
- ❌ Storing OAuth tokens insecurely
- ❌ Not handling OAuth errors
- ❌ Missing CSRF protection
- ❌ Not validating redirect URLs

### **Security Issues**
- ❌ No input validation
- ❌ Missing rate limiting
- ❌ No CORS configuration
- ❌ Exposing stack traces
- ❌ Weak session management
- ❌ No security headers

### **Frontend Issues**
- ❌ Storing tokens in localStorage without consideration
- ❌ Not clearing state on logout
- ❌ Missing loading states
- ❌ Poor error handling
- ❌ Not validating forms
- ❌ Inconsistent role checks

## **Success Checklist**

### **✅ Authentication Success Criteria**
- [ ] User registration working with validation
- [ ] Login working with JWT generation
- [ ] Password hashed with bcrypt (12+ rounds)
- [ ] Protected routes require authentication
- [ ] Logout functionality working
- [ ] Token management implemented
- [ ] Error handling comprehensive

### **✅ Authorization Success Criteria**
- [ ] RBAC implemented with user/admin roles
- [ ] Admin endpoints protected (403 for non-admins)
- [ ] Frontend UI conditional on roles
- [ ] Admin dashboard functional
- [ ] Users cannot elevate privileges
- [ ] Role changes work correctly
- [ ] JWT contains role claims

### **✅ OAuth Success Criteria**
- [ ] Google OAuth working end-to-end
- [ ] Account linking implemented
- [ ] Profile data synced
- [ ] Social login buttons working
- [ ] OAuth errors handled
- [ ] State validation present

### **✅ Security Success Criteria**
- [ ] Input validation on all endpoints
- [ ] CORS configured properly
- [ ] Rate limiting active
- [ ] Security headers set (Helmet.js)
- [ ] No secrets in repository
- [ ] Secure error messages
- [ ] MongoDB credentials protected

### **✅ Code Quality Criteria**
- [ ] Clean project structure
- [ ] Comprehensive documentation
- [ ] Consistent code style
- [ ] Error handling throughout
- [ ] No code duplication
- [ ] Meaningful names

## **Quick Start Resources**

### **MERN Authentication**
- **Passport.js**: http://www.passportjs.org/
- **JWT**: https://jwt.io/introduction
- **bcrypt**: https://www.npmjs.com/package/bcrypt
- **MongoDB User Authentication**: https://www.mongodb.com/blog/post/password-authentication-with-mongoose-part-1

### **OAuth 2.0**
- **Google OAuth Setup**: https://developers.google.com/identity/protocols/oauth2
- **Passport Google OAuth20**: https://www.passportjs.org/packages/passport-google-oauth20/
- **OAuth Best Practices**: https://oauth.net/2/

### **RBAC**
- **RBAC Concepts**: https://auth0.com/intro-to-iam/what-is-role-based-access-control-rbac/
- **Node.js RBAC**: https://blog.logrocket.com/how-to-implement-rbac-node-js/

### **Security**
- **Express Security**: https://expressjs.com/en/advanced/best-practice-security.html
- **Helmet.js**: https://helmetjs.github.io/
- **OWASP Top 10**: https://owasp.org/www-project-top-ten/

### **Frontend**
- **React Router Protected Routes**: https://ui.dev/react-router-protected-routes-authentication
- **React Context**: https://react.dev/reference/react/useContext
- **React Hook Form**: https://react-hook-form.com/

## **FAQ**

### **Authentication Questions:**

**Q: Should I use cookies or localStorage for JWT tokens?**  
A: httpOnly cookies are more secure (immune to XSS), but localStorage is simpler. For learning, localStorage is acceptable. For production, prefer httpOnly cookies.

**Q: How long should JWT tokens last?**  
A: Access tokens: 15 minutes to 24 hours. Refresh tokens: 7 days to 30 days. Balance security (shorter) vs UX (longer). For this project, 24 hours is reasonable.

**Q: What if a user forgets their password?**  
A: Implement password reset flow (send email with reset link). This is a stretch goal. For core requirements, users can't reset passwords.

**Q: Should I implement email verification?**  
A: Email verification is a valuable stretch goal but not required for core project. Focus on authentication and authorization first.

### **Authorization Questions:**

**Q: Can users have multiple roles?**  
A: For this project, each user has one role (user or admin). Multiple roles is an advanced feature you can add later.

**Q: How do I create the first admin user?**  
A: Use a seeding script that directly creates an admin in the database. See server/scripts/seedAdmin.js pattern.

**Q: Can admin users do everything regular users can do?**  
A: Yes, admins should have all user permissions PLUS additional admin permissions. Implement this in your permission checking logic.

**Q: What if JWT contains old role after role change?**  
A: Old tokens remain valid until expiration. User must re-login to get new token with updated role. This is acceptable behavior.

### **OAuth Questions:**

**Q: Do I need to implement all OAuth providers?**  
A: Google is required. Facebook and LinkedIn are stretch goals. Start with Google, add others if time permits.

**Q: How do I test OAuth locally?**  
A: Use http://localhost:5173 for frontend and http://localhost:5000 for backend. Add these to OAuth provider's authorized origins/redirect URIs.

**Q: What if user signs in with Google after registering with email?**  
A: Link the Google account to existing user by matching email. User can then login with either method.

### **Database Questions:**

**Q: Why MongoDB instead of PostgreSQL?**  
A: MongoDB is part of the MERN stack and simpler for this learning project. PostgreSQL is better for relational data but adds complexity.

**Q: Should I use Mongoose or native MongoDB driver?**  
A: Use Mongoose. It provides schema validation, middleware, and better developer experience.

## **Measurable Goals & Review Template Compliance**

### **Primary Objectives**
- [ ] **Security Excellence**: Pass Security Review with 9.0/10+ score (critical)
- [ ] **Architecture Quality**: Pass Architecture Review with 8.5/10+ score
- [ ] **Authentication Working**: Complete auth flow with JWT and OAuth
- [ ] **Authorization Working**: RBAC with user/admin roles functional
- [ ] **Performance Standards**: Sub-200ms authentication response times
- [ ] **Code Quality**: Pass Code Quality Review with 8.5/10+ score

### **Review Template Integration**

#### **Security Review Requirements (Critical for Auth Systems)**
```
security_criteria = {
    "authentication_security": {"target": 95, "weight": 25},     # JWT, bcrypt, secure flows
    "authorization_security": {"target": 95, "weight": 25},      # RBAC, role validation
    "input_validation": {"target": 90, "weight": 20},            # Comprehensive validation
    "session_management": {"target": 90, "weight": 15},          # Secure session handling
    "data_protection": {"target": 85, "weight": 10},             # Password security
    "vulnerability_prevention": {"target": 85, "weight": 5}      # OWASP compliance
}
```

#### **Architecture Review Requirements**
```
architecture_criteria = {
    "fullstack_design": {"target": 90, "weight": 25},           # Clean separation
    "rbac_architecture": {"target": 90, "weight": 25},          # Clean role design
    "api_architecture": {"target": 85, "weight": 20},           # RESTful API
    "database_design": {"target": 85, "weight": 15},            # User schema
    "scalability_design": {"target": 80, "weight": 15}          # Multi-user capability
}
```

#### **Performance Review Requirements**
```
performance_criteria = {
    "auth_response_time": {"target": 95, "weight": 30},         # Fast authentication
    "authz_response_time": {"target": 90, "weight": 25},        # Fast role checks
    "database_efficiency": {"target": 85, "weight": 20},        # Optimized queries
    "frontend_performance": {"target": 80, "weight": 15},       # Responsive UI
    "concurrent_sessions": {"target": 75, "weight": 10}         # Multi-user support
}
```

### **Performance Standards**
- **Authentication Response**: < 200ms for login/registration
- **Authorization Check**: < 50ms for role-based access control
- **Password Security**: Minimum 12 rounds bcrypt hashing
- **JWT Security**: Secure secret management and expiration
- **Database Performance**: Indexed queries with sub-50ms response times

---

**Remember**: This project is the foundation for building secure, production-ready applications. Master authentication and authorization here, and you'll have skills applicable to every future project!