# Project 4: Complete Authentication & Authorization System - Key Concepts

> **Note**: This document provides the theoretical foundation for implementing Project 4. For step-by-step implementation guidance, refer to the [Technical Implementation Guide](/V2-Plan-1-Phase-1-Project-Implementations/Project-04-Technical-Implementation-Guide.md).

## Table of Contents
1. [Full-Stack Architecture with Backend-Frontend Separation](#1-full-stack-architecture-with-backend-frontend-separation)
2. [Database Design and ORM Integration with Role-Based Access](#2-database-design-and-orm-integration-with-role-based-access)
3. [JWT Authentication and Authorization with RBAC](#3-jwt-authentication-and-authorization-with-rbac)
4. [Password Security and Hashing](#4-password-security-and-hashing)
5. [React Authentication Context and Protected Routes](#5-react-authentication-context-and-protected-routes)
6. [Modern Frontend Development with React, Vite, and Tailwind](#6-modern-frontend-development-with-react-vite-and-tailwind)
7. [OAuth 2.0 and Social Authentication](#7-oauth-20-and-social-authentication)
8. [Role-Based Access Control (RBAC) Implementation](#8-role-based-access-control-rbac-implementation)

## 1. Full-Stack Architecture with Backend-Frontend Separation

### Concept Overview
Full-stack architecture with backend-frontend separation involves building applications with distinct backend (server) and frontend (client) components that communicate through well-defined APIs, enabling independent development, deployment, and scaling.

### Problem It Solves
Monolithic applications where frontend and backend are tightly coupled create challenges for development teams, make testing difficult, limit technology choices, and create scaling bottlenecks.

### Solution Approach
- **API-First Design**: Defining clear API contracts between frontend and backend
- **Separation of Concerns**: Backend focuses on data processing and business logic; frontend handles UI and user interaction
- **Independent Development**: Frontend and backend teams can work independently with minimal conflicts
- **Technology Flexibility**: Different technologies can be used for frontend and backend based on requirements

### Implementation Insight
```
# Enhanced Backend Structure (FastAPI)
backend/
├── app/
│   ├── __init__.py
│   ├── main.py                  # Main FastAPI application
│   ├── api/
│   │   ├── __init__.py
│   │   ├── dependencies.py      # Shared API dependencies
│   │   └── routes/
│   │       ├── __init__.py
│   │       ├── auth.py          # Authentication endpoints
│   │       ├── users.py         # User management endpoints
│   │       ├── admin.py         # Admin-only endpoints
│   │       └── oauth.py         # Social authentication endpoints
│   ├── core/
│   │   ├── __init__.py
│   │   ├── config.py            # Application configuration
│   │   ├── security.py          # Security utilities
│   │   └── rbac.py              # Role-based access control
│   ├── db/
│   │   ├── __init__.py
│   │   ├── base.py              # Base DB setup
│   │   └── session.py           # DB session management
│   ├── models/
│   │   ├── __init__.py
│   │   ├── user.py              # User DB model with roles
│   │   └── social_account.py    # Social account DB model
│   └── schemas/
│       ├── __init__.py
│       ├── token.py             # Token schemas
│       ├── user.py              # User data schemas
│       └── social.py            # Social auth schemas
├── migrations/                  # Alembic migrations
├── .env.example                 # Example environment variables
├── requirements.txt             # Python dependencies
└── alembic.ini                  # Migration configuration

# Enhanced Frontend Structure (React + Vite)
frontend/
├── public/
│   └── favicon.ico              # Public assets
├── src/
│   ├── App.tsx                  # Main application component
│   ├── main.tsx                 # Application entry point
│   ├── api/
│   │   ├── index.ts             # API client setup
│   │   ├── auth.ts              # Authentication API calls
│   │   └── oauth.ts             # Social authentication API
│   ├── components/
│   │   ├── common/              # Shared UI components
│   │   ├── auth/                # Authentication components
│   │   ├── admin/               # Admin-only components
│   │   └── layout/              # Layout components
│   ├── context/
│   │   └── AuthContext.tsx      # Authentication context with roles
│   ├── hooks/
│   │   ├── useAuth.ts           # Authentication hook
│   │   └── useRBAC.ts           # Role-based access hook
│   ├── pages/
│   │   ├── Home.tsx             # Public home page
│   │   ├── Login.tsx            # Login page with social options
│   │   ├── Register.tsx         # Registration page
│   │   ├── Dashboard.tsx        # Protected dashboard page
│   │   └── AdminPanel.tsx       # Admin-only page
│   ├── routes/
│   │   ├── index.tsx            # Routing configuration
│   │   ├── ProtectedRoute.tsx   # General protected routes
│   │   └── AdminRoute.tsx       # Admin-only routes
│   └── utils/
│       ├── token.ts             # Token utilities
│       └── roles.ts             # Role management utilities
├── .env.example                 # Environment variables
├── package.json                 # Frontend dependencies
├── tailwind.config.js           # Tailwind CSS configuration
└── vite.config.js               # Vite configuration
```

> **Implementation Guide**: See [Milestone 1: Backend Foundation & Database Setup](/V2-Plan-1-Phase-1-Project-Implementations/Project-04-Technical-Implementation-Guide.md#3-milestone-1-backend-foundation--database-setup) and [Milestone 2: Frontend Development](/V2-Plan-1-Phase-1-Project-Implementations/Project-04-Technical-Implementation-Guide.md#4-milestone-2-frontend-development) for practical implementation steps.

### Common Pitfalls
- **Inconsistent API Contracts**: Not maintaining consistent API structures and response formats
- **CORS Configuration Issues**: Incorrect Cross-Origin Resource Sharing settings blocking legitimate requests
- **Over-fetching**: Retrieving more data than needed for frontend operations
- **Under-fetching**: Making multiple API calls when data could be combined in one request
- **Tight Coupling**: Creating frontend components that depend on specific backend implementation details
- **Authentication Flow Fragmentation**: Inconsistent handling of auth tokens between frontend and backend

### Cross-References
- Builds upon [Project 1's REST API Development](./Project-1-Key-Concepts.md#3-rest-api-development-with-fastapi) with more comprehensive architecture
- Utilizes [Project 1's Environment-Based Security Management](./Project-1-Key-Concepts.md#2-environment-based-security-management) for configuration
- Integrates with [Database Design and ORM Integration](#2-database-design-and-orm-integration) for data persistence
- Forms foundation for [JWT Authentication and Authorization](#3-jwt-authentication-and-authorization)

### Architecture Diagram
```
┌──────────────────────────────────────────────────────────┐
│                        Client Side                       │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  ┌─────────────┐   ┌─────────────┐   ┌──────────────┐    │
│  │             │   │             │   │              │    │
│  │    Views    │◄──┤    State    │◄──┤  API Client  │    │
│  │             │   │             │   │              │    │
│  └─────────────┘   └─────────────┘   └──────┬───────┘    │
│                                             │            │
└─────────────────────────────────────────────┼────────────┘
                                              │
                                              │ HTTP/HTTPS
                                              │
┌─────────────────────────────────────────────┼───────────┐
│                                             │           │
│  ┌──────────────┐   ┌─────────────┐   ┌─────▼──────┐    │
│  │              │   │             │   │            │    │
│  │  Database    │◄──┤  Services   │◄──┤    API     │    │
│  │              │   │             │   │  Endpoints │    │
│  └──────────────┘   └─────────────┘   └────────────┘    │
│                                                         │
├─────────────────────────────────────────────────────────┤
│                       Server Side                       │
└─────────────────────────────────────────────────────────┘
```

## 2. Database Design and ORM Integration with Role-Based Access

### Concept Overview
Database design with ORM (Object-Relational Mapping) integration for authentication systems involves creating efficient database schemas for users, roles, sessions, and social accounts while using programming language objects to interact with the database rather than writing raw SQL.

### Problem It Solves
Raw SQL queries are error-prone, difficult to maintain, and create security vulnerabilities like SQL injection. Additionally, manually mapping between database rows and application objects is tedious and error-prone. Traditional authentication systems also lack proper role management.

### Solution Approach
- **Enhanced Schema Design**: Creating properly normalized database tables with role-based access patterns
- **ORM Models**: Defining Python classes that map to database tables with role relationships
- **Migration Management**: Using tools like Alembic to handle schema evolution including role additions
- **Type Safety**: Leveraging ORM features for type checking and validation
- **Social Account Integration**: Designing schemas that support OAuth provider linking

### Implementation Insight
```python
# Enhanced SQLAlchemy models with roles and social accounts
from sqlalchemy import Boolean, Column, Integer, String, DateTime, ForeignKey, UniqueConstraint
from sqlalchemy.sql import func
from sqlalchemy.orm import relationship
from app.db.base import Base
from passlib.context import CryptContext
from enum import Enum
import enum

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

class UserRole(str, enum.Enum):
    USER = "user"
    ADMIN = "admin"
    # Future roles can be added here

class User(Base):
    __tablename__ = "users"
    
    id = Column(Integer, primary_key=True, index=True)
    email = Column(String(100), unique=True, index=True, nullable=False)
    username = Column(String(50), unique=True, index=True, nullable=False)
    hashed_password = Column(String(255), nullable=False)
    first_name = Column(String(50))
    last_name = Column(String(50))
    role = Column(String(20), default=UserRole.USER.value, nullable=False)
    is_active = Column(Boolean, default=True)
    created_at = Column(DateTime(timezone=True), server_default=func.now())
    updated_at = Column(DateTime(timezone=True), onupdate=func.now())
    
    # Relationships
    sessions = relationship("UserSession", back_populates="user")
    social_accounts = relationship("SocialAccount", back_populates="user")
    
    @classmethod
    def create(cls, db, *, email: str, username: str, password: str, role: str = UserRole.USER.value):
        hashed_password = pwd_context.hash(password)
        user = cls(
            email=email,
            username=username,
            hashed_password=hashed_password,
            role=role
        )
        db.add(user)
        db.commit()
        db.refresh(user)
        return user
    
    def verify_password(self, plain_password: str) -> bool:
        return pwd_context.verify(plain_password, self.hashed_password)
    
    def has_role(self, role: UserRole) -> bool:
        return self.role == role.value
    
    def is_admin(self) -> bool:
        return self.role == UserRole.ADMIN.value

class UserSession(Base):
    __tablename__ = "user_sessions"
    
    id = Column(Integer, primary_key=True, index=True)
    user_id = Column(Integer, ForeignKey("users.id"), nullable=False)
    token_jti = Column(String(255), unique=True, nullable=False)
    expires_at = Column(DateTime(timezone=True), nullable=False)
    created_at = Column(DateTime(timezone=True), server_default=func.now())
    
    # Relationships
    user = relationship("User", back_populates="sessions")

class SocialAccount(Base):
    __tablename__ = "social_accounts"
    
    id = Column(Integer, primary_key=True, index=True)
    user_id = Column(Integer, ForeignKey("users.id"), nullable=False)
    provider = Column(String(20), nullable=False)  # 'google', 'facebook', 'linkedin'
    provider_user_id = Column(String(255), nullable=False)
    provider_email = Column(String(255))
    access_token = Column(String(1024))
    refresh_token = Column(String(1024))
    expires_at = Column(DateTime(timezone=True))
    created_at = Column(DateTime(timezone=True), server_default=func.now())
    updated_at = Column(DateTime(timezone=True), onupdate=func.now())
    
    # Relationships
    user = relationship("User", back_populates="social_accounts")
    
    __table_args__ = (
        UniqueConstraint('provider', 'provider_user_id', name='uq_provider_user'),
    )

# Enhanced Pydantic schemas for validation and serialization
from pydantic import BaseModel, EmailStr, Field, validator
from typing import Optional, List
import re

class UserBase(BaseModel):
    email: EmailStr
    username: str = Field(..., min_length=3, max_length=50)
    first_name: Optional[str] = Field(None, max_length=50)
    last_name: Optional[str] = Field(None, max_length=50)
    
    @validator("username")
    def username_alphanumeric(cls, v):
        if not re.match(r"^[a-zA-Z0-9_-]+$", v):
            raise ValueError("Username must be alphanumeric with dashes and underscores")
        return v

class UserCreate(UserBase):
    password: str = Field(..., min_length=8)
    
    @validator("password")
    def validate_password(cls, v):
        if not re.search(r"[A-Z]", v):
            raise ValueError("Password must contain at least one uppercase letter")
        if not re.search(r"[a-z]", v):
            raise ValueError("Password must contain at least one lowercase letter")
        if not re.search(r"\d", v):
            raise ValueError("Password must contain at least one digit")
        return v

class UserResponse(UserBase):
    id: int
    role: str
    is_active: bool
    created_at: datetime
    social_accounts: List[str] = []  # List of provider names
    
    class Config:
        orm_mode = True
```

> **Implementation Guide**: For practical implementation steps, see [Database Design & Models](/V2-Plan-1-Phase-1-Project-Implementations/Project-04-Technical-Implementation-Guide.md#32-database-design--models).

### Common Pitfalls
- **N+1 Query Problem**: Loading related objects inefficiently with multiple database queries
- **Role Assignment Logic**: Not properly handling default role assignment and role transitions
- **Social Account Conflicts**: Not handling cases where multiple social accounts link to same email
- **Session Management Issues**: Not properly handling database sessions (opening/closing)
- **Migration Conflicts**: Improper handling of schema migrations causing data loss or corruption
- **Index Optimization**: Missing indexes on role and provider columns affecting query performance

### Cross-References
- Works with [Full-Stack Architecture](#1-full-stack-architecture-with-backend-frontend-separation) for data persistence
- Supports [JWT Authentication and Authorization](#3-jwt-authentication-and-authorization-with-rbac) by providing user storage
- Builds on database concepts from [Project 3's Knowledge Base Design](./Project-3-Key-Concepts.md#2-knowledge-base-design-and-management)
- Enables [Password Security and Hashing](#4-password-security-and-hashing) through proper storage models

### Architecture Diagram
```
┌─────────────────────────────────────────────────────────────────┐
│                       Application Layer                         │
└──────────────────────────────┬──────────────────────────────────┘
                               │
                               │
┌──────────────────────────────▼──────────────────────────────────┐
│                           ORM Layer                             │
│                                                                 │
│  ┌──────────────┐       ┌──────────────┐      ┌──────────────┐  │
│  │              │       │              │      │              │  │
│  │  Models      │◄─────►│  Session     │◄────►│  Migrations  │  │
│  │              │       │  Management  │      │              │  │
│  └──────────────┘       └──────────────┘      └──────────────┘  │
│                                                                 │
└──────────────────────────────┬──────────────────────────────────┘
                               │
                               │
┌──────────────────────────────▼──────────────────────────────────┐
│                        Database Layer                           │
│                                                                 │
│   ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐ │
│   │             │  │             │  │  Constraints &          │ │
│   │  Tables     │  │  Indexes    │  │  Relationships          │ │
│   │             │  │             │  │                         │ │
│   └─────────────┘  └─────────────┘  └─────────────────────────┘ │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## 3. JWT Authentication and Authorization with RBAC

### Concept Overview
JWT (JSON Web Token) authentication with Role-Based Access Control provides a stateless mechanism for verifying user identity and managing role-based permissions across distributed systems without requiring server-side storage.

### Problem It Solves
Traditional session-based authentication requires server memory to store session data, creating scaling challenges and making distributed systems complex. Additionally, managing user permissions across different features requires a systematic approach to access control.

### Solution Approach
- **Enhanced Token Generation**: Creating signed JWT tokens with role claims upon successful authentication
- **Role-Based Token Validation**: Validating token signatures, expiration, and role-based permissions
- **Claims Management**: Including appropriate user data and roles in token claims
- **Token Refresh**: Implementing token refresh mechanisms for longer sessions with role preservation
- **Authorization Middleware**: Protecting routes based on token validation and specific role requirements

### Implementation Insight
```python
# Enhanced Backend JWT implementation with RBAC
from datetime import datetime, timedelta
from typing import Optional, List
from jose import JWTError, jwt
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel
from functools import wraps

# Configuration
SECRET_KEY = "your-secret-key-stored-in-env"  # In production, use env var
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30
REFRESH_TOKEN_EXPIRE_DAYS = 7

# Enhanced token schemas with roles
class Token(BaseModel):
    access_token: str
    refresh_token: str
    token_type: str
    user_role: str  # Include role information

class TokenPayload(BaseModel):
    sub: Optional[int] = None      # User ID
    role: Optional[str] = None     # User role
    exp: Optional[int] = None      # Expiration time
    jti: Optional[str] = None      # JWT ID for session tracking

# OAuth2 scheme for token extraction
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="auth/login")

def create_access_token(data: dict, expires_delta: Optional[timedelta] = None):
    """Create a new access token with role information"""
    to_encode = data.copy()
    if expires_delta:
        expire = datetime.utcnow() + expires_delta
    else:
        expire = datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    
    # Include unique JWT ID for session tracking
    jti = str(uuid.uuid4())
    to_encode.update({
        "exp": expire,
        "jti": jti,
        "iat": datetime.utcnow()
    })
    
    encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    return encoded_jwt, jti

def create_refresh_token(data: dict):
    """Create a new refresh token with longer expiry and role preservation"""
    to_encode = data.copy()
    expire = datetime.utcnow() + timedelta(days=REFRESH_TOKEN_EXPIRE_DAYS)
    
    jti = str(uuid.uuid4())
    to_encode.update({
        "exp": expire,
        "jti": jti,
        "iat": datetime.utcnow(),
        "token_type": "refresh"
    })
    
    encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    return encoded_jwt, jti

async def get_current_user(token: str = Depends(oauth2_scheme), db = Depends(get_db)):
    """Dependency to get the current authenticated user from a token"""
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )
    
    try:
        # Decode the JWT
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        user_id: int = payload.get("sub")
        user_role: str = payload.get("role")
        token_jti: str = payload.get("jti")
        
        if user_id is None:
            raise credentials_exception
        
        # Create token payload model
        token_data = TokenPayload(
            sub=user_id, 
            role=user_role, 
            exp=payload.get("exp"),
            jti=token_jti
        )
    except JWTError:
        raise credentials_exception
    
    # Get user from database
    user = db.query(User).filter(User.id == token_data.sub).first()
    if user is None:
        raise credentials_exception
    
    # Check if token is expired
    if datetime.fromtimestamp(token_data.exp) < datetime.utcnow():
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Token expired",
            headers={"WWW-Authenticate": "Bearer"},
        )
    
    # Verify that the role in token matches current user role
    if user.role != token_data.role:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="User role has changed, please login again",
            headers={"WWW-Authenticate": "Bearer"},
        )
    
    return user

# Role-based authorization dependencies
async def get_current_admin_user(current_user: User = Depends(get_current_user)):
    """Dependency that requires admin role"""
    if not current_user.is_admin():
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Admin access required"
        )
    return current_user

def require_roles(allowed_roles: List[UserRole]):
    """Decorator factory for role-based endpoint protection"""
    def decorator(func):
        @wraps(func)
        async def wrapper(*args, current_user: User = Depends(get_current_user), **kwargs):
            if not any(current_user.has_role(role) for role in allowed_roles):
                raise HTTPException(
                    status_code=status.HTTP_403_FORBIDDEN,
                    detail=f"Access denied. Required roles: {[role.value for role in allowed_roles]}"
                )
            return await func(*args, current_user=current_user, **kwargs)
        return wrapper
    return decorator

# Enhanced login endpoint with role information
@router.post("/login", response_model=Token)
async def login(form_data: OAuth2PasswordRequestForm = Depends(), db = Depends(get_db)):
    """Authenticate user and return tokens with role information"""
    # Find user by username
    user = db.query(User).filter(User.username == form_data.username).first()
    if not user or not user.verify_password(form_data.password):
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Incorrect username or password",
            headers={"WWW-Authenticate": "Bearer"},
        )
    
    # Check if user is active
    if not user.is_active:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="User account is inactive"
        )
    
    # Create access and refresh tokens with role information
    access_token, access_jti = create_access_token(
        data={"sub": user.id, "role": user.role}
    )
    refresh_token, refresh_jti = create_refresh_token(
        data={"sub": user.id, "role": user.role}
    )
    
    # Store session information
    session = UserSession(
        user_id=user.id,
        token_jti=access_jti,
        expires_at=datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    )
    db.add(session)
    db.commit()
    
    return {
        "access_token": access_token,
        "refresh_token": refresh_token,
        "token_type": "bearer",
        "user_role": user.role
    }

# Example of role-protected endpoint
@router.get("/admin/users", dependencies=[Depends(get_current_admin_user)])
async def get_all_users(db = Depends(get_db)):
    """Admin-only endpoint to list all users"""
    users = db.query(User).all()
    return users

# Alternative using decorator approach
@router.delete("/admin/users/{user_id}")
@require_roles([UserRole.ADMIN])
async def delete_user(user_id: int, db = Depends(get_db), current_user: User = None):
    """Admin-only endpoint to delete users"""
    user_to_delete = db.query(User).filter(User.id == user_id).first()
    if not user_to_delete:
        raise HTTPException(status_code=404, detail="User not found")
    
    # Prevent admin from deleting themselves
    if user_to_delete.id == current_user.id:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="Cannot delete your own account"
        )
    
    db.delete(user_to_delete)
    db.commit()
    return {"message": "User deleted successfully"}
```

> **Implementation Guide**: For practical JWT implementation with RBAC, refer to [JWT Authentication System](/V2-Plan-1-Phase-1-Project-Implementations/Project-04-Technical-Implementation-Guide.md#34-jwt-authentication-system) in the Technical Implementation Guide.

### Common Pitfalls
- **Role Information Staleness**: Not handling cases where user roles change after token issuance
- **Insufficient Role Validation**: Not properly validating role claims in JWT tokens
- **Token Refresh Role Conflicts**: Not preserving role information during token refresh
- **Authorization Logic Scattered**: Implementing role checks inconsistently across endpoints
- **Missing Audit Trails**: Not logging role-based access attempts and decisions
- **Overly Broad Permissions**: Not following principle of least privilege in role assignments

## 8. Role-Based Access Control (RBAC) Implementation

### Concept Overview
Role-Based Access Control (RBAC) is a security model that restricts system access based on the roles assigned to individual users within an organization. In the context of authentication systems, it provides a systematic way to manage permissions and ensure users can only access resources appropriate to their role.

### Problem It Solves
Without RBAC, applications either provide the same access to all users or implement ad-hoc permission systems that are difficult to maintain and audit. This creates security risks, administrative overhead, and poor user experience.

### Solution Approach
- **Role Definition**: Creating clear role hierarchies and permissions
- **User-Role Assignment**: Associating users with appropriate roles
- **Permission Enforcement**: Checking roles before allowing access to resources
- **Dynamic Authorization**: Real-time role validation without requiring re-authentication
- **Audit and Compliance**: Tracking role assignments and access attempts

### Implementation Insight
```python
# Enhanced RBAC implementation
from enum import Enum
from typing import List, Dict, Optional
from functools import wraps
from fastapi import HTTPException, status

class UserRole(str, Enum):
    """Define system roles with clear hierarchy"""
    USER = "user"           # Basic user access
    MODERATOR = "moderator" # Content moderation capabilities
    ADMIN = "admin"         # Full system access
    SUPER_ADMIN = "super_admin"  # System administration

class Permission(str, Enum):
    """Define granular permissions"""
    # User management
    READ_USERS = "read_users"
    CREATE_USERS = "create_users"
    UPDATE_USERS = "update_users"
    DELETE_USERS = "delete_users"
    
    # Content management
    READ_CONTENT = "read_content"
    CREATE_CONTENT = "create_content"
    UPDATE_CONTENT = "update_content"
    DELETE_CONTENT = "delete_content"
    MODERATE_CONTENT = "moderate_content"
    
    # System administration
    MANAGE_SYSTEM = "manage_system"
    VIEW_ANALYTICS = "view_analytics"
    MANAGE_ROLES = "manage_roles"

# Role-Permission mapping
ROLE_PERMISSIONS: Dict[UserRole, List[Permission]] = {
    UserRole.USER: [
        Permission.READ_CONTENT,
        Permission.CREATE_CONTENT,
        Permission.UPDATE_CONTENT,  # Own content only
    ],
    UserRole.MODERATOR: [
        Permission.READ_CONTENT,
        Permission.CREATE_CONTENT,
        Permission.UPDATE_CONTENT,
        Permission.DELETE_CONTENT,  # Any content
        Permission.MODERATE_CONTENT,
        Permission.READ_USERS,
    ],
    UserRole.ADMIN: [
        Permission.READ_USERS,
        Permission.CREATE_USERS,
        Permission.UPDATE_USERS,
        Permission.DELETE_USERS,
        Permission.READ_CONTENT,
        Permission.CREATE_CONTENT,
        Permission.UPDATE_CONTENT,
        Permission.DELETE_CONTENT,
        Permission.MODERATE_CONTENT,
        Permission.VIEW_ANALYTICS,
    ],
    UserRole.SUPER_ADMIN: [
        # Super admin has all permissions
        *[permission for permission in Permission]
    ]
}

class RBACManager:
    """Centralized role-based access control manager"""
    
    @staticmethod
    def has_permission(user_role: UserRole, required_permission: Permission) -> bool:
        """Check if a role has a specific permission"""
        role_permissions = ROLE_PERMISSIONS.get(user_role, [])
        return required_permission in role_permissions
    
    @staticmethod
    def has_any_permission(user_role: UserRole, required_permissions: List[Permission]) -> bool:
        """Check if a role has any of the specified permissions"""
        return any(
            RBACManager.has_permission(user_role, permission)
            for permission in required_permissions
        )
    
    @staticmethod
    def has_all_permissions(user_role: UserRole, required_permissions: List[Permission]) -> bool:
        """Check if a role has all of the specified permissions"""
        return all(
            RBACManager.has_permission(user_role, permission)
            for permission in required_permissions
        )
    
    @staticmethod
    def can_access_user_data(current_user_role: UserRole, target_user_id: int, current_user_id: int) -> bool:
        """Check if user can access another user's data"""
        # Users can always access their own data
        if current_user_id == target_user_id:
            return True
        
        # Admin and moderator can access other users' data
        return current_user_role in [UserRole.ADMIN, UserRole.MODERATOR, UserRole.SUPER_ADMIN]

# Permission-based decorators
def require_permission(required_permission: Permission):
    """Decorator to protect endpoints with specific permission requirements"""
    def decorator(func):
        @wraps(func)
        async def wrapper(*args, current_user: User = Depends(get_current_user), **kwargs):
            user_role = UserRole(current_user.role)
            
            if not RBACManager.has_permission(user_role, required_permission):
                raise HTTPException(
                    status_code=status.HTTP_403_FORBIDDEN,
                    detail=f"Access denied. Required permission: {required_permission.value}"
                )
            
            return await func(*args, current_user=current_user, **kwargs)
        return wrapper
    return decorator

def require_any_permission(required_permissions: List[Permission]):
    """Decorator to protect endpoints requiring any of the specified permissions"""
    def decorator(func):
        @wraps(func)
        async def wrapper(*args, current_user: User = Depends(get_current_user), **kwargs):
            user_role = UserRole(current_user.role)
            
            if not RBACManager.has_any_permission(user_role, required_permissions):
                permission_names = [p.value for p in required_permissions]
                raise HTTPException(
                    status_code=status.HTTP_403_FORBIDDEN,
                    detail=f"Access denied. Required permissions (any): {permission_names}"
                )
            
            return await func(*args, current_user=current_user, **kwargs)
        return wrapper
    return decorator

# Enhanced User model with RBAC methods
class User(Base):
    # ... existing fields ...
    
    def get_role(self) -> UserRole:
        """Get user role as enum"""
        return UserRole(self.role)
    
    def has_permission(self, permission: Permission) -> bool:
        """Check if user has specific permission"""
        return RBACManager.has_permission(self.get_role(), permission)
    
    def can_access_user(self, target_user_id: int) -> bool:
        """Check if user can access another user's data"""
        return RBACManager.can_access_user_data(
            self.get_role(), target_user_id, self.id
        )
    
    def can_modify_role(self, target_role: UserRole) -> bool:
        """Check if user can assign/modify a specific role"""
        current_role = self.get_role()
        
        # Only admins and super admins can modify roles
        if current_role not in [UserRole.ADMIN, UserRole.SUPER_ADMIN]:
            return False
        
        # Super admin can modify any role
        if current_role == UserRole.SUPER_ADMIN:
            return True
        
        # Admin cannot create super admin
        if target_role == UserRole.SUPER_ADMIN:
            return False
        
        return True

# Example usage in API endpoints
@router.get("/users/{user_id}")
@require_permission(Permission.READ_USERS)
async def get_user(
    user_id: int, 
    current_user: User = Depends(get_current_user),
    db = Depends(get_db)
):
    """Get user information (requires READ_USERS permission)"""
    # Additional check for accessing other users' data
    if not current_user.can_access_user(user_id):
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Cannot access other user's data"
        )
    
    user = db.query(User).filter(User.id == user_id).first()
    if not user:
        raise HTTPException(status_code=404, detail="User not found")
    
    return user

@router.put("/users/{user_id}/role")
@require_permission(Permission.MANAGE_ROLES)
async def update_user_role(
    user_id: int,
    new_role: UserRole,
    current_user: User = Depends(get_current_user),
    db = Depends(get_db)
):
    """Update user role (requires MANAGE_ROLES permission)"""
    # Check if current user can assign this role
    if not current_user.can_modify_role(new_role):
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail=f"Cannot assign role: {new_role.value}"
        )
    
    user = db.query(User).filter(User.id == user_id).first()
    if not user:
        raise HTTPException(status_code=404, detail="User not found")
    
    # Prevent users from modifying their own role
    if user.id == current_user.id:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="Cannot modify your own role"
        )
    
    old_role = user.role
    user.role = new_role.value
    db.commit()
    
    # Log role change for audit
    logger.info(f"Role changed: User {user_id} from {old_role} to {new_role.value} by {current_user.id}")
    
    return {"message": f"User role updated to {new_role.value}"}

@router.get("/content/{content_id}")
async def get_content(
    content_id: int,
    current_user: User = Depends(get_current_user),
    db = Depends(get_db)
):
    """Get content with role-based access control"""
    content = db.query(Content).filter(Content.id == content_id).first()
    if not content:
        raise HTTPException(status_code=404, detail="Content not found")
    
    # Check permissions based on content ownership and user role
    user_role = current_user.get_role()
    
    # Content owner can always access
    if content.owner_id == current_user.id:
        return content
    
    # Check role-based permissions for accessing others' content
    if content.is_private and not current_user.has_permission(Permission.MODERATE_CONTENT):
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Cannot access private content"
        )
    
    return content
```

### Frontend RBAC Integration
```tsx
// React RBAC utilities and components
import React from 'react';
import { useAuth } from '../hooks/useAuth';

// RBAC permissions enum (should match backend)
export enum Permission {
  READ_USERS = 'read_users',
  CREATE_USERS = 'create_users',
  UPDATE_USERS = 'update_users',
  DELETE_USERS = 'delete_users',
  READ_CONTENT = 'read_content',
  CREATE_CONTENT = 'create_content',
  UPDATE_CONTENT = 'update_content',
  DELETE_CONTENT = 'delete_content',
  MODERATE_CONTENT = 'moderate_content',
  MANAGE_SYSTEM = 'manage_system',
  VIEW_ANALYTICS = 'view_analytics',
  MANAGE_ROLES = 'manage_roles',
}

export enum UserRole {
  USER = 'user',
  MODERATOR = 'moderator',
  ADMIN = 'admin',
  SUPER_ADMIN = 'super_admin',
}

// Role-Permission mapping (should match backend)
const ROLE_PERMISSIONS: Record<UserRole, Permission[]> = {
  [UserRole.USER]: [
    Permission.READ_CONTENT,
    Permission.CREATE_CONTENT,
    Permission.UPDATE_CONTENT,
  ],
  [UserRole.MODERATOR]: [
    Permission.READ_CONTENT,
    Permission.CREATE_CONTENT,
    Permission.UPDATE_CONTENT,
    Permission.DELETE_CONTENT,
    Permission.MODERATE_CONTENT,
    Permission.READ_USERS,
  ],
  [UserRole.ADMIN]: [
    Permission.READ_USERS,
    Permission.CREATE_USERS,
    Permission.UPDATE_USERS,
    Permission.DELETE_USERS,
    Permission.READ_CONTENT,
    Permission.CREATE_CONTENT,
    Permission.UPDATE_CONTENT,
    Permission.DELETE_CONTENT,
    Permission.MODERATE_CONTENT,
    Permission.VIEW_ANALYTICS,
  ],
  [UserRole.SUPER_ADMIN]: Object.values(Permission),
};

// RBAC utility functions
export class RBACUtils {
  static hasPermission(userRole: UserRole, requiredPermission: Permission): boolean {
    const rolePermissions = ROLE_PERMISSIONS[userRole] || [];
    return rolePermissions.includes(requiredPermission);
  }
  
  static hasAnyPermission(userRole: UserRole, requiredPermissions: Permission[]): boolean {
    return requiredPermissions.some(permission => 
      this.hasPermission(userRole, permission)
    );
  }
  
  static hasAllPermissions(userRole: UserRole, requiredPermissions: Permission[]): boolean {
    return requiredPermissions.every(permission => 
      this.hasPermission(userRole, permission)
    );
  }
}

// Custom hook for RBAC
export const useRBAC = () => {
  const { user } = useAuth();
  
  const hasPermission = (permission: Permission): boolean => {
    if (!user) return false;
    return RBACUtils.hasPermission(user.role as UserRole, permission);
  };
  
  const hasAnyPermission = (permissions: Permission[]): boolean => {
    if (!user) return false;
    return RBACUtils.hasAnyPermission(user.role as UserRole, permissions);
  };
  
  const hasAllPermissions = (permissions: Permission[]): boolean => {
    if (!user) return false;
    return RBACUtils.hasAllPermissions(user.role as UserRole, permissions);
  };
  
  const hasRole = (role: UserRole): boolean => {
    if (!user) return false;
    return user.role === role;
  };
  
  const canAccessUser = (targetUserId: number): boolean => {
    if (!user) return false;
    // Users can access their own data
    if (user.id === targetUserId) return true;
    // Admin and moderator can access other users' data
    return [UserRole.ADMIN, UserRole.MODERATOR, UserRole.SUPER_ADMIN].includes(user.role as UserRole);
  };
  
  return {
    hasPermission,
    hasAnyPermission,
    hasAllPermissions,
    hasRole,
    canAccessUser,
    currentRole: user?.role as UserRole | null,
  };
};

// Permission-based component wrapper
interface PermissionGuardProps {
  permission?: Permission;
  permissions?: Permission[];
  requireAll?: boolean;
  role?: UserRole;
  roles?: UserRole[];
  fallback?: React.ReactNode;
  children: React.ReactNode;
}

export const PermissionGuard: React.FC<PermissionGuardProps> = ({
  permission,
  permissions = [],
  requireAll = false,
  role,
  roles = [],
  fallback = null,
  children,
}) => {
  const { hasPermission, hasAnyPermission, hasAllPermissions, hasRole } = useRBAC();
  
  let hasAccess = true;
  
  // Check single permission
  if (permission) {
    hasAccess = hasAccess && hasPermission(permission);
  }
  
  // Check multiple permissions
  if (permissions.length > 0) {
    if (requireAll) {
      hasAccess = hasAccess && hasAllPermissions(permissions);
    } else {
      hasAccess = hasAccess && hasAnyPermission(permissions);
    }
  }
  
  // Check single role
  if (role) {
    hasAccess = hasAccess && hasRole(role);
  }
  
  // Check multiple roles
  if (roles.length > 0) {
    hasAccess = hasAccess && roles.some(r => hasRole(r));
  }
  
  if (!hasAccess) {
    return <>{fallback}</>;
  }
  
  return <>{children}</>;
};

// Usage examples
export const AdminPanel: React.FC = () => {
  return (
    <PermissionGuard permission={Permission.VIEW_ANALYTICS}>
      <div className="admin-panel">
        <h2>Admin Dashboard</h2>
        
        <PermissionGuard permission={Permission.MANAGE_USERS}>
          <UserManagement />
        </PermissionGuard>
        
        <PermissionGuard 
          permissions={[Permission.MODERATE_CONTENT, Permission.DELETE_CONTENT]}
          fallback={<div>Content moderation not available</div>}
        >
          <ContentModerationPanel />
        </PermissionGuard>
      </div>
    </PermissionGuard>
  );
};
```

### Common Pitfalls
- **Role Proliferation**: Creating too many specific roles instead of using permission-based systems
- **Hardcoded Permissions**: Not making role-permission mappings configurable
- **Client-Side Only Validation**: Relying solely on frontend role checks without backend enforcement
- **Inconsistent Role Hierarchy**: Not maintaining clear role hierarchies and inheritance patterns
- **Missing Audit Trails**: Not logging role changes and access decisions for compliance
- **Static Role Assignment**: Not providing mechanisms for dynamic role updates

### Cross-References
- Builds upon [JWT Authentication and Authorization](#3-jwt-authentication-and-authorization-with-rbac) for role-based token claims
- Integrates with [Database Design](#2-database-design-and-orm-integration-with-role-based-access) for role storage and management
- Works with [React Authentication Context](#5-react-authentication-context-and-protected-routes) for frontend role management
- Enhances [Full-Stack Architecture](#1-full-stack-architecture-with-backend-frontend-separation) with proper access control
    </div>
  );
};
```

### Common Pitfalls
- **Insufficient State Validation**: Not properly validating the state parameter, risking CSRF attacks
- **Scope Overreach**: Requesting excessive permissions from providers
- **User Data Inconsistency**: Not handling profile data differences between providers
- **Token Storage Security**: Insecure storage of OAuth access/refresh tokens
- **Account Linking Conflicts**: Not properly handling cases where users have multiple social accounts
- **Redirect URI Mismatches**: Misconfigured redirect URIs between provider settings and application
- **Session Fixation**: Not regenerating session IDs during authentication flows

### Cross-References
- Builds upon [JWT Authentication and Authorization](#3-jwt-authentication-and-authorization) for token management
- Integrates with [Database Design and ORM Integration](#2-database-design-and-orm-integration) for user storage
- Works with [React Authentication Context](#5-react-authentication-context-and-protected-routes) for state management
- Enhances [Password Security](#4-password-security-and-hashing) by providing alternative authentication methods

### Architecture Diagram
```
┌──────────────────┐     ┌────────────────┐     ┌────────────────┐
│                  │     │                │     │                │
│  User Interface  │────▶│  OAuth Client  │────▶│ OAuth Provider │
│                  │     │                │     │                │
└────────┬─────────┘     └────────┬───────┘     └────────┬───────┘
         │                        │                      │
         │                        │                      │
         ▼                        ▼                      ▼
┌────────────────┐     ┌────────────────┐     ┌────────────────┐
│                │     │                │     │                │
│ Auth Context   │◀───▶│ Backend API    │◀───▶│ Provider API   │
│                │     │                │     │                │
└────────┬───────┘     └────────┬───────┘     └────────────────┘
         │                      │
         │                      │
         ▼                      ▼
┌────────────────┐     ┌────────────────┐
│                │     │                │
│ Protected UI   │     │ User Database  │
│                │     │                │
└────────────────┘     └────────────────┘
```

### Further Resources
- [OAuth 2.0 Specification](https://oauth.net/2/)
- [OpenID Connect](https://openid.net/connect/)
- [OAuth 2.0 PKCE Flow](https://oauth.net/2/pkce/)
- [Authlib Documentation](https://docs.authlib.org/)
- [Google OAuth Documentation](https://developers.google.com/identity/protocols/oauth2)
- [Facebook Login Documentation](https://developers.facebook.com/docs/facebook-login/)
- [LinkedIn OAuth Documentation](https://docs.microsoft.com/en-us/linkedin/shared/authentication/authorization-code-flow)
