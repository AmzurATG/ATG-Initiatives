# **Project 01 Implementation Guide: Basic LLM Chatbot**

## **🚀 AI-Assisted Development Prompts for MERN LLM Chatbot**

---

## **📋 Project Overview**

This guide provides specific prompts for building a Basic LLM Chatbot using the MERN stack with AI assistance from GitHub Copilot or other coding assistants.

**Goal:** Create a web chatbot where users can send prompts and receive responses from an LLM.

---

## **🏗️ Phase 1: Project Setup & Structure**

### **1.1 Initial Project Structure**

```
Create folder structure for MERN chatbot project:

client/
├── src/
│   ├── components/
│   │   ├── Chat/
│   │   ├── Common/
│   │   └── Auth/
│   ├── services/
│   ├── context/
│   ├── utils/
│   └── pages/
├── public/
└── package.json

server/
├── config/
├── controllers/
├── models/
├── routes/
├── middleware/
├── services/
└── server.js

Include proper .env files, .gitignore, and README.md
```

### **1.2 Package Dependencies**

```
Create package.json for Express.js server with dependencies for:
- Express.js 4.x for REST API
- Mongoose for MongoDB connection
- JWT for authentication
- OpenAI SDK for LLM integration
- CORS, Helmet, express-validator for security
- dotenv for environment variables
- nodemon for development

Include scripts for dev, start, and test
```

```
Create package.json for React client with:
- React 18+ with functional components
- Vite 5+ for build tooling
- Tailwind CSS for styling
- Axios for HTTP requests
- React Router for navigation
- React Context for state management

Include scripts for dev, build, and preview
```

---

## **🗄️ Phase 2: Backend Development**

### **2.1 Database Setup**

```
Create MongoDB connection setup using Mongoose that:
- Connects to MongoDB Atlas using connection string from .env
- Handles connection success and error events
- Implements proper error logging
- Sets up connection options for production
- Exports the connection function
```

```
Create Mongoose schema for User model with:
- username: String (required, unique, 3-30 characters)
- email: String (required, unique, valid email format)
- password: String (required, hashed with bcrypt)
- role: String (enum: 'user', 'admin', default: 'user')
- isActive: Boolean (default: true)
- createdAt, updatedAt: timestamps

Include validation, indexes, and pre-save password hashing
```

```
Create Mongoose schema for ChatSession model with:
- userId: ObjectId (required, references User)
- messages: Array of objects with { role: 'user'|'assistant', content: String, timestamp: Date }
- title: String (optional, for session naming)
- isActive: Boolean (default: true)
- createdAt, updatedAt: timestamps

Include proper indexing and population methods
```

### **2.2 Authentication System**

```
Create JWT authentication service that:
- Generates JWT tokens with user ID and role
- Validates JWT tokens and extracts user data
- Implements token refresh functionality
- Uses secure signing secret from environment variables
- Sets appropriate token expiration times (15m access, 7d refresh)
```

```
Create authentication middleware for Express that:
- Extracts JWT token from Authorization header (Bearer format)
- Validates token using JWT service
- Attaches user data to req.user
- Handles expired tokens with proper error messages
- Allows certain routes to be unprotected
```

```
Create Express route handlers for authentication:

POST /api/auth/register
- Validates input: username, email, password
- Checks for existing user
- Hashes password with bcrypt
- Creates new user
- Returns success message (no sensitive data)

POST /api/auth/login  
- Validates credentials
- Compares password hash
- Generates JWT tokens
- Returns tokens and basic user info

POST /api/auth/refresh
- Validates refresh token
- Generates new access token
- Returns new access token
```

### **2.3 LLM Integration Service**

```
Create OpenAI service module that:
- Initializes OpenAI client with API key from environment
- Exports function to send chat completion requests
- Handles API errors and rate limiting
- Implements retry logic for failed requests
- Manages conversation context and token limits
- Returns formatted responses
```

```
Create chat controller for Express with:

POST /api/chat/message
- Requires authentication
- Validates input message
- Retrieves user's chat session or creates new one
- Adds user message to session
- Calls OpenAI API with conversation context
- Saves AI response to session
- Returns AI response to client
- Handles all errors appropriately
```

### **2.4 Chat Session Management**

```
Create chat session controller with endpoints:

GET /api/chat/sessions
- Returns user's chat sessions (paginated)
- Includes session title and last message
- Only returns sessions belonging to authenticated user

GET /api/chat/sessions/:sessionId
- Returns full conversation history for session
- Validates user owns the session
- Returns messages in chronological order

POST /api/chat/sessions
- Creates new chat session
- Generates session title from first message
- Associates with authenticated user

DELETE /api/chat/sessions/:sessionId
- Soft deletes session (sets isActive: false)
- Validates user ownership
```

### **2.5 Server Setup & Middleware**

```
Create Express.js server with:
- CORS configuration for React client origin
- Helmet for security headers
- Express.json() for parsing JSON bodies
- Rate limiting middleware (100 requests per 15 minutes)
- Custom error handling middleware
- Route mounting for /api/auth and /api/chat
- 404 handler for unknown routes
- Server listening on PORT from environment
```

---

## **⚛️ Phase 3: Frontend Development**

### **3.1 Authentication Context & State**

```
Create React Authentication Context that:
- Manages user authentication state (user, isAuthenticated, isLoading)
- Provides functions: login, logout, register, refreshToken
- Handles token storage in localStorage
- Automatically refreshes tokens before expiry
- Provides authentication status to child components
- Redirects to login on authentication failures
```

```
Create React hook useAuth that:
- Consumes AuthContext
- Returns authentication state and functions
- Handles loading states
- Provides easy access to user data
- Can be used in any component
```

### **3.2 API Service Layer**

```
Create Axios API service for authentication:
- Base URL configuration from environment variables
- Request interceptor to add JWT token to headers
- Response interceptor to handle token refresh
- Functions for: register, login, logout, refreshToken
- Error handling for network issues and auth failures
- TypeScript interfaces for request/response types
```

```
Create Axios API service for chat functionality:
- Functions for: sendMessage, getChatSessions, getSessionHistory
- Handles file uploads for future enhancements
- Proper error handling and loading states
- Request/response formatting
- Cancel tokens for request cancellation
```

### **3.3 Chat Interface Components**

```
Create React component ChatWindow that:
- Displays conversation history in scrollable container
- Shows user and AI messages with different styling
- Auto-scrolls to latest message
- Handles loading state while waiting for AI response
- Uses Tailwind CSS for responsive design
- Props: messages array, isLoading boolean
```

```
Create React component ChatMessage that:
- Accepts props: message object {role, content, timestamp}
- Renders different styles for user vs assistant messages
- Shows timestamp in user-friendly format
- Handles long messages with proper line breaks
- Includes copy-to-clipboard functionality
- Uses Tailwind CSS for styling
```

```
Create React component ChatInput that:
- Controlled input field for user messages
- Send button with loading state
- Handles Enter key submission
- Prevents empty message submission
- Character limit indicator
- Disabled state during AI response
- Proper form validation
```

```
Create React component SessionSidebar that:
- Lists user's chat sessions
- Shows session titles and timestamps
- Highlights currently active session
- Provides new session button
- Handles session deletion
- Responsive design for mobile
```

### **3.4 Main Application Components**

```
Create React component ChatPage that:
- Combines ChatWindow, ChatInput, SessionSidebar
- Manages local chat state (current session, messages)
- Handles sending messages to API
- Updates UI immediately for user messages
- Shows typing indicator while waiting for AI
- Implements proper error handling
```

```
Create React component AuthPage that:
- Provides login and register forms
- Form validation using controlled inputs
- Shows loading states during authentication
- Displays error messages appropriately
- Redirects to chat page on successful auth
- Responsive design with Tailwind CSS
```

```
Create React App component that:
- Sets up routing with React Router
- Protects chat routes with authentication check
- Provides AuthContext to entire app
- Handles global loading and error states
- Includes navigation header
- Responsive layout structure
```

---

## **🔧 Phase 4: Error Handling & Validation**

### **4.1 Backend Validation**

```
Create Express validation middleware using express-validator for:

User registration:
- Username: 3-30 characters, alphanumeric
- Email: valid email format
- Password: minimum 8 characters, contains uppercase, lowercase, number

Chat message:
- Message content: 1-4000 characters, not empty
- Trim whitespace, sanitize HTML

Session operations:
- Session ID: valid MongoDB ObjectId
- Pagination: positive integers for page/limit
```

### **4.2 Frontend Validation**

```
Create React custom hooks for form validation:

useFormValidation hook that:
- Accepts validation rules object
- Returns {values, errors, handleChange, handleSubmit, isValid}
- Validates fields on change and blur
- Provides real-time error feedback
- Handles form submission with validation

useAuthForm hook specifically for:
- Registration form validation
- Login form validation
- Password strength indicator
- Email format validation
```

### **4.3 Error Handling**

```
Create React ErrorBoundary component that:
- Catches JavaScript errors in component tree
- Shows user-friendly error message
- Logs errors for debugging
- Provides fallback UI
- Allows error recovery

Create toast notification system that:
- Shows success/error/info messages
- Auto-dismisses after timeout
- Stacks multiple notifications
- Provides manual dismiss option
- Animated transitions
```

---

## **🚀 Phase 5: Optimization & Polish**

### **5.1 Performance Optimization**

```
Optimize React components with:
- React.memo for expensive components
- useCallback for event handlers
- useMemo for expensive calculations
- Lazy loading for code splitting
- Virtualization for large message lists
```

```
Optimize API calls with:
- Request debouncing for search
- Caching for session data
- Pagination for message history
- Request cancellation for outdated requests
- Loading states for better UX
```

### **5.2 UI/UX Enhancements**

```
Add UI enhancements:
- Loading skeletons for chat messages
- Typing indicators for AI responses
- Smooth animations with Tailwind transitions
- Dark/light mode toggle
- Responsive design for all screen sizes
- Accessibility features (ARIA labels, keyboard navigation)
```

### **5.3 Security Enhancements**

```
Implement security measures:
- Input sanitization on frontend and backend
- XSS prevention in message display
- CSRF protection for state-changing operations
- Rate limiting per user
- Secure HTTP headers
- Environment variable validation
```

---

## **🧪 Phase 6: Testing & Deployment**

### **6.1 Testing Prompts**

```
Create Jest tests for backend:
- Authentication middleware tests
- API endpoint tests
- Database model tests  
- Service function tests
- Error handling tests

Include setup/teardown, mocking, and assertions
```

```
Create React Testing Library tests for frontend:
- Component rendering tests
- User interaction tests
- Authentication flow tests
- API integration tests
- Error state tests

Include mocking of API calls and context providers
```

### **6.2 Deployment Preparation**

```
Create production configuration:
- Environment variables for all secrets
- Build scripts for client and server
- Docker configuration files
- MongoDB Atlas connection
- Deployment-ready package.json scripts
- Health check endpoints
```

---

## **📝 Success Checklist**

### **Backend Complete ✅**
- [ ] MongoDB connection established
- [ ] User authentication working
- [ ] JWT token management implemented
- [ ] OpenAI API integration functional
- [ ] Chat session CRUD operations
- [ ] Error handling and validation
- [ ] Security middleware applied

### **Frontend Complete ✅**
- [ ] User registration and login
- [ ] Protected route navigation
- [ ] Real-time chat interface
- [ ] Session management UI
- [ ] Error handling and notifications
- [ ] Responsive design
- [ ] Performance optimizations

### **Integration Complete ✅**
- [ ] Frontend-backend communication
- [ ] Authentication flow end-to-end
- [ ] Chat functionality working
- [ ] Error states handled
- [ ] Loading states implemented
- [ ] Security measures in place

---

## **💡 Troubleshooting Common Issues**

### **CORS Errors**
```
Configure CORS in Express server:
- Allow specific origin (React dev server)
- Include credentials for authentication
- Handle preflight requests
```

### **Authentication Issues**
```
Debug JWT authentication:
- Verify token format and signing
- Check token expiration
- Validate middleware order
- Test token refresh flow
```

### **OpenAI API Errors**
```
Handle OpenAI service issues:
- Implement exponential backoff
- Add proper error messages
- Handle rate limiting
- Validate API key configuration
```

---

**Next:** [Project 02 Implementation Guide](Project-02-Implementation-Guide.md)

