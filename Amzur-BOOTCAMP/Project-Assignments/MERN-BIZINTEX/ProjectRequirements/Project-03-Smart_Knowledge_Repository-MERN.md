# **Project 3: Smart Knowledge Repository (MERN Stack)**

## **Objective (Why?)**

Build an intelligent knowledge base that scrapes specific website sections, stores information in a database, and answers questions within scope in just 2 days. This accelerated timeline builds efficiently on your Projects 1-2 foundation. You will practice:

* **Structured Data Extraction**: Scraping and organizing specific profile information
* **Database Design & Management**: Building a MongoDB knowledge database with proper schemas
* **Context-Aware AI**: Creating scope-limited AI responses with domain detection
* **Intelligent Querying**: Matching user questions to relevant data
* **Full CRUD Operations**: Complete data management functionality
* **Advanced Search**: Implementing text search and relevance scoring

## **Core Requirements (Must-have for Initial 2-Day Project)**

| Layer | Requirement |
| :---- | ----- |
| **Data Collection** | **Targeted Web Scraping**: Scrape a *pre-defined* leadership team page (e.g., buffer.com/about) <br> Extract structured data: name, role/title, bio, photo URL, contact info, department <br> Validate and normalize extracted data <br> Store data in MongoDB with proper schema design <br> Handle multiple profiles automatically |
| **Knowledge Base** | **MongoDB Storage**: Design proper schema with indexes for efficient querying <br> **Text Search**: Implement MongoDB text search or custom keyword matching <br> **Categorization**: Organize profiles by role, department, or team <br> **Data Integrity**: Ensure consistent data storage and validation <br> **Query Optimization**: Fast retrieval with proper indexing |
| **Chat Interface** | **React Application**: Build chat interface with message history <br> **Scope Detection**: Identify in-domain vs out-of-domain questions <br> **Context-Aware Responses**: Use stored knowledge for accurate answers <br> **Profile Browser**: Display and filter leadership profiles <br> **Multi-View Interface**: Chat, Browse Profiles, Search views |
| **Scope Management** | **Rule-Based Detection**: Check if questions relate to leadership/team topics <br> **Keyword Matching**: Identify relevant queries (who, CEO, team, leadership) <br> **Helpful Fallbacks**: Provide suggestions for out-of-scope questions <br> **Intent Recognition**: Understand different question patterns |

## **Technology Stack (MERN)**

### **Backend Stack**
- **Runtime**: Node.js 18+ (LTS)
- **Framework**: Express.js 4.x
- **Database**: MongoDB 6+ with Mongoose ODM
- **Web Scraping**: Cheerio for HTML parsing, Axios for HTTP requests
- **LLM Integration**: OpenAI SDK or Google Generative AI SDK
- **Validation**: express-validator for input validation
- **Security**: Helmet.js for security headers
- **Environment Variables**: dotenv

### **Frontend Stack**
- **Library**: React 18+
- **Build Tool**: Vite 5+
- **Styling**: Tailwind CSS 3+
- **HTTP Client**: Axios
- **State Management**: React Hooks (useState, useEffect, useContext)
- **Routing**: React Router (for multi-view navigation)
- **UI Components**: Custom components for chat and profile display

### **Database**
- **MongoDB**: Document-based NoSQL database
- **Mongoose**: ODM for schema definition and validation
- **Indexing**: Text indexes for search, compound indexes for queries
- **Aggregation**: For analytics and statistics

### **Optional Enhancements**
- **Vector Search**: MongoDB Atlas Vector Search for semantic similarity
- **Caching**: Redis for frequently accessed profiles
- **File Upload**: Multer for profile image uploads
- **Authentication**: JWT for user management (stretch goal)

## **Project Structure**

```
mern-knowledge-repo/
├── client/                               # React frontend
│   ├── public/
│   │   └── vite.svg
│   ├── src/
│   │   ├── components/
│   │   │   ├── Chat/
│   │   │   │   ├── ChatWindow.jsx       # Main chat interface
│   │   │   │   ├── ChatMessage.jsx      # Individual messages
│   │   │   │   ├── ChatInput.jsx        # Input component
│   │   │   │   └── ScopeIndicator.jsx   # Shows if question is in-scope
│   │   │   ├── Profiles/
│   │   │   │   ├── ProfileBrowser.jsx   # Grid/list view of profiles
│   │   │   │   ├── ProfileCard.jsx      # Individual profile display
│   │   │   │   ├── ProfileDetail.jsx    # Detailed profile view
│   │   │   │   └── ProfileFilter.jsx    # Filter by role/department
│   │   │   ├── Search/
│   │   │   │   ├── SearchBar.jsx        # Search input
│   │   │   │   ├── SearchResults.jsx    # Results display
│   │   │   │   └── Filters.jsx          # Search filters
│   │   │   ├── Admin/                   # CRUD operations (stretch)
│   │   │   │   ├── ProfileForm.jsx      # Add/edit profiles
│   │   │   │   └── ProfileManager.jsx   # Manage all profiles
│   │   │   ├── Layout/
│   │   │   │   ├── Navbar.jsx           # Navigation
│   │   │   │   ├── Sidebar.jsx          # Side navigation
│   │   │   │   └── Footer.jsx           # Footer
│   │   │   └── Common/
│   │   │       ├── LoadingSpinner.jsx
│   │   │       ├── ErrorDisplay.jsx
│   │   │       └── EmptyState.jsx
│   │   ├── services/
│   │   │   ├── chatService.js           # Chat API calls
│   │   │   ├── profileService.js        # Profile CRUD operations
│   │   │   └── searchService.js         # Search functionality
│   │   ├── context/
│   │   │   └── AppContext.jsx           # Global state management
│   │   ├── utils/
│   │   │   ├── scopeDetector.js         # Client-side scope detection
│   │   │   └── formatters.js            # Data formatting
│   │   ├── pages/
│   │   │   ├── ChatPage.jsx             # Chat view
│   │   │   ├── BrowsePage.jsx           # Browse profiles
│   │   │   ├── SearchPage.jsx           # Search view
│   │   │   └── AdminPage.jsx            # Admin panel (stretch)
│   │   ├── App.jsx
│   │   ├── App.css
│   │   ├── index.css
│   │   └── main.jsx
│   ├── .env.example
│   ├── .gitignore
│   ├── index.html
│   ├── package.json
│   ├── tailwind.config.js
│   ├── postcss.config.js
│   └── vite.config.js
│
├── server/                               # Express backend
│   ├── config/
│   │   ├── env.js                       # Environment configuration
│   │   └── database.js                  # MongoDB connection
│   ├── models/
│   │   └── Profile.js                   # Mongoose schema
│   ├── controllers/
│   │   ├── chatController.js            # Chat and query handling
│   │   ├── profileController.js         # Profile CRUD operations
│   │   ├── searchController.js          # Search functionality
│   │   └── scraperController.js         # Scraping trigger
│   ├── routes/
│   │   ├── chatRoutes.js                # Chat endpoints
│   │   ├── profileRoutes.js             # Profile CRUD endpoints
│   │   ├── searchRoutes.js              # Search endpoints
│   │   └── scraperRoutes.js             # Scraping endpoints
│   ├── services/
│   │   ├── scraperService.js            # Web scraping logic
│   │   ├── profileService.js            # Profile business logic
│   │   ├── searchService.js             # Search implementation
│   │   ├── scopeDetector.js             # Scope detection logic
│   │   ├── llmService.js                # LLM integration
│   │   └── knowledgeService.js          # Knowledge retrieval
│   ├── middleware/
│   │   ├── errorHandler.js              # Error handling
│   │   ├── corsConfig.js                # CORS configuration
│   │   ├── validator.js                 # Request validation
│   │   └── securityHeaders.js           # Helmet setup
│   ├── utils/
│   │   ├── dataExtractor.js             # Extract data from HTML
│   │   ├── dataCleaner.js               # Clean and normalize data
│   │   ├── queryMatcher.js              # Match queries to profiles
│   │   └── relevanceScorer.js           # Score search relevance
│   ├── scripts/
│   │   └── seedDatabase.js              # Initial data seeding
│   ├── .env.example
│   ├── .gitignore
│   ├── package.json
│   └── server.js                         # Entry point
│
├── .gitignore                            # Root gitignore
├── README.md                             # Project documentation
└── package.json                          # Optional: root package.json
```

## **Development Approach: Milestone-Based Progression**

**Philosophy**: Focus on **deliverable quality** and **comprehensive review compliance** rather than rigid timelines. Each milestone must pass all relevant review templates from our Templates folder before proceeding.

### **Milestone 1: Data Collection & Knowledge Storage**
**Estimated Time**: 4-6 hours (flexible based on learning pace)

#### **Deliverables:**
- [ ] MongoDB database setup with proper connection
- [ ] Mongoose schema design for leadership profiles
- [ ] Structured web scraping for leadership profile data
- [ ] Data validation and normalization pipeline
- [ ] Database seeding with scraped data
- [ ] Basic profile retrieval API endpoints
- [ ] Profile categorization by role/department
- [ ] Data integrity checks and error handling

#### **Review Requirements (Must Pass to Proceed):**
- [ ] **Security Review**: Safe data scraping, input validation, database security
- [ ] **Code Quality Review**: Clean data modeling and storage architecture
- [ ] **Performance Review**: Efficient data extraction, storage, and retrieval

### **Milestone 2: Intelligent Query & Scope Management**
**Estimated Time**: 4-6 hours (flexible based on Milestone 1 completion)

#### **Deliverables:**
- [ ] Scope detection service (in-domain vs out-domain)
- [ ] Search functionality with keyword matching
- [ ] LLM integration for context-aware responses
- [ ] Knowledge retrieval service using stored profiles
- [ ] Chat API endpoint with scope-aware logic
- [ ] React chat interface with message history
- [ ] Out-of-scope question handling with suggestions
- [ ] Relevance scoring for search results

#### **Review Requirements (Must Pass to Proceed):**
- [ ] **AI Integration Review**: Effective scope management and context awareness
- [ ] **Architecture Review**: Clean separation of knowledge and AI components
- [ ] **Code Quality Review**: Maintainable search and query systems

### **Milestone 3: Production Features & Knowledge Management**
**Estimated Time**: 3-4 hours (flexible based on previous milestones)

#### **Deliverables:**
- [ ] Profile browsing interface with filtering
- [ ] Multi-view React application (Chat, Browse, Search)
- [ ] CRUD operations for profiles (stretch goal)
- [ ] Export/import functionality for knowledge base
- [ ] Analytics dashboard (profile stats, query stats)
- [ ] Comprehensive documentation and testing
- [ ] Production deployment preparation

#### **Review Requirements (Must Pass for Project Completion):**
- [ ] **Architecture Review**: Scalable knowledge management system
- [ ] **AI Integration Review**: Production-ready scope-aware AI
- [ ] **Security Review**: Complete data security and access control
- [ ] **Code Quality Review**: Final production-ready code quality

### **Milestone Progression Rules:**
- **Cannot advance** to next milestone without passing all review requirements
- **Flexible timing** allows for learning at individual pace
- **Quality gates** ensure each milestone meets professional standards
- **Mentor support** available for concept clarification and review failures

## **Stretch Goals (Nice-to-have)**

### **Basic Enhancements**
- **CRUD Interface**: Admin panel for managing profiles (add, edit, delete)
- **Advanced Search**: Fuzzy matching, typo tolerance, synonym support
- **Analytics Dashboard**: Query statistics, popular searches, usage metrics
- **Export Functionality**: Download knowledge base as JSON or CSV
- **Import Functionality**: Bulk upload profiles from JSON/CSV

### **Advanced Features**
- **Semantic Search**: Use vector embeddings for semantic similarity search
- **RAG Pipeline**: Implement formal Retrieval-Augmented Generation
- **Auto-Discovery**: Automatically find and scrape new profile pages
- **Data Visualization**: Organization chart, team hierarchy visualization
- **Auto-Updates**: Scheduled re-scraping to keep data current
- **Multi-Source**: Scrape from multiple websites, consolidate knowledge
- **User Authentication**: JWT-based auth for personalized experience
- **Conversation History**: Store and retrieve past chat sessions

### **AI/ML Enhancements**
- **Intent Classification**: ML model for better query understanding
- **Named Entity Recognition**: Extract names, roles, departments from queries
- **Question Answering**: Fine-tuned model for domain-specific Q&A
- **Smart Suggestions**: Predict next questions based on context
- **Sentiment Analysis**: Detect user satisfaction with responses

## **Technical Specifications**

### **Database Schema Design**

#### **Profile Schema (Mongoose)**

**Required Fields:**
Your Profile model should include:
- `name` (String, required, indexed): Full name of the person
- `role` or `title` (String, required, indexed): Job title/position
- `department` (String, optional, indexed): Department or team
- `bio` (String, required): Biography or description
- `photoUrl` (String, optional): URL to profile photo
- `email` (String, optional): Email address
- `phone` (String, optional): Phone number
- `linkedin` (String, optional): LinkedIn profile URL
- `sourceUrl` (String, required): Original webpage URL
- `scrapedAt` (Date, default: Date.now): When data was scraped
- `updatedAt` (Date, default: Date.now): Last update timestamp
- `isActive` (Boolean, default: true): Whether profile is active
- `metadata` (Object, optional): Additional custom fields

**Indexes:**
- Text index on: name, role, bio, department (for text search)
- Compound index on: department + role (for filtered queries)
- Single indexes on: name, role, department (for sorting/filtering)

**Schema Validation:**
- Name: minimum 2 characters, maximum 100
- Role: minimum 2 characters, maximum 100
- Bio: minimum 10 characters, maximum 5000
- Email: valid email format (regex validation)
- URLs: valid URL format

**Schema Methods:**
- `getFullInfo()`: Return complete profile information
- `getBasicInfo()`: Return name, role, department only
- `updateLastModified()`: Update timestamp
- `isRecent()`: Check if scraped within last 30 days

### **Backend API Structure**

#### **Chat Endpoints**

**1. Send Query**
```
POST /api/chat/query
Content-Type: application/json

Request:
{
  "message": "Who is the CEO?",
  "sessionId": "optional-session-id"
}

Success Response (200):
{
  "success": true,
  "data": {
    "inScope": true,
    "response": "Joel Gascoigne is the founding partner and CEO...",
    "profiles": [
      {
        "_id": "...",
        "name": "Joel Gascoigne",
        "role": "CEO",
        "photoUrl": "..."
      }
    ],
    "confidence": 0.95,
    "sources": ["profile-id-1"]
  },
  "timestamp": "2024-01-30T10:30:00.000Z"
}

Out-of-Scope Response (200):
{
  "success": true,
  "data": {
    "inScope": false,
    "response": "I only have information about our leadership team...",
    "suggestions": [
      "Who are the company founders?",
      "Tell me about the CTO",
      "List all VPs"
    ]
  },
  "timestamp": "2024-01-30T10:30:00.000Z"
}
```

#### **Profile Endpoints**

**1. Get All Profiles**
```
GET /api/profiles?department=engineering&limit=10&sort=name

Response (200):
{
  "success": true,
  "data": {
    "profiles": [
      {
        "_id": "...",
        "name": "John Doe",
        "role": "VP Engineering",
        "department": "Engineering",
        "bio": "...",
        "photoUrl": "...",
        "email": "john@example.com"
      }
    ],
    "total": 25,
    "page": 1,
    "limit": 10
  }
}
```

**2. Get Profile by ID**
```
GET /api/profiles/:id

Response (200):
{
  "success": true,
  "data": {
    "profile": {
      "_id": "...",
      "name": "John Doe",
      "role": "VP Engineering",
      "department": "Engineering",
      "bio": "Detailed biography...",
      "photoUrl": "...",
      "email": "john@example.com",
      "linkedin": "...",
      "scrapedAt": "2024-01-30T10:00:00.000Z"
    }
  }
}
```

**3. Create Profile (Stretch Goal)**
```
POST /api/profiles
Content-Type: application/json

Request:
{
  "name": "Jane Smith",
  "role": "CTO",
  "department": "Technology",
  "bio": "Technology leader with 15 years experience...",
  "photoUrl": "https://example.com/photo.jpg",
  "email": "jane@example.com"
}

Response (201):
{
  "success": true,
  "data": {
    "profile": { ...created profile... }
  },
  "message": "Profile created successfully"
}
```

**4. Update Profile (Stretch Goal)**
```
PUT /api/profiles/:id
Content-Type: application/json

Request:
{
  "bio": "Updated biography...",
  "role": "Chief Technology Officer"
}

Response (200):
{
  "success": true,
  "data": {
    "profile": { ...updated profile... }
  },
  "message": "Profile updated successfully"
}
```

**5. Delete Profile (Stretch Goal)**
```
DELETE /api/profiles/:id

Response (200):
{
  "success": true,
  "message": "Profile deleted successfully"
}
```

#### **Search Endpoints**

**1. Search Profiles**
```
POST /api/search
Content-Type: application/json

Request:
{
  "query": "engineering leader",
  "filters": {
    "department": "Engineering",
    "role": "VP"
  },
  "limit": 5
}

Response (200):
{
  "success": true,
  "data": {
    "results": [
      {
        "profile": { ...profile data... },
        "score": 0.92,
        "matchedFields": ["role", "bio"]
      }
    ],
    "total": 3,
    "query": "engineering leader"
  }
}
```

#### **Scraper Endpoints**

**1. Trigger Scraping (Admin)**
```
POST /api/scraper/run
Content-Type: application/json

Request:
{
  "url": "https://buffer.com/about",
  "options": {
    "updateExisting": true,
    "validateData": true
  }
}

Response (200):
{
  "success": true,
  "data": {
    "profilesScraped": 7,
    "profilesCreated": 2,
    "profilesUpdated": 5,
    "errors": [],
    "duration": 5.2
  },
  "message": "Scraping completed successfully"
}
```

**2. Get Scraping Status**
```
GET /api/scraper/status

Response (200):
{
  "success": true,
  "data": {
    "lastRun": "2024-01-30T10:00:00.000Z",
    "totalProfiles": 25,
    "status": "idle",
    "nextScheduledRun": "2024-01-31T10:00:00.000Z"
  }
}
```

### **Web Scraping Architecture**

#### **Scraper Service Requirements**

**Target Website Structure:**
Your scraper should handle:
- Leadership/team pages with multiple profiles
- Individual profile cards or sections
- Nested HTML structures
- Different layout patterns
- Missing or optional fields

**Data Extraction Strategy:**

**Profile Container Identification:**
- Locate profile containers (divs, sections, articles)
- Identify common parent selectors
- Handle different HTML structures
- Support multiple profile formats

**Field Extraction:**
Extract from various sources:
- **Name**: h1-h4 headings, name classes, specific data attributes
- **Role/Title**: Subtitle elements, role classes, position indicators
- **Bio**: Paragraph tags, description classes, about sections
- **Photo**: img tags, background images, data-src attributes
- **Email**: mailto links, email classes, contact info
- **Phone**: tel links, phone classes, contact sections
- **LinkedIn**: LinkedIn URLs in links or social sections
- **Department**: Team labels, category classes, group indicators

**Data Cleaning Pipeline:**
- Remove HTML tags and entities
- Trim whitespace and normalize spacing
- Fix character encoding issues
- Standardize phone number formats
- Validate email addresses
- Normalize names (proper case)
- Clean and truncate bios
- Validate URLs

**Data Validation:**
- Ensure required fields are present (name, role)
- Validate data types and formats
- Check data length constraints
- Verify URL accessibility
- Detect and handle duplicates
- Flag incomplete profiles

#### **Structured Data Extraction**

**Multi-Profile Handling:**
Your scraper should:
- Identify all profile containers on page
- Extract data from each profile independently
- Handle varying numbers of profiles
- Process profiles in parallel or sequence
- Aggregate results efficiently

**Error Handling:**
- Handle missing fields gracefully (use defaults)
- Continue processing if one profile fails
- Log extraction errors with context
- Provide detailed error reports
- Retry failed extractions

**Data Normalization:**
- Standardize role titles (CEO, Chief Executive Officer → CEO)
- Normalize department names
- Clean and format contact information
- Remove duplicate whitespace
- Handle special characters

### **Scope Detection Architecture**

#### **Scope Detection Service Requirements**

**In-Scope Detection:**
Your scope detector should identify questions about:
- **People Queries**: who, whose, person, team member, executive
- **Role Queries**: CEO, CTO, VP, director, manager, head, president
- **Team Queries**: leadership, team, staff, employees, executives
- **Information Queries**: tell me about, who is, describe, background
- **List Queries**: list, show, display, all, team members

**Out-of-Scope Detection:**
Identify questions NOT about leadership:
- Weather, news, current events
- Stock prices, financials, revenue
- Product information, services
- Technical support, troubleshooting
- General knowledge, definitions
- Time, date, calculations

**Detection Strategy:**

**Keyword-Based Detection:**
- Check for leadership-related keywords
- Check for person names in query
- Check for role/title keywords
- Check for team/organization keywords
- Score based on keyword matches

**Pattern Matching:**
- Question patterns: "Who is...", "Tell me about...", "Who leads..."
- List patterns: "List all...", "Show me...", "Give me..."
- Information patterns: "What does... do?", "What's... role?"

**Confidence Scoring:**
- Calculate scope confidence (0-1)
- High confidence (0.8+): Definitely in-scope
- Medium confidence (0.5-0.8): Likely in-scope
- Low confidence (<0.5): Likely out-of-scope

**Fallback Handling:**
For out-of-scope questions:
- Explain scope limitations clearly
- Provide helpful suggestions
- Show example questions
- Offer alternative resources

### **Knowledge Retrieval Architecture**

#### **Search Implementation**

**Search Types:**

**1. Text Search (MongoDB)**
Use MongoDB text search capabilities:
- Full-text search on name, role, bio, department
- Relevance scoring based on text match
- Support for partial matches
- Case-insensitive search

**2. Keyword Matching**
Simple keyword-based search:
- Extract keywords from query
- Match against profile fields
- Score by number of matches
- Rank by relevance

**3. Filtered Search**
Search with filters:
- Filter by department
- Filter by role/seniority
- Filter by name (starts with, contains)
- Combine filters with text search

**Search Query Processing:**
- Extract search terms from user question
- Remove stop words (the, a, an, is, etc.)
- Normalize terms (lowercase)
- Handle synonyms (CEO = Chief Executive Officer)
- Generate MongoDB query or filter logic

**Relevance Scoring:**
Score results based on:
- Exact name match (highest score)
- Role match (high score)
- Department match (medium score)
- Bio keyword match (lower score)
- Multiple field matches (boost score)

**Result Ranking:**
- Sort by relevance score (descending)
- Break ties by role importance
- Limit results to top 5-10
- Include score in results for debugging

#### **LLM Integration for Knowledge Retrieval**

**Context Building:**
When answering queries:
1. Search knowledge base for relevant profiles
2. Build context from top matching profiles
3. Include profile data in LLM prompt
4. Request structured response from LLM

**Prompt Engineering:**
Your prompts should:
- Provide context about the knowledge domain
- Include retrieved profile information
- Specify response format and structure
- Request concise, accurate answers
- Handle multiple profiles appropriately

**Response Generation:**
- Use LLM for natural language responses
- Format profile information clearly
- Cite sources (profile names) when relevant
- Maintain conversation context
- Handle follow-up questions

### **Frontend Architecture**

#### **Chat Interface Requirements**

**ChatWindow Component:**
Your chat interface should:
- Display conversation history
- Show user and bot messages differently
- Auto-scroll to latest message
- Handle loading states
- Display scope indicators
- Show profile cards for relevant people
- Support message actions (copy, etc.)

**ChatMessage Component:**
- Display message content with formatting
- Show timestamp
- Distinguish user vs bot messages
- Support rich content (links, images, profile cards)
- Handle long messages with expansion

**ChatInput Component:**
- Text input with multi-line support
- Send button with keyboard shortcuts (Enter)
- Character limit indicator
- Disabled during processing
- Clear after sending
- Placeholder with example questions

**ScopeIndicator Component:**
- Show if question is in-scope or out-of-scope
- Visual indicator (green/red badge)
- Tooltip with explanation
- Confidence score display (optional)

#### **Profile Browser Requirements**

**ProfileBrowser Component:**
Your browser should:
- Display profiles in grid or list view
- Support view toggle (grid/list)
- Implement filtering by department, role
- Implement sorting (name, role, department)
- Show profile count
- Handle empty states
- Pagination for large datasets

**ProfileCard Component:**
- Display profile photo
- Show name prominently
- Display role and department
- Show truncated bio with "Read more"
- Click to view detailed profile
- Responsive card design

**ProfileDetail Component:**
- Full profile information display
- Photo in larger size
- Complete bio
- Contact information (email, phone, LinkedIn)
- Back to browse button
- Share profile option (stretch)

**ProfileFilter Component:**
- Filter by department (dropdown or checkboxes)
- Filter by role/seniority
- Search by name
- Clear filters button
- Active filter indicators

#### **Search Interface Requirements**

**SearchBar Component:**
- Search input with autocomplete (optional)
- Search button
- Clear search button
- Search suggestions based on common queries
- Recent searches (optional)

**SearchResults Component:**
- Display matching profiles
- Show relevance scores (optional)
- Highlight matching terms
- Quick view of profile info
- Click to view full profile
- No results state with suggestions

**Filters Component:**
- Advanced filtering options
- Range filters (optional)
- Multi-select filters
- Apply/reset buttons

### **State Management**

#### **Global State (Context API)**

**Application State:**
- Current user query
- Chat history (array of messages)
- Available profiles (cached)
- Active filters
- Current view (chat, browse, search)
- Loading states
- Error states

**Profile State:**
- All profiles list
- Filtered profiles list
- Selected profile
- Profile categories
- Departments list

**Search State:**
- Current search query
- Search results
- Active filters
- Search history

#### **Local Component State**

Each component manages:
- Form input values
- UI states (expanded, collapsed)
- Temporary data
- Validation errors

### **Performance Considerations**

#### **Backend Optimization**

**Database Performance:**
- Proper indexing on frequently queried fields
- Text indexes for search
- Compound indexes for filtered queries
- Limit query results appropriately
- Use projection to return only needed fields
- Implement pagination for large datasets

**Search Performance:**
- Cache frequent searches (optional)
- Optimize text search queries
- Limit result set size
- Use aggregation pipeline efficiently
- Index commonly filtered fields

**LLM Integration:**
- Limit context size to relevant profiles only
- Cache identical queries (optional)
- Set appropriate token limits
- Implement timeout handling
- Use faster models for simple queries

#### **Frontend Optimization**

**Component Performance:**
- Use React.memo for profile cards
- Implement virtual scrolling for long lists (optional)
- Lazy load images
- Debounce search input
- Minimize re-renders
- Use proper key props in lists

**Data Fetching:**
- Cache profile data in context
- Implement optimistic updates
- Prefetch likely-needed data
- Use proper loading states
- Handle stale data appropriately

**User Experience:**
- Target < 2 seconds for queries
- Show instant feedback on actions
- Smooth transitions between views
- Responsive on all devices
- Accessible keyboard navigation

### **Security Requirements**

#### **Data Security**

**Database Security:**
- Use strong MongoDB credentials
- Limit database user permissions
- Enable MongoDB authentication
- Use environment variables for credentials
- Never expose database connection strings

**Input Validation:**
- Validate all API inputs
- Sanitize user queries
- Prevent NoSQL injection
- Validate profile data before storage
- Limit input lengths

**API Security:**
- Implement rate limiting
- Use CORS appropriately
- Set security headers (Helmet.js)
- Validate request origins
- Prevent CSRF attacks (if applicable)

#### **Web Scraping Security**

**Safe Scraping:**
- Respect robots.txt
- Implement rate limiting
- Set appropriate timeouts
- Use proper User-Agent
- Handle errors gracefully
- Don't overload target servers

**Data Privacy:**
- Only scrape publicly available data
- Don't store sensitive information
- Respect data retention policies
- Implement data deletion capability
- Comply with data protection regulations

### **Environment Variables**

#### **Server (.env)**
```
# Server Configuration
PORT=5000
NODE_ENV=development

# MongoDB Configuration
MONGODB_URI=mongodb://localhost:27017/knowledge-repo
MONGODB_DB_NAME=knowledge_repo

# LLM API Keys (use only one)
OPENAI_API_KEY=your_openai_key_here
GEMINI_API_KEY=your_gemini_key_here

# Scraping Configuration
TARGET_URL=https://buffer.com/about
SCRAPING_TIMEOUT=15000
USER_AGENT=Mozilla/5.0 (Knowledge Scraper Bot)

# CORS Configuration
CORS_ORIGIN=http://localhost:5173

# Rate Limiting
RATE_LIMIT_WINDOW=15
RATE_LIMIT_MAX=100

# Security
JWT_SECRET=your_jwt_secret_here  # If implementing auth
```

#### **Client (.env)**
```
# API Configuration
VITE_API_BASE_URL=http://localhost:5000

# App Configuration
VITE_APP_NAME=Knowledge Repository
VITE_MAX_PROFILES_PER_PAGE=12
```

### **Documentation Requirements**

#### **README.md Structure**

**Project Overview:**
- Description of the knowledge repository
- Technologies used (MERN stack)
- Key features and capabilities
- Use cases

**Prerequisites:**
- Node.js 18+
- MongoDB 6+
- OpenAI or Gemini API key
- Git

**Installation:**
- Clone repository
- Install dependencies (client + server)
- Setup MongoDB
- Configure environment variables
- Seed initial data
- Run application

**Usage Guide:**
- How to chat with the bot
- How to browse profiles
- How to search knowledge base
- Understanding scope (in/out-domain)
- Admin features (if implemented)

**API Documentation:**
- All endpoints with examples
- Request/response formats
- Error codes
- Rate limits

**Database Schema:**
- Profile model structure
- Indexes explanation
- Sample documents

**Deployment:**
- MongoDB Atlas setup
- Backend deployment (Render/Railway)
- Frontend deployment (Vercel/Netlify)
- Environment configuration

#### **KNOWLEDGE_DEMO.md**

Include:
- At least 5 sample profiles extracted
- 10+ Q&A examples:
  - In-scope questions (who is CEO, tell me about CTO, list VPs)
  - Out-of-scope questions (weather, stocks, general knowledge)
- Screenshots of:
  - Chat interface
  - Profile browser
  - Search results
  - Scope indicators
- Data sources used
- Scraping success rate
- Query accuracy metrics

#### **Technical_Learnings.md**

Document:
- MongoDB schema design learnings
- Web scraping for structured data
- Scope detection implementation
- Text search and relevance scoring
- LLM integration for knowledge retrieval
- React multi-view application design
- Challenges and solutions
- Time spent per milestone
- Key technical decisions
- Future improvements

## **Deliverables**

1. **GitHub Repository** (public or invite @mentor)
   - Complete MERN stack application
   - Comprehensive documentation
   - Clean commit history
   - Proper .gitignore

2. **Live Demo** (choose one)
   - Local demo with sample Q&A session
   - Deployed application URL
   - Screen recording

3. **KNOWLEDGE_DEMO.md**
   - 5+ extracted profiles with full data
   - 10+ Q&A examples (in-scope and out-of-scope)
   - Screenshots of all major features
   - Data sources and scraping statistics
   - Accuracy metrics

4. **Technical_Learnings.md**
   - Detailed learning journey
   - Technical challenges and solutions
   - Time breakdown by milestone
   - Key insights and takeaways

## **Evaluation Rubric (100 Points)**

| Criterion | Points | Details |
| ----- | :---: | ----- |
| **Data Extraction** | 30 pts | ✓ Successfully scrapes structured profile data (12 pts) <br> ✓ Handles multiple profiles automatically (8 pts) <br> ✓ Data quality and completeness (10 pts) |
| **Knowledge Search** | 25 pts | ✓ Accurate search and retrieval (10 pts) <br> ✓ Relevant results for queries (10 pts) <br> ✓ Handles name/role variations (5 pts) |
| **Scope Management** | 20 pts | ✓ Correctly identifies in-scope questions (8 pts) <br> ✓ Graceful out-of-scope responses (7 pts) <br> ✓ Helpful redirection suggestions (5 pts) |
| **AI Integration** | 15 pts | ✓ Context-aware LLM responses (6 pts) <br> ✓ Natural conversation flow (5 pts) <br> ✓ Accurate information presentation (4 pts) |
| **Code Quality** | 10 pts | ✓ Clean project structure (4 pts) <br> ✓ Good documentation (3 pts) <br> ✓ Error handling (3 pts) |

### **Minimum Passing Requirements**
- Total score ≥ 70/100
- Data Extraction score ≥ 20/30
- Scope detection accuracy ≥ 80%
- All review requirements passed
- Working demo with diverse Q&A

## **Sample Interactions**

### **In-Scope Questions**

```
User: "Who is the CEO?"
Bot: "Joel Gascoigne is the CEO and Co-founder of Buffer. With a passion 
for transparency and building products that help people, Joel has led Buffer to 
become one of the most transparent companies in the world. Under his leadership, 
Buffer has grown to help millions of users manage their social media presence 
across multiple platforms. He is based in San Francisco, CA..."

[Profile card shown with photo, contact info]

User: "Tell me about the Leadership Team"
Bot: "Here are the key members of our leadership team:

1. Joel Gascoigne - CEO & Co-founder
2. Leo Widrich - Co-founder & CMO
3. Carolyn Kopprasch - Chief Marketing Officer
4. Dan Holloway - VP Engineering
5. Nicole Miller - Head of People
6. Brian Peters - Director of Marketing
7. Katie Zhu - Head of Engineering

Would you like to know more about any specific team member?"

[Profile cards shown for each person]

User: "Who leads the technology team?"
Bot: "Based on our leadership team, the technology and engineering functions 
are overseen by [Name], our [Role]. [He/She] has [X] years of experience in 
[relevant background]..."

User: "What's Sarah's background?"
Bot: "I found Sarah Johnson in our leadership team. She is our VP of Product 
Development with 15 years of experience in product management and strategy. 
Before joining us, she led product teams at [previous companies]..."
```

### **Out-of-Scope Questions**

```
User: "What's the weather today?"
Bot: "I only have information about our leadership team and can't help with 
weather questions. I can tell you about our executives, their backgrounds, 
roles, or experience. 

Try asking something like:
• 'Who are the company founders?'
• 'Tell me about the CTO'
• 'List all VPs'
• 'Who leads the engineering team?'"

User: "What's the stock price?"
Bot: "I don't have access to financial or stock information. My knowledge is 
focused on our leadership team profiles. I can share details about our 
executives' backgrounds, roles, and experience instead.

Would you like to know about any specific team member?"

User: "How do I reset my password?"
Bot: "I can't help with technical support or account issues. I specialize in 
answering questions about our leadership team.

For technical support, please contact: support@example.com

Is there anything you'd like to know about our executives?"
```

## **Testing Scenarios**

### **Data Collection Testing**

**Target URL:** https://buffer.com/about

**Expected Results:**
- Extract all team member profiles (7+ profiles from Buffer's about page)
- Complete data for each profile (name, role, bio)
- Valid photo URLs
- Contact information where available
- Proper data normalization

**Validation Checks:**
- All required fields present
- No duplicate profiles
- Data matches source website
- URLs are accessible
- Text is properly cleaned

### **Question Categories to Test**

**✅ In-Scope Questions:**
- "Who is the CEO?"
- "Tell me about the CTO"
- "List all VPs"
- "What's Sarah's background?"
- "Who leads the engineering team?"
- "Show me the leadership team"
- "Who is Joel Gascoigne?"
- "What does the CFO do?"
- "Tell me about our executives"
- "Who manages operations?"

**❌ Out-of-Scope Questions:**
- "What's the weather?"
- "How's the stock doing?"
- "What's for lunch?"
- "Current news updates?"
- "Company financials?"
- "How do I apply for a job?"
- "What products do you sell?"
- "When was the company founded?"
- "Where is the office located?"
- "How many employees work here?"

### **Search Testing**

**Name Searches:**
- Exact match: "Joel Gascoigne"
- Partial match: "Joel"
- Last name only: "Gascoigne"
- Typo tolerance: "Joel Gascoine"

**Role Searches:**
- Exact title: "CEO"
- Generic role: "director"
- Department: "finance"
- Level: "VP"

**Content Searches:**
- Keywords from bio: "entrepreneurial"
- Skills: "product development"
- Experience: "25 years"

## **Quick Start Resources**

### **MERN Stack**
- **MongoDB Tutorial**: https://www.mongodb.com/docs/manual/tutorial/
- **Mongoose Guide**: https://mongoosejs.com/docs/guide.html
- **Express.js**: https://expressjs.com/en/guide/routing.html
- **React**: https://react.dev/learn

### **Web Scraping**
- **Cheerio**: https://cheerio.js.org/
- **Scraping with Node.js**: https://www.freecodecamp.org/news/web-scraping-in-nodejs/
- **Axios**: https://axios-http.com/docs/intro

### **Database Design**
- **MongoDB Schema Design**: https://www.mongodb.com/blog/post/6-rules-of-thumb-for-mongodb-schema-design
- **Mongoose Schemas**: https://mongoosejs.com/docs/guide.html
- **Indexing**: https://www.mongodb.com/docs/manual/indexes/

### **Search Implementation**
- **MongoDB Text Search**: https://www.mongodb.com/docs/manual/text-search/
- **Search Algorithm**: https://www.mongodb.com/docs/manual/reference/operator/query/text/

### **LLM Integration**
- **OpenAI API**: https://platform.openai.com/docs/api-reference
- **Prompt Engineering**: https://platform.openai.com/docs/guides/prompt-engineering
- **Google Gemini**: https://ai.google.dev/gemini-api/docs

### **Frontend**
- **React Router**: https://reactrouter.com/en/main
- **Context API**: https://react.dev/reference/react/useContext
- **Tailwind CSS**: https://tailwindcss.com/docs

## **FAQ**

**Q: Which database should I use - MongoDB or SQLite?**  
A: Use MongoDB for this project. It's part of the MERN stack, supports text search natively, and provides better scalability. MongoDB is industry-standard for modern applications.

**Q: How do I handle missing fields during scraping?**  
A: Make only name and role required. Other fields (email, phone, LinkedIn) are optional. Set defaults for missing data and flag incomplete profiles for review.

**Q: How accurate should scope detection be?**  
A: Target 90%+ accuracy. Use keyword matching and pattern detection. Test with diverse questions. It's okay to occasionally misclassify edge cases.

**Q: Should I use vector embeddings for search?**  
A: Not for core requirements. Start with MongoDB text search or simple keyword matching. Vector search is a valuable stretch goal using MongoDB Atlas Vector Search.

**Q: How do I prevent duplicate profiles?**  
A: Check for existing profiles by name before insertion. Implement upsert logic (update if exists, insert if new). Use unique indexes on name+role combination.

**Q: Can I scrape from multiple websites?**  
A: Focus on one target site for core requirements (buffer.com/about). Multi-source scraping is an excellent stretch goal.

**Q: How should I structure the LLM prompt?**  
A: Include system message explaining the domain (leadership Q&A), provide retrieved profile data as context, specify response format, and request concise answers. See AI Integration section for details.

**Q: Should I implement user authentication?**  
A: Not required for core project. Focus on knowledge management and chat functionality. Auth is a valuable stretch goal for personalized experience.

**Q: How do I test scope detection?**  
A: Create a test suite with 20+ questions (half in-scope, half out-of-scope). Calculate accuracy as (correct classifications / total questions). Aim for 90%+.

**Q: What if the website structure changes?**  
A: Implement flexible selectors using multiple fallback strategies. Document the current HTML structure. Add error handling for missing elements.

**Q: How many profiles should I extract?**  
A: Minimum 5 profiles for a working demo. Buffer's about page has 7+ team members which is perfect. More profiles = better demo.

**Q: Should I cache LLM responses?**  
A: Optional but recommended for stretch goals. Cache based on query similarity to reduce API costs and improve response time.

## **Measurable Goals & Review Template Compliance**

### **Primary Objectives (Must Complete for Project Advancement)**
- [ ] **Knowledge Management Excellence**: 90%+ accurate scope detection and responses
- [ ] **Data Architecture Quality**: Pass Architecture Review with 8.5/10+ score
- [ ] **AI Integration Sophistication**: Pass AI Integration Review with 8.5/10+ score
- [ ] **Search Performance**: Sub-second search response times with relevant results
- [ ] **Code Quality Standards**: Pass Code Quality Review with 8/10+ score
- [ ] **Security Compliance**: Pass Security Review with 8.5/10+ score

### **Review Template Integration (All Must Pass)**

#### **Architecture Review Requirements (Critical for Knowledge Systems)**
```
architecture_criteria = {
    "data_modeling": {"target": 90, "weight": 30},              # Clean knowledge schema design
    "search_architecture": {"target": 85, "weight": 25},        # Efficient search implementation
    "separation_of_concerns": {"target": 85, "weight": 20},     # Clean component separation
    "scalability_design": {"target": 80, "weight": 15},         # Future expansion capability
    "integration_patterns": {"target": 80, "weight": 10}        # Clean API/service integration
}
```

#### **AI Integration Review Requirements**
```
ai_integration_criteria = {
    "scope_management": {"target": 90, "weight": 35},           # Accurate domain detection
    "context_awareness": {"target": 85, "weight": 25},          # Knowledge-based responses
    "query_understanding": {"target": 85, "weight": 20},        # Intent recognition
    "response_quality": {"target": 80, "weight": 15},           # Helpful, accurate answers
    "fallback_handling": {"target": 85, "weight": 5}            # Out-of-scope management
}
```

#### **Performance Review Requirements**
```
performance_criteria = {
    "search_speed": {"target": 95, "weight": 30},               # Sub-second search responses
    "data_retrieval": {"target": 85, "weight": 25},             # Efficient knowledge access
    "memory_efficiency": {"target": 80, "weight": 20},          # Optimal resource usage
    "concurrent_users": {"target": 75, "weight": 15},           # Multi-user support
    "storage_optimization": {"target": 80, "weight": 10}        # Efficient data storage
}
```

#### **Security Review Requirements**
```
security_criteria = {
    "data_security": {"target": 90, "weight": 30},              # Database security
    "input_validation": {"target": 90, "weight": 25},           # Query validation
    "api_security": {"target": 85, "weight": 20},               # Endpoint security
    "scraping_safety": {"target": 85, "weight": 15},            # Safe web scraping
    "access_control": {"target": 80, "weight": 10}              # Data access control
}
```

#### **Code Quality Review Requirements**
```
code_quality_criteria = {
    "readability": {"target": 85, "weight": 25},                # Clear code structure
    "organization": {"target": 85, "weight": 25},               # Proper separation
    "documentation": {"target": 80, "weight": 20},              # Complete documentation
    "maintainability": {"target": 80, "weight": 20},            # Clean patterns
    "testing": {"target": 75, "weight": 10}                     # Testing coverage
}
```

### **Performance Standards**
- **Search Response Time**: < 1 second for keyword searches
- **Scope Detection Accuracy**: 90%+ correct in-domain/out-of-domain classification
- **Knowledge Retrieval**: Relevant results in top 3 for 85% of queries
- **Data Integrity**: 100% consistency in profile data storage and retrieval
- **API Response Time**: < 2 seconds for chat queries with LLM
- **Database Query Time**: < 100ms for profile lookups

## **Common Pitfalls to Avoid**

### **Data Extraction Issues**
- ❌ Hardcoding HTML selectors (use flexible fallbacks)
- ❌ Not handling missing fields gracefully
- ❌ Storing duplicate profiles
- ❌ Not cleaning/normalizing data
- ❌ Ignoring data validation
- ❌ Not handling different HTML structures

### **Database Issues**
- ❌ No indexes on frequently queried fields
- ❌ Not using Mongoose schema validation
- ❌ Storing unstructured data inconsistently
- ❌ Not handling connection errors
- ❌ Poor schema design (denormalized where needed)

### **Scope Detection Issues**
- ❌ Too strict (rejecting valid in-scope questions)
- ❌ Too lenient (accepting clearly out-of-scope questions)
- ❌ Not providing helpful suggestions
- ❌ Poor error messages for out-of-scope
- ❌ Not handling edge cases

### **Search Issues**
- ❌ Case-sensitive search
- ❌ No partial matching
- ❌ Poor relevance scoring
- ❌ Returning too many results
- ❌ Not handling typos
- ❌ Slow search performance

### **LLM Integration Issues**
- ❌ Not providing enough context
- ❌ Including irrelevant profile data
- ❌ Poor prompt engineering
- ❌ Not handling API errors
- ❌ Exceeding token limits
- ❌ Not caching responses

### **Frontend Issues**
- ❌ Poor chat UX (no loading states)
- ❌ Not clearing input after send
- ❌ Profile cards not clickable
- ❌ No empty states
- ❌ Not responsive on mobile
- ❌ Poor error handling

## **Success Checklist**

Before submitting your project, verify:

### **Functionality**
- [ ] Successfully scrapes 5+ leadership profiles
- [ ] Stores all profiles in MongoDB
- [ ] Chat interface works with message history
- [ ] Correctly answers in-scope questions
- [ ] Handles out-of-scope questions gracefully
- [ ] Search functionality returns relevant results
- [ ] Profile browser displays all profiles
- [ ] Application works on desktop and mobile

### **Data Quality**
- [ ] All required profile fields populated
- [ ] Data is cleaned and normalized
- [ ] No duplicate profiles
- [ ] Photos display correctly
- [ ] Contact information is valid
- [ ] Bios are properly formatted

### **Scope Detection**
- [ ] 90%+ accuracy on test question set
- [ ] Clear in-scope indicators
- [ ] Helpful out-of-scope messages
- [ ] Good suggestion questions
- [ ] Handles edge cases reasonably

### **Performance**
- [ ] Search responds in < 1 second
- [ ] Chat queries complete in < 2 seconds
- [ ] Database queries optimized with indexes
- [ ] No memory leaks or performance issues
- [ ] Handles multiple concurrent users

### **Code Quality**
- [ ] Clean separation of concerns
- [ ] Consistent code style
- [ ] Comprehensive documentation
- [ ] Proper error handling
- [ ] No code duplication
- [ ] Meaningful names and comments

### **Security**
- [ ] MongoDB credentials secured
- [ ] Input validation on all endpoints
- [ ] No secrets in git repository
- [ ] CORS properly configured
- [ ] Rate limiting implemented
- [ ] Secure error messages

### **Documentation**
- [ ] README with complete setup instructions
- [ ] KNOWLEDGE_DEMO.md with examples
- [ ] Technical_Learnings.md completed
- [ ] API endpoints documented
- [ ] Database schema documented
- [ ] Deployment guide included

## **Mentor Review Process**

### **Review Stages**

**Stage 1: Milestone 1 Review**
- Database schema design review
- Scraping implementation assessment
- Data quality evaluation
- Initial architecture review
- Feedback within 24-48 hours

**Stage 2: Milestone 2 Review**
- Scope detection accuracy testing
- Search functionality evaluation
- LLM integration assessment
- Chat interface review
- Mid-project feedback

**Stage 3: Final Review**
- Complete functionality testing
- Comprehensive documentation review
- Security assessment
- Performance testing
- Production readiness check
- Final scoring and detailed feedback

### **Review Metrics**
- Architecture: Clean schema, proper indexing, separation of concerns
- AI Integration: Scope accuracy, response quality, context awareness
- Performance: Search speed, query times, optimization
- Security: Data protection, input validation, safe practices
- Code Quality: Organization, documentation, maintainability

## **Post-Project Learning Path**

After completing this project successfully, consider:

### **Immediate Enhancements**
1. Implement CRUD interface for profile management
2. Add vector search with MongoDB Atlas
3. Implement RAG pipeline for better responses
4. Add conversation history and memory
5. Create organization chart visualization

### **Skill Building**
1. Advanced MongoDB (aggregation, transactions)
2. Vector databases and semantic search
3. Advanced prompt engineering
4. React advanced patterns (custom hooks, optimization)
5. Full-text search algorithms

### **Next Projects**
1. Full-stack application with authentication (Project 4)
2. Multi-tenant knowledge base
3. AI-powered customer support system
4. Document Q&A system with file uploads
5. Personal knowledge management tool

---

**Remember**: This project teaches you how to build domain-specific AI applications with scope management - a critical skill for real-world AI systems. Focus on data quality, accurate scope detection, and helpful user experience!