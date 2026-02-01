# **Project 2: Web Content Analyzer (MERN Stack)**

## **Objective (Why?)**

Build a web application that extracts content from any website URL and generates a comprehensive analysis report using LLM in just 2 days. This accelerated timeline leverages your Project 1 experience for rapid development. You will practice:

* **Web Scraping**: Extracting content from websites using Node.js libraries
* **Content Processing**: Cleaning and structuring scraped data
* **LLM Integration**: Using AI to analyze and summarize web content
* **API Development**: Building more complex backend endpoints with data processing
* **Full-Stack Integration**: Combining scraping, processing, and presentation layers

## **Core Requirements (Must-have)**

| Layer | Requirement |
| :---: | ----- |
| **Backend** | Node.js 18+ with Express.js <br> POST `/api/analyze` endpoint: `{"url": "https://example.com"}` → `{"analysis": {...}}` <br> Web scraping with Axios + Cheerio for HTML parsing <br> Content extraction: title, headings, main text, meta description, links <br> LLM integration for intelligent content analysis <br> URL validation and sanitization to prevent SSRF attacks <br> Error handling for invalid URLs, failed requests, and scraping failures <br> Content size limits and intelligent truncation |
| **Frontend** | React 18+ with Vite and Tailwind CSS <br> URL input form with validation <br> Analysis trigger button with loading states <br> Structured report display with expandable sections <br> Progress indicators during scraping and analysis <br> Error handling and user feedback <br> Responsive design for various screen sizes |
| **Content Processing** | Extract meaningful content (skip navigation, ads, footer, etc.) <br> Implement intelligent content filtering and cleaning <br> Limit content size to prevent LLM API token limits <br> Structure data appropriately for LLM analysis <br> Handle different website layouts and structures <br> Parse and categorize different content types (headings, paragraphs, lists) |

## **Technology Stack (MERN)**

### **Backend Stack**
- **Runtime**: Node.js 18+ (LTS)
- **Framework**: Express.js 4.x
- **Web Scraping**: Cheerio for HTML parsing, Axios for HTTP requests
- **Content Processing**: Custom text processing utilities
- **LLM Integration**: OpenAI SDK or Google Generative AI SDK
- **Validation**: express-validator for input validation
- **Security**: Helmet.js for security headers
- **Environment Variables**: dotenv

### **Frontend Stack**
- **Library**: React 18+
- **Build Tool**: Vite 5+
- **Styling**: Tailwind CSS 3+
- **HTTP Client**: Axios
- **State Management**: React Hooks (useState, useEffect, useReducer)
- **UI Components**: Custom components for analysis display

### **Optional Enhancements**
- **Database**: MongoDB with Mongoose (for analysis history)
- **Caching**: Redis or in-memory cache for repeated URLs
- **Advanced Scraping**: Puppeteer for JavaScript-heavy sites
- **Rate Limiting**: express-rate-limit
- **PDF Export**: jsPDF or similar library

## **Project Structure**

```
mern-web-analyzer/
├── client/                           # React frontend
│   ├── public/
│   │   └── vite.svg
│   ├── src/
│   │   ├── components/
│   │   │   ├── UrlInput.jsx         # URL input form
│   │   │   ├── AnalysisReport.jsx   # Main report display
│   │   │   ├── ReportSection.jsx    # Individual report sections
│   │   │   ├── LoadingIndicator.jsx # Loading states
│   │   │   ├── ErrorDisplay.jsx     # Error messages
│   │   │   └── ProgressBar.jsx      # Progress tracking
│   │   ├── services/
│   │   │   └── analyzerService.js   # API communication
│   │   ├── utils/
│   │   │   ├── urlValidator.js      # Client-side URL validation
│   │   │   └── formatters.js        # Data formatting utilities
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
├── server/                           # Express backend
│   ├── config/
│   │   ├── env.js                   # Environment configuration
│   │   └── allowedDomains.js        # URL whitelist/blacklist
│   ├── controllers/
│   │   └── analyzerController.js    # Request handlers
│   ├── routes/
│   │   └── analyzerRoutes.js        # API routes
│   ├── services/
│   │   ├── scraperService.js        # Web scraping logic
│   │   ├── contentProcessor.js      # Content cleaning & structuring
│   │   ├── llmService.js            # LLM analysis integration
│   │   └── urlValidator.js          # URL validation & SSRF prevention
│   ├── middleware/
│   │   ├── errorHandler.js          # Error handling
│   │   ├── corsConfig.js            # CORS configuration
│   │   ├── securityHeaders.js       # Helmet security setup
│   │   └── rateLimiter.js           # Rate limiting (optional)
│   ├── utils/
│   │   ├── textCleaner.js           # Text processing utilities
│   │   └── contentExtractor.js      # HTML content extraction
│   ├── .env.example
│   ├── .gitignore
│   ├── package.json
│   └── server.js                     # Entry point
│
├── .gitignore                        # Root gitignore
├── README.md                         # Project documentation
└── package.json                      # Optional: root package.json
```

## **Development Approach: Milestone-Based Progression**

**Philosophy**: Focus on **deliverable quality** and **comprehensive review compliance** rather than rigid timelines. Each milestone must pass all relevant review templates from our Templates folder before proceeding.

### **Milestone 1: Web Scraping Foundation & Data Extraction**
**Estimated Time**: 4-8 hours (flexible based on learning pace)

#### **Deliverables:**
- [ ] Working development environment with web scraping dependencies (Cheerio, Axios)
- [ ] Basic Express.js server with analyzer endpoint structure
- [ ] Web scraping service with content extraction capabilities
- [ ] URL validation and SSRF prevention system
- [ ] Basic React interface for URL input
- [ ] Content preprocessing and cleaning pipeline
- [ ] Error handling for scraping failures
- [ ] Security measures (input validation, private IP blocking)

#### **Review Requirements (Must Pass to Proceed):**
- [ ] **Security Review**: SSRF prevention, URL sanitization, input validation
- [ ] **Code Quality Review**: Clean separation of scraping logic and presentation
- [ ] **Performance Review**: Efficient content extraction and memory management

### **Milestone 2: LLM Integration & Analysis Engine**
**Estimated Time**: 4-6 hours (flexible based on Milestone 1 completion)

#### **Deliverables:**
- [ ] LLM integration for content analysis and report generation
- [ ] Structured analysis pipeline with intelligent prompting
- [ ] Content optimization for LLM processing (token limits)
- [ ] Comprehensive error handling for analysis failures
- [ ] Enhanced React UI with analysis display components
- [ ] Progress indicators during scraping and analysis phases
- [ ] Professional report formatting and structure

#### **Review Requirements (Must Pass to Proceed):**
- [ ] **AI Integration Review**: Optimal prompt engineering and content analysis quality
- [ ] **Performance Review**: Response times and content processing efficiency
- [ ] **Security Review**: Content sanitization and safe processing

### **Milestone 3: Production Features & Advanced Analysis**
**Estimated Time**: 3-5 hours (flexible based on previous milestones)

#### **Deliverables:**
- [ ] Advanced report formatting with expandable sections
- [ ] Multiple URL analysis capability (stretch goal)
- [ ] Export functionality - JSON download (stretch goal: PDF)
- [ ] Analysis history with MongoDB integration (stretch goal)
- [ ] Comprehensive documentation and testing procedures
- [ ] Production-ready deployment preparation
- [ ] Performance optimization and caching

#### **Review Requirements (Must Pass for Project Completion):**
- [ ] **Architecture Review**: Scalable design and component separation
- [ ] **Security Review**: Complete security assessment including SSRF prevention
- [ ] **AI Integration Review**: Production-ready analysis pipeline
- [ ] **Code Quality Review**: Final code quality and documentation standards

### **Milestone Progression Rules:**
- **Cannot advance** to next milestone without passing all review requirements
- **Flexible timing** allows for learning at individual pace
- **Quality gates** ensure each milestone meets professional standards
- **Mentor support** available for concept clarification and review failures

## **Stretch Goals (Nice-to-have)**

### **Basic Enhancements**
- **Analysis History**: Store previous analyses in MongoDB with timestamps
- **Export Functionality**: Download analysis as JSON, PDF, or Markdown
- **Content Categories**: Auto-detect website type (corporate, blog, news, e-commerce)
- **Bulk Analysis**: Analyze multiple URLs in sequence or parallel

### **Advanced Features**
- **JavaScript Rendering**: Use Puppeteer for JavaScript-heavy websites
- **Comparison Mode**: Compare content from multiple websites side-by-side
- **Scheduled Analysis**: Monitor websites and track changes over time
- **SEO Analysis**: Add SEO metrics (meta tags, keywords, readability scores)
- **Caching Layer**: Cache analyzed content to reduce API costs
- **User Authentication**: Save personal analysis history with JWT auth

### **UI/UX Enhancements**
- **Data Visualization**: Charts showing content statistics
- **Keyword Extraction**: Visual display of key terms and phrases
- **Content Preview**: Show original webpage in iframe alongside analysis
- **Analysis Templates**: Different analysis types (SEO, competitive, content audit)
- **Export Templates**: Customizable report formats

## **Technical Specifications**

### **Backend API Structure**

#### **1. Analyze Endpoint**
**Request Format:**
```
POST /api/analyze
Content-Type: application/json

{
  "url": "https://www.example.com",
  "options": {
    "analysisType": "comprehensive",  // optional: comprehensive, seo, summary
    "includeLinks": true,              // optional: extract links
    "maxContentLength": 5000           // optional: content limit
  }
}
```

**Success Response Format:**
```
{
  "success": true,
  "data": {
    "url": "https://www.example.com",
    "metadata": {
      "title": "Page title",
      "description": "Meta description",
      "keywords": ["keyword1", "keyword2"],
      "author": "Author name",
      "publishDate": "2024-01-30"
    },
    "content": {
      "headings": {
        "h1": ["Main heading"],
        "h2": ["Subheading 1", "Subheading 2"],
        "h3": ["Section 1", "Section 2"]
      },
      "mainText": "Extracted main content...",
      "wordCount": 1250,
      "paragraphCount": 15
    },
    "links": {
      "internal": 25,
      "external": 10,
      "samples": ["https://example.com/page1", "..."]
    },
    "analysis": {
      "summary": "AI-generated summary...",
      "keyPoints": ["Point 1", "Point 2", "Point 3"],
      "topics": ["Topic 1", "Topic 2"],
      "sentiment": "neutral",  // positive, neutral, negative
      "contentType": "corporate",  // blog, news, e-commerce, etc.
      "insights": "Detailed AI analysis..."
    },
    "scrapedAt": "2024-01-30T10:30:00.000Z",
    "processingTime": 3.5  // seconds
  }
}
```

**Error Response Format:**
```
{
  "success": false,
  "error": {
    "message": "Failed to scrape URL",
    "code": "SCRAPING_FAILED",
    "details": "Connection timeout after 10 seconds",
    "timestamp": "2024-01-30T10:30:00.000Z"
  }
}
```

#### **2. Health Check Endpoint**
```
GET /api/health

Response:
{
  "status": "ok",
  "services": {
    "scraper": "operational",
    "llm": "operational"
  },
  "timestamp": "2024-01-30T10:30:00.000Z"
}
```

### **Web Scraping Architecture**

#### **Scraper Service Requirements**

**URL Validation Layer:**
- Validate URL format using URL parsing libraries
- Check protocol (only allow http and https)
- Block private IP addresses (localhost, 127.0.0.1, 192.168.x.x, 10.x.x.x)
- Block internal network addresses and cloud metadata endpoints
- Implement domain whitelist/blacklist capability
- Validate URL length and structure
- Sanitize and normalize URLs

**HTTP Request Layer:**
- Use Axios with proper timeout configuration (10-15 seconds)
- Set appropriate User-Agent headers
- Follow redirects with limit (max 5 redirects)
- Handle different response types (HTML, XML, JSON)
- Implement retry logic for transient failures
- Handle large responses (streaming or size limits)
- Support custom headers if needed

**Content Extraction Layer (Cheerio):**
Your scraping service should extract:
- Page title (from `<title>` tag)
- Meta description and keywords
- All heading levels (h1, h2, h3, h4, h5, h6)
- Main content from `<main>`, `<article>`, or primary content containers
- Paragraph text from `<p>` tags
- List items from `<ul>` and `<ol>` tags
- Internal and external links
- Image alt texts (optional)
- Structured data if present (JSON-LD, microdata)

**Content Filtering Strategy:**
Elements to EXCLUDE during scraping:
- Navigation menus (`<nav>`, class contains "nav", "menu")
- Headers and footers (`<header>`, `<footer>`)
- Sidebars and widgets (class contains "sidebar", "widget")
- Advertisements (class/id contains "ad", "banner", "sponsored")
- Comments sections
- Cookie notices and popups
- Social media share buttons
- Related content suggestions
- JavaScript and CSS code blocks

**Content Cleaning Pipeline:**
- Remove all HTML tags and keep only text
- Normalize whitespace (multiple spaces → single space)
- Remove special characters and encoding artifacts
- Trim leading/trailing whitespace
- Remove empty lines and excessive line breaks
- Decode HTML entities (&amp; → &, etc.)
- Handle different character encodings (UTF-8, ISO-8859-1)

**Content Size Management:**
- Limit total content to 5000-10000 characters for LLM processing
- Implement intelligent truncation (preserve important sections)
- Prioritize title, headings, and first paragraphs
- Create content summary if exceeding limits
- Track original content length vs. processed length

### **Content Processing Architecture**

#### **Text Processing Requirements**

**Content Structuring:**
Your processor should organize content into:
- Metadata section (title, description, author, date)
- Hierarchical headings structure
- Main body text with paragraph separation
- Lists and bullet points (preserved structure)
- Link inventory (categorized by type)
- Statistics (word count, paragraph count, reading time)

**Text Analysis Preparation:**
- Identify key sections and their importance
- Extract potential keywords and phrases
- Calculate readability metrics (optional)
- Detect content language
- Identify content patterns (lists, tables, code blocks)

**Content Quality Assessment:**
- Verify minimum content length (avoid empty pages)
- Check content coherence and structure
- Detect placeholder or template text
- Identify auto-generated content
- Flag potential scraping issues

### **LLM Integration Architecture**

#### **Analysis Service Requirements**

**Prompt Engineering Strategy:**
Your prompts should:
- Provide clear context about the source URL
- Specify desired analysis format and structure
- Request specific insights (summary, key points, topics)
- Set appropriate response length limits
- Include examples of good analysis (optional)
- Handle different content types appropriately

**Analysis Types:**

**Comprehensive Analysis (default):**
- Overall summary (2-3 sentences)
- Key points and highlights (3-5 bullet points)
- Main topics and themes
- Content type identification
- Target audience assessment
- Content quality evaluation
- Recommendations or insights

**SEO Analysis (stretch goal):**
- Keyword analysis and density
- Meta tag evaluation
- Content structure assessment
- Readability score
- Mobile-friendliness indicators
- Link analysis

**Competitive Analysis (stretch goal):**
- Company/product positioning
- Value propositions identified
- Target market analysis
- Competitive advantages noted
- Content strategy insights

**LLM API Integration:**
- Use appropriate model (GPT-4, GPT-3.5-turbo, or Gemini)
- Set reasonable token limits (1000-1500 for responses)
- Implement timeout handling (30-60 seconds)
- Handle rate limiting with retry logic
- Cache results for identical URLs (optional)
- Track API usage and costs
- Provide fallback for API failures

**Response Processing:**
- Parse LLM response into structured format
- Validate response completeness
- Extract key sections (summary, points, topics)
- Handle incomplete or malformed responses
- Format text for frontend display
- Add confidence scores if available

### **Security Requirements (Critical for Web Scraping)**

#### **SSRF (Server-Side Request Forgery) Prevention**

**URL Validation (Must Implement):**
- Parse URL using built-in URL parser
- Reject non-HTTP/HTTPS protocols (file://, ftp://, etc.)
- Block private IP address ranges:
  - 127.0.0.0/8 (localhost)
  - 10.0.0.0/8 (private network)
  - 172.16.0.0/12 (private network)
  - 192.168.0.0/16 (private network)
  - 169.254.0.0/16 (link-local)
- Block cloud metadata endpoints:
  - 169.254.169.254 (AWS, Azure, GCP)
  - metadata.google.internal
- Block localhost variations (localhost, 0.0.0.0, [::1])
- Validate domain doesn't resolve to private IP
- Implement domain whitelist for production (optional)

**Request Security:**
- Set request timeouts (10-15 seconds max)
- Limit response size (5-10 MB max)
- Limit redirect following (5 max redirects)
- Disable automatic cookie handling
- Set appropriate User-Agent headers
- Don't pass user credentials in requests

**Content Security:**
- Sanitize all extracted content before processing
- Don't execute any scripts from scraped pages
- Validate content types (accept only text/html)
- Scan for malicious patterns in content
- Don't store raw HTML in database

#### **Input Validation**

**URL Input Validation:**
- Validate URL format and structure
- Check URL length (max 2048 characters)
- Verify protocol is http or https
- Ensure domain is properly formed
- Reject URLs with unusual characters
- Normalize URLs before processing
- Prevent path traversal attempts

**Request Body Validation:**
- Validate JSON structure
- Check required fields are present
- Validate option values against allowed types
- Reject requests with excessive data
- Sanitize all string inputs
- Validate numeric ranges

**Rate Limiting:**
- Limit requests per IP address (10-20 per minute)
- Limit requests per API key if using authentication
- Implement cooldown periods for repeated failures
- Return appropriate HTTP 429 responses
- Provide retry-after headers

#### **Error Handling Security**

**Secure Error Messages:**
- Don't expose internal file paths
- Don't reveal server configuration details
- Don't show stack traces in production
- Use generic messages for security failures
- Log detailed errors server-side only
- Don't reveal valid vs. invalid URLs

**Error Response Structure:**
- Consistent error format
- Appropriate HTTP status codes
- User-friendly error messages
- Error codes for categorization
- Timestamp for tracking
- No sensitive information exposed

### **Frontend Requirements**

#### **Component Architecture**

**UrlInput Component:**
- Text input field with URL validation
- Real-time format validation (visual feedback)
- Submit button with loading state
- Clear/reset functionality
- Example URLs for testing (optional)
- Keyboard shortcuts (Enter to submit)
- Validation error messages
- Responsive design for mobile

**AnalysisReport Component:**
- Structured display of analysis results
- Expandable/collapsible sections
- Metadata display (title, description, stats)
- Content preview with truncation
- Analysis insights prominently displayed
- Copy-to-clipboard functionality (optional)
- Export button (JSON download)
- Share functionality (optional)

**ReportSection Component:**
- Reusable section component
- Expandable/collapsible behavior
- Icon indicators for section type
- Loading skeletons during data fetch
- Styled headers and content areas
- Support for different content types (text, lists, tables)

**LoadingIndicator Component:**
- Multi-phase progress indicator:
  1. Fetching URL
  2. Processing content
  3. Analyzing with AI
  4. Generating report
- Animated loading states
- Progress percentage (optional)
- Estimated time remaining (optional)
- Cancel functionality (stretch goal)

**ErrorDisplay Component:**
- Error message display with severity levels
- Error code and description
- Suggested actions for resolution
- Retry button for transient errors
- Close/dismiss functionality
- Different styles for warning vs. error
- Link to documentation for common errors

#### **State Management**

**Application State:**
Your React app should manage:
- Current URL input value
- Loading states (idle, fetching, processing, analyzing, complete, error)
- Analysis results data
- Error states and messages
- Form validation states
- UI preferences (expanded sections, etc.)

**State Updates:**
- Handle asynchronous operations properly
- Update loading states at each phase
- Clear previous results before new analysis
- Persist analysis history in local storage (optional)
- Handle race conditions for multiple requests

#### **API Communication**

**Service Layer Pattern:**
- Centralize all API calls in service module
- Use Axios with proper configuration
- Implement request/response interceptors
- Handle errors consistently
- Transform responses for component consumption
- Add request timeout handling
- Implement retry logic for failed requests

**Request Handling:**
- Show loading state immediately
- Disable submit during processing
- Handle network errors gracefully
- Parse and validate response data
- Update UI based on response status
- Clear loading state on completion

### **UI/UX Requirements**

#### **Visual Design**

**Layout Structure:**
- Single-page application with clear sections
- Prominent URL input at top
- Analysis results below input
- Sticky header with app title
- Footer with links and information
- Responsive grid layout
- Proper spacing and padding

**Color Scheme:**
- Primary brand colors (define in Tailwind config)
- Success green for completed analysis
- Warning yellow for validation issues
- Error red for failures
- Neutral grays for secondary content
- High contrast for accessibility

**Typography:**
- Clear heading hierarchy (h1-h6)
- Readable font sizes (16px minimum for body)
- Appropriate line heights (1.5-1.6 for body)
- Monospace font for URLs and technical data
- Bold emphasis for key points
- Proper text alignment

#### **User Experience**

**Loading States:**
- Immediate visual feedback on submit
- Progress indication for long operations
- Clear phase labels (fetching, processing, analyzing)
- Disable input during processing
- Option to cancel long operations (stretch goal)

**Error Handling:**
- Clear, actionable error messages
- Specific guidance for different error types:
  - Invalid URL format → "Please enter a valid URL"
  - Scraping failed → "Could not access website. It may be blocking automated requests"
  - LLM error → "Analysis failed. Please try again"
  - Network error → "Check your internet connection"
- Retry buttons for recoverable errors
- Link to help documentation

**Success States:**
- Smooth transition to results display
- Highlight key information
- Scroll to results automatically
- Success confirmation message
- Option to analyze another URL
- Export/share options readily available

**Responsive Design:**
- Mobile-first approach
- Breakpoints for tablet and desktop
- Touch-friendly buttons and inputs (44px min)
- Readable text on all screen sizes
- Collapsible sections for mobile
- Proper viewport configuration

### **Performance Considerations**

#### **Backend Optimization**

**Scraping Performance:**
- Set appropriate request timeouts
- Parallel processing for multiple elements (when applicable)
- Limit concurrent scraping requests
- Cache frequently accessed URLs (optional)
- Stream large responses when possible
- Optimize regex patterns and text processing
- Minimize memory allocations

**Content Processing:**
- Efficient string operations
- Limit unnecessary iterations
- Use streaming for large content
- Implement early termination for size limits
- Optimize Cheerio selectors
- Batch similar operations

**LLM Integration:**
- Optimize prompt length
- Cache identical requests (optional)
- Implement request queueing for rate limits
- Use appropriate model for task complexity
- Monitor token usage
- Implement timeout handling

#### **Frontend Optimization**

**Component Performance:**
- Use React.memo for expensive components
- Implement virtual scrolling for long lists (stretch goal)
- Debounce URL input validation
- Lazy load large result sections
- Minimize re-renders with proper state management
- Use keys properly in lists

**Network Optimization:**
- Show cached results immediately if available
- Implement optimistic UI updates
- Compress API responses (gzip)
- Minimize payload size
- Use proper HTTP caching headers

**User Experience Performance:**
- Target < 30 seconds for complete analysis
- Show progress updates every 2-3 seconds
- Immediate feedback on user actions
- Smooth animations and transitions
- No layout shifts during loading

### **Testing Strategy**

#### **Backend Testing**

**Unit Tests:**
- URL validation functions
- Content extraction logic
- Text cleaning utilities
- SSRF prevention mechanisms
- Error handling edge cases

**Integration Tests:**
- Full scraping pipeline
- LLM integration
- API endpoint behavior
- Error scenarios
- Different website structures

**Manual Testing:**
- Test with diverse website types:
  - Corporate sites
  - Blogs
  - News sites
  - E-commerce platforms
  - Educational institutions
- Test error scenarios:
  - Invalid URLs
  - Non-existent domains
  - Timeout scenarios
  - Private IP attempts
  - Redirect loops
- Test edge cases:
  - Very large pages
  - Empty pages
  - Pages with minimal content
  - JavaScript-heavy sites
  - Different character encodings

#### **Frontend Testing**

**Component Testing:**
- Test URL input validation
- Test loading state transitions
- Test error display behavior
- Test report rendering
- Test responsive behavior

**User Flow Testing:**
- Complete analysis flow
- Error recovery flow
- Multiple consecutive analyses
- Browser compatibility
- Mobile device testing

**Accessibility Testing:**
- Keyboard navigation
- Screen reader compatibility
- Color contrast ratios
- Focus indicators
- ARIA labels

### **Environment Variables**

#### **Server (.env)**
Required environment variables for backend:
- `PORT`: Server port number (e.g., 5000)
- `NODE_ENV`: Environment (development/production)
- `OPENAI_API_KEY`: Your OpenAI API key (if using OpenAI)
- `GEMINI_API_KEY`: Your Gemini API key (if using Gemini)
- `CORS_ORIGIN`: Allowed frontend origin (e.g., http://localhost:5173)
- `MAX_CONTENT_LENGTH`: Maximum content size in characters (e.g., 10000)
- `REQUEST_TIMEOUT`: Scraping request timeout in milliseconds (e.g., 15000)
- `RATE_LIMIT_WINDOW`: Rate limit window in minutes (e.g., 15)
- `RATE_LIMIT_MAX`: Maximum requests per window (e.g., 100)

Never commit actual API keys to git!

#### **Client (.env)**
Required environment variables for frontend:
- `VITE_API_BASE_URL`: Backend API URL (e.g., http://localhost:5000)
- `VITE_MAX_URL_LENGTH`: Maximum URL input length (e.g., 2048)

Vite requires environment variables to be prefixed with `VITE_` to be exposed to the client.

#### **.env.example Files**
Create `.env.example` files in both client and server directories with:
- All required variable names
- Placeholder values (not real keys)
- Comments explaining each variable
- Instructions for obtaining API keys

### **Documentation Requirements**

#### **README.md Structure**

**Project Overview:**
- Brief description of the web content analyzer
- Technologies used (MERN stack)
- Key features implemented
- Use cases and benefits

**Prerequisites:**
- Node.js version requirement (18+)
- Required accounts (OpenAI/Gemini)
- Other dependencies
- System requirements

**Installation Instructions:**
- Clone repository command
- Server setup steps with all commands
- Client setup steps with all commands
- Environment variable configuration
- Running the application (both client and server)
- Accessing the application

**Project Structure:**
- Directory tree overview
- Description of key folders
- Explanation of main files
- Architecture diagram (optional)

**API Documentation:**
- List of available endpoints
- Request formats with examples
- Response formats with examples
- Error codes and meanings
- Rate limiting information

**Usage Guide:**
- How to analyze a website
- Understanding the analysis report
- Export functionality
- Troubleshooting common issues
- Best practices for URL selection

**Testing Websites:**
- List of recommended test websites
- Different website types to test
- Known limitations
- Expected results

**Security Considerations:**
- SSRF prevention measures
- Rate limiting policies
- Data privacy information
- Allowed/blocked domains

**Environment Variables:**
- Complete list of required variables
- Instructions for obtaining API keys
- Configuration examples
- Security best practices

**Deployment Guide:**
- Prerequisites for deployment
- Backend deployment (Render/Railway)
- Frontend deployment (Vercel/Netlify)
- Environment variable configuration in production
- Post-deployment testing

**Troubleshooting:**
- Common issues and solutions
- Error message explanations
- Debug mode instructions
- Support resources

**Future Enhancements:**
- List of stretch goals
- Potential improvements
- Ideas for expansion
- Contribution guidelines

#### **ANALYSIS_SAMPLES.md**

This document should include:
- At least 5 different website analyses
- Screenshots of the application
- Various website types (corporate, blog, news, e-commerce, educational)
- Both successful and failed analysis examples
- Analysis quality demonstrations
- Processing time examples

#### **Technical_Learnings.md**

Document your learning journey:
- What you learned about web scraping with Node.js
- Challenges with content extraction and cleaning
- LLM prompt engineering insights
- SSRF prevention implementation
- Performance optimization techniques
- MERN stack integration patterns
- Time spent on each milestone
- Key takeaways and skills developed
- Areas for future improvement

#### **Code Documentation**

**JSDoc Comments:**
- Add JSDoc for all functions
- Document parameters and return types
- Include usage examples for complex functions
- Explain algorithm choices

**Inline Comments:**
- Explain complex logic
- Document security measures
- Note important edge cases
- Reference external resources

**Configuration Comments:**
- Explain environment variables
- Document rate limits and thresholds
- Note security configurations
- Reference relevant documentation

## **Deliverables**

You must submit the following to complete this project:

1. **GitHub Repository Link** (public or invite @mentor)
   - Complete source code for both client and server
   - Comprehensive README.md
   - ANALYSIS_SAMPLES.md with diverse examples
   - Technical_Learnings.md
   - Proper .gitignore configuration
   - Clean commit history

2. **Live Demo** (choose one):
   - Local demo during review session with 3+ different website analyses
   - Deployed URL (backend + frontend)
   - Screen recording demonstrating functionality

3. **ANALYSIS_SAMPLES.md** - Include:
   - At least 5 sample analyses of different website types:
     - Corporate site (e.g., microsoft.com)
     - Blog/News site (e.g., techcrunch.com, medium.com)
     - E-commerce site (e.g., shopify.com)
     - Educational site (e.g., coursera.org)
     - Product/documentation site
   - Screenshots of the application interface
   - Processing time for each analysis
   - Any scraping challenges encountered
   - Quality assessment of LLM analysis

4. **Technical_Learnings.md** - Document:
   - Web scraping learnings and challenges
   - Content processing insights
   - LLM integration experience
   - Security implementation (SSRF prevention)
   - Performance optimization approaches
   - Time spent on each milestone
   - Key technical decisions and rationale

## **Evaluation Rubric (100 Points)**

| Criterion | Points | Details |
| ----- | :---: | ----- |
| **Web Scraping** | 30 pts | ✓ Successfully extracts content from various sites (12 pts) <br> ✓ Handles different website structures (8 pts) <br> ✓ Proper error handling for failed scrapes (5 pts) <br> ✓ Content cleaning and filtering (5 pts) |
| **LLM Analysis** | 25 pts | ✓ Generates meaningful, structured analysis (10 pts) <br> ✓ Good prompt engineering (8 pts) <br> ✓ Handles content size limits (4 pts) <br> ✓ Error handling for LLM failures (3 pts) |
| **API Design** | 20 pts | ✓ Clean endpoint structure (6 pts) <br> ✓ Proper request/response format (6 pts) <br> ✓ Error handling and validation (5 pts) <br> ✓ Security measures (SSRF prevention) (3 pts) |
| **Frontend** | 15 pts | ✓ User-friendly interface (6 pts) <br> ✓ Good UX during processing (5 pts) <br> ✓ Clear display of results (4 pts) |
| **Code Quality** | 10 pts | ✓ Clean, organized code structure (4 pts) <br> ✓ Proper project structure (3 pts) <br> ✓ Good documentation (3 pts) |

### **Minimum Passing Requirements**
- Total score ≥ 70/100
- Web Scraping score ≥ 20/30
- Security measures implemented (SSRF prevention)
- All review requirements passed
- Working demo with 3+ different website types

## **Quick Start Resources**

### **MERN Stack & Web Scraping**
- **Cheerio Documentation**: https://cheerio.js.org/
- **Axios Documentation**: https://axios-http.com/docs/intro
- **Web Scraping Guide**: https://www.freecodecamp.org/news/web-scraping-in-nodejs/
- **Express.js Guide**: https://expressjs.com/en/guide/routing.html

### **Content Processing**
- **HTML Parsing with Cheerio**: https://cheerio.js.org/docs/basics/loading
- **Text Processing in Node.js**: https://nodejs.dev/learn/manipulating-strings-in-nodejs
- **Regular Expressions**: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions

### **Security Resources**
- **SSRF Prevention**: https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html
- **Input Validation**: https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html
- **Helmet.js Security**: https://helmetjs.github.io/

### **LLM API Documentation**
- **OpenAI API**: https://platform.openai.com/docs/api-reference
- **OpenAI Node.js SDK**: https://github.com/openai/openai-node
- **Google Gemini API**: https://ai.google.dev/gemini-api/docs
- **Prompt Engineering Guide**: https://platform.openai.com/docs/guides/prompt-engineering

### **Frontend Resources**
- **React Documentation**: https://react.dev/
- **Tailwind CSS**: https://tailwindcss.com/docs
- **Vite Guide**: https://vitejs.dev/guide/

### **Deployment Resources**
- **Render**: https://render.com/docs (Backend deployment)
- **Railway**: https://docs.railway.app/ (Alternative backend)
- **Vercel**: https://vercel.com/docs (Frontend deployment)
- **Netlify**: https://docs.netlify.com/ (Alternative frontend)

## **FAQ**

**Q: What if a website blocks scraping?**  
A: Handle gracefully with appropriate error messages. Try setting a proper User-Agent header. Some sites have terms of service against scraping - respect those. Focus on publicly accessible sites that allow automated access.

**Q: Should I handle all website types?**  
A: Focus on standard HTML sites for core requirements. JavaScript-heavy sites requiring browser rendering (Puppeteer) are a stretch goal. Aim for 80%+ success rate on typical websites.

**Q: How detailed should the analysis be?**  
A: Aim for 200-500 words, structured with clear sections (summary, key points, topics, insights). Quality over quantity - make it informative and actionable.

**Q: Can I use Puppeteer instead of Cheerio?**  
A: For basic requirements, use Axios + Cheerio (faster, lighter). Puppeteer is a stretch goal for JavaScript-heavy sites. Start simple, then enhance.

**Q: How do I prevent SSRF attacks?**  
A: Validate URLs thoroughly, block private IP ranges, block cloud metadata endpoints, set request timeouts, limit redirects. See Security Requirements section for details.

**Q: What's a reasonable processing time?**  
A: Target < 30 seconds total for standard websites. Scraping (5-10s) + Content Processing (2-5s) + LLM Analysis (10-15s) = ~20-30 seconds.

**Q: Should I cache analysis results?**  
A: Optional but recommended as a stretch goal. Cache identical URLs to save API costs and improve performance. Use Redis or in-memory cache with TTL.

**Q: How do I handle very large websites?**  
A: Implement content size limits (5000-10000 chars), intelligent truncation prioritizing important content (title, headings, first paragraphs), and provide a summary.

**Q: Can I analyze multiple URLs at once?**  
A: Batch processing is a stretch goal. Core requirement is single URL analysis. If implementing, use queue system and process sequentially to avoid rate limits.

**Q: Which LLM API should I use?**  
A: Either OpenAI (GPT-4/3.5-turbo) or Google Gemini. OpenAI is recommended for better analysis quality. Gemini has higher free tier limits.

**Q: How do I test if my SSRF prevention works?**  
A: Try accessing private IPs (http://127.0.0.1, http://192.168.1.1), localhost, cloud metadata (http://169.254.169.254), and verify they're blocked.

**Q: Should I use TypeScript?**  
A: Optional but adds complexity. Project can be completed in JavaScript. TypeScript is valuable for larger projects but not required here.

## **Testing Websites**

Test your analyzer with these different types of websites:

### **Corporate Websites**
- https://www.microsoft.com
- https://www.apple.com
- https://www.google.com

### **E-commerce Platforms**
- https://www.shopify.com
- https://stripe.com
- https://www.etsy.com

### **News & Media**
- https://www.bbc.com
- https://techcrunch.com
- https://arstechnica.com
- https://www.theverge.com

### **Blogs & Content**
- https://medium.com
- https://dev.to
- https://css-tricks.com

### **Educational**
- https://www.coursera.org
- https://www.edx.org
- https://www.khanacademy.org

### **Technical Documentation**
- https://nodejs.org
- https://reactjs.org
- https://developer.mozilla.org

**Note**: Some sites may have anti-scraping measures. Test with a variety and document success rates.

## **Measurable Goals & Review Template Compliance**

### **Primary Objectives (Must Complete for Project Advancement)**
- [ ] **Web Scraping Excellence**: 80%+ success rate on diverse website scraping
- [ ] **Security Compliance**: Pass Security Review with 9/10+ score (SSRF prevention critical)
- [ ] **AI Integration Quality**: Pass AI Integration Review with 8.5/10+ score
- [ ] **Performance Standards**: Process websites within 30 seconds, handle large content efficiently
- [ ] **Code Quality Standards**: Pass Code Quality Review with 8/10+ score
- [ ] **Architecture Soundness**: Pass Architecture Review with 8/10+ score

### **Review Template Integration (All Must Pass)**

#### **Security Review Requirements (Critical for Web Scraping)**
```
security_criteria = {
    "ssrf_prevention": {"target": 100, "weight": 35},          # Server-Side Request Forgery prevention
    "input_validation": {"target": 95, "weight": 25},          # URL validation and sanitization
    "content_sanitization": {"target": 90, "weight": 20},      # Safe content processing
    "error_handling": {"target": 85, "weight": 15},            # Secure error messages
    "dependency_security": {"target": 90, "weight": 5}         # Secure libraries and packages
}
```

#### **AI Integration Review Requirements**
```
ai_integration_criteria = {
    "content_preprocessing": {"target": 90, "weight": 30},     # Content cleaning and optimization
    "prompt_engineering": {"target": 85, "weight": 25},        # Effective analysis prompts
    "analysis_quality": {"target": 85, "weight": 20},          # Meaningful insights generation
    "cost_efficiency": {"target": 80, "weight": 15},           # Token optimization
    "error_handling": {"target": 85, "weight": 10}             # AI service failure management
}
```

#### **Performance Review Requirements**
```
performance_criteria = {
    "scraping_speed": {"target": 85, "weight": 30},            # Efficient content extraction
    "memory_management": {"target": 80, "weight": 25},         # Large content handling
    "response_time": {"target": 85, "weight": 20},             # End-to-end processing time
    "resource_optimization": {"target": 75, "weight": 15},     # CPU and memory efficiency
    "concurrent_handling": {"target": 70, "weight": 10}        # Multiple request support
}
```

#### **Code Quality Review Requirements**
```
code_quality_criteria = {
    "readability": {"target": 85, "weight": 25},               # Clear naming and structure
    "organization": {"target": 85, "weight": 25},              # Proper separation of concerns
    "documentation": {"target": 80, "weight": 20},             # README, comments, JSDoc
    "maintainability": {"target": 80, "weight": 20},           # DRY, clean patterns
    "testing": {"target": 75, "weight": 10}                    # Testing approach
}
```

#### **Architecture Review Requirements**
```
architecture_criteria = {
    "design_patterns": {"target": 80, "weight": 30},           # Clean architecture principles
    "scalability": {"target": 75, "weight": 25},               # Future-ready design
    "modularity": {"target": 85, "weight": 25},                # Proper module separation
    "integration": {"target": 80, "weight": 20}                # Clean component integration
}
```

### **Performance Standards**
- **Scraping Success Rate**: 80%+ on diverse websites (news, blogs, corporate sites)
- **Processing Time**: Average < 30 seconds for standard web pages
- **Content Quality**: Extract meaningful content while filtering noise
- **Memory Efficiency**: Handle large websites without memory issues
- **Error Rate**: < 10% failed scraping attempts due to code issues
- **LLM Analysis Quality**: Meaningful, structured, actionable insights

## **Common Pitfalls to Avoid**

### **Security Issues**
- ❌ Not implementing SSRF prevention (critical failure)
- ❌ Not validating URLs before scraping
- ❌ Not blocking private IP addresses
- ❌ Not setting request timeouts
- ❌ Exposing internal errors to users
- ❌ Not sanitizing scraped content
- ❌ Storing sensitive data in git

### **Scraping Issues**
- ❌ Not handling different HTML structures
- ❌ Including navigation/footer in content
- ❌ Not filtering ads and boilerplate
- ❌ Not handling encoding issues
- ❌ Not setting User-Agent headers
- ❌ Not handling redirects properly
- ❌ Not limiting content size

### **Content Processing Issues**
- ❌ Not cleaning HTML entities
- ❌ Including empty or whitespace-only content
- ❌ Not preserving content structure
- ❌ Sending too much content to LLM (token limits)
- ❌ Not handling special characters
- ❌ Losing important content during truncation

### **LLM Integration Issues**
- ❌ Poor prompt engineering (vague instructions)
- ❌ Not handling API errors gracefully
- ❌ Not setting token limits
- ❌ Not handling rate limiting
- ❌ Sending unstructured content
- ❌ Not validating LLM responses

### **Frontend Issues**
- ❌ No loading states during processing
- ❌ Not showing progress indicators
- ❌ Poor error message display
- ❌ Not disabling input during processing
- ❌ Not handling long-running operations
- ❌ Not making results readable and scannable

## **Success Checklist**

Before submitting your project, verify:

### **Functionality**
- [ ] User can input a URL and receive analysis
- [ ] Successfully scrapes 4+ different website types
- [ ] Generates meaningful LLM analysis
- [ ] Displays structured, readable results
- [ ] Handles errors gracefully with user feedback
- [ ] Shows loading states and progress
- [ ] Works on both desktop and mobile

### **Security**
- [ ] SSRF prevention implemented and tested
- [ ] Private IP addresses blocked
- [ ] Cloud metadata endpoints blocked
- [ ] URL validation working correctly
- [ ] Content sanitization in place
- [ ] No secrets in git repository
- [ ] Secure error messages (no internal details exposed)

### **Performance**
- [ ] Average processing time < 30 seconds
- [ ] Handles large websites without crashing
- [ ] Memory efficient content processing
- [ ] Appropriate timeouts configured
- [ ] No performance bottlenecks identified

### **Code Quality**
- [ ] Clean separation of concerns (scraper/processor/llm)
- [ ] Proper error handling throughout
- [ ] Consistent code style
- [ ] Well-documented with JSDoc
- [ ] No code duplication
- [ ] Meaningful variable and function names

### **Documentation**
- [ ] Comprehensive README with setup instructions
- [ ] ANALYSIS_SAMPLES.md with 5+ examples
- [ ] Technical_Learnings.md completed
- [ ] Environment variables documented
- [ ] API endpoints documented
- [ ] Security measures explained

### **Testing**
- [ ] Tested with diverse website types
- [ ] Tested error scenarios
- [ ] Tested SSRF prevention
- [ ] Tested on different browsers
- [ ] Tested responsive design
- [ ] Tested with slow/large websites

## **Mentor Review Process**

### **Review Stages**

**Stage 1: Initial Code Review (After Milestone 1)**
- Web scraping implementation review
- Security assessment (SSRF prevention)
- Content extraction quality
- Error handling evaluation
- Feedback provided within 24-48 hours

**Stage 2: Functionality Review (After Milestone 2)**
- LLM integration assessment
- Prompt engineering evaluation
- End-to-end testing with various sites
- Performance assessment
- UX evaluation

**Stage 3: Final Review (After Milestone 3)**
- Complete security audit
- Architecture review
- Documentation review
- Production readiness check
- Final scoring and feedback

### **Review Response Time**
- Code reviews: 24-48 hours
- Questions/blockers: Same day (business hours)
- Final evaluation: 48-72 hours

### **Feedback Format**
- Written feedback document
- Scored rubric with detailed breakdown
- Specific improvement suggestions
- Examples of good patterns
- Security vulnerability report (if any)
- Performance recommendations
- Resources for improvement

## **Post-Project Learning Path**

After completing this project successfully, consider these next steps:

### **Immediate Enhancements**
1. Add MongoDB for analysis history persistence
2. Implement caching with Redis
3. Add Puppeteer for JavaScript-heavy sites
4. Implement batch URL processing
5. Add PDF export functionality

### **Skill Building**
1. Advanced web scraping techniques (dynamic content, AJAX)
2. Natural Language Processing (NLP) basics
3. Data visualization with Chart.js or D3.js
4. Advanced prompt engineering
5. Microservices architecture patterns

### **Next Projects**
1. SEO analysis and audit tool
2. Competitive intelligence platform
3. Content aggregation and summarization service
4. Social media sentiment analyzer
5. Research assistant with web scraping

## **Additional Resources**

### **GitHub Copilot Tips for Web Scraping**
- Describe the HTML structure you want to target
- Use comments to explain scraping logic
- Ask for specific Cheerio selectors
- Request error handling for edge cases
- Generate test cases for different HTML structures

### **Debugging Resources**
- Browser DevTools for analyzing HTML structure
- Node.js debugger for backend
- Postman for testing API endpoints
- Console.log for scraping pipeline stages
- Network tab for analyzing website requests

### **Community Support**
- Stack Overflow for web scraping questions
- Reddit r/webscraping for discussions
- GitHub issues for library-specific problems
- Discord communities for MERN developers

---

**Remember**: Web scraping must be done responsibly. Respect robots.txt, terms of service, and rate limits. Focus on publicly accessible content and implement proper security measures. Build something ethical and useful!