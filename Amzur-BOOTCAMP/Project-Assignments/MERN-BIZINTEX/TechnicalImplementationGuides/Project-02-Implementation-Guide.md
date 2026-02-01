# **Project 02 Implementation Guide: Web Content Analyzer**

## **🌐 AI-Assisted Development Prompts for MERN Web Scraping & Analysis**

---

## **📋 Project Overview**

This guide provides specific prompts for building a Web Content Analyzer using the MERN stack with AI assistance. This is a **standalone project** that focuses on web scraping, content processing, and AI-powered analysis.

**Goal:** Extract content from any website URL and generate comprehensive analysis reports using LLM.

---

## **🏗️ Phase 1: Project Setup & Structure**

### **1.1 Initial Project Structure**

```
Create folder structure for MERN web content analyzer:

client/
├── src/
│   ├── components/
│   │   ├── Analysis/
│   │   ├── Scraping/
│   │   ├── Common/
│   │   └── Auth/
│   ├── services/
│   ├── context/
│   ├── utils/
│   ├── hooks/
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
├── utils/
├── scrapers/
└── server.js

Include .env files, .gitignore, README.md, and documentation folder
```

### **1.2 Package Dependencies**

```
Create package.json for Express.js server with dependencies for:
- Express.js 4.x for REST API
- Mongoose for MongoDB connection
- Cheerio for HTML parsing and web scraping
- Axios for HTTP requests to target websites
- Puppeteer for dynamic content scraping (optional)
- OpenAI SDK for content analysis
- JWT for authentication
- express-validator for input validation
- rate-limiter-flexible for scraping rate limits
- bull (Redis queue) for background scraping jobs
- CORS, Helmet for security
- dotenv for environment variables

Include scripts for dev, start, worker (for queue processing)
```

```
Create package.json for React client with:
- React 18+ with functional components
- Vite 5+ for build tooling
- Tailwind CSS + Headless UI for styling
- Axios for HTTP requests
- React Router for navigation
- React Hook Form for form handling
- React Query for server state management
- Recharts for data visualization
- React Markdown for displaying analysis
- React Syntax Highlighter for code display

Include scripts for dev, build, preview, lint
```

---

## **🗄️ Phase 2: Backend Development**

### **2.1 Database Setup & Models**

```
Create MongoDB connection setup using Mongoose that:
- Connects to MongoDB Atlas with retry logic
- Handles connection pooling for multiple requests
- Implements connection monitoring and logging
- Sets up proper indexes for performance
- Exports connection with error handling
```

```
Create Mongoose schema for User model with:
- username: String (required, unique, 3-30 characters)
- email: String (required, unique, valid email format)
- password: String (required, hashed with bcrypt)
- role: String (enum: 'user', 'admin', default: 'user')
- subscription: String (enum: 'free', 'pro', default: 'free')
- analysisCount: Number (default: 0, for rate limiting)
- createdAt, updatedAt: timestamps

Include validation, indexes, and usage tracking methods
```

```
Create Mongoose schema for WebsiteAnalysis model with:
- userId: ObjectId (required, references User)
- url: String (required, validated URL)
- title: String (extracted from webpage)
- content: Object with {text, images, links, metadata}
- analysis: Object with {summary, keywords, sentiment, topics, insights}
- metrics: Object with {wordCount, readingTime, complexity}
- status: String (enum: 'pending', 'processing', 'completed', 'failed')
- error: String (optional, for failed analyses)
- processingTime: Number (milliseconds)
- createdAt, updatedAt: timestamps

Include indexes for efficient querying and full-text search
```

```
Create Mongoose schema for ScrapingJob model with:
- userId: ObjectId (required, references User)
- url: String (required, target URL)
- status: String (enum: 'queued', 'processing', 'completed', 'failed')
- priority: Number (default: 0, for job ordering)
- attempts: Number (default: 0, for retry logic)
- result: ObjectId (optional, references WebsiteAnalysis)
- error: String (optional, error details)
- scheduledAt: Date (for delayed processing)
- createdAt, updatedAt: timestamps

Include methods for job management and retry logic
```

### **2.2 Web Scraping Service**

```
Create web scraping service using Cheerio that:
- Accepts URL and scraping options
- Validates URL format and accessibility
- Handles different content types (HTML, XML, etc.)
- Extracts structured content: title, headings, paragraphs, images, links
- Respects robots.txt and meta tags
- Implements rate limiting per domain
- Handles errors gracefully (timeouts, 404s, etc.)
- Returns standardized content object
- Logs scraping activities for debugging
```

```
Create content extraction utilities that:
- Extract main content using readability algorithms
- Remove navigation, ads, and boilerplate content
- Parse meta tags for SEO information
- Extract images with alt text and captions
- Collect internal and external links
- Identify content language and encoding
- Calculate content metrics (word count, reading time)
- Handle different HTML structures gracefully
```

```
Create advanced scraping service with Puppeteer for:
- Dynamic content that requires JavaScript execution
- SPA (Single Page Application) content
- Content behind authentication (where permitted)
- Screenshots of webpage for visual analysis
- Performance metrics (load time, page size)
- Mobile vs desktop content comparison
- Handling of infinite scroll and lazy loading
- Cookie consent and popup handling
```

### **2.3 Content Analysis Service**

```
Create OpenAI analysis service that:
- Takes extracted content as input
- Generates comprehensive content analysis including:
  * Executive summary (2-3 sentences)
  * Key topics and themes identification
  * Content quality assessment
  * SEO analysis and recommendations
  * Target audience identification
  * Content structure evaluation
  * Readability score and suggestions
- Implements prompt templates for different analysis types
- Handles API rate limits and retries
- Manages token usage efficiently
- Returns structured analysis results
```

```
Create sentiment analysis service that:
- Analyzes overall content sentiment (positive, negative, neutral)
- Identifies emotional tone and language style
- Detects bias or subjective language
- Provides sentiment scores with confidence levels
- Handles different content types (news, blogs, product pages)
- Returns actionable insights for content improvement
```

```
Create keyword and topic extraction service that:
- Extracts important keywords and phrases
- Identifies main topics and subtopics
- Calculates keyword density and frequency
- Suggests related keywords for SEO
- Categorizes content by industry or domain
- Provides topic hierarchy and relationships
- Generates tag suggestions for content organization
```

### **2.4 API Controllers**

```
Create scraping controller with endpoints:

POST /api/scrape/analyze
- Validates URL format and accessibility
- Checks user rate limits and permissions
- Queues scraping job for background processing
- Returns job ID for status tracking
- Handles duplicate URL submissions
- Implements security checks for malicious URLs

GET /api/scrape/status/:jobId
- Returns current status of scraping job
- Provides progress updates and ETA
- Returns partial results if available
- Handles job not found errors

GET /api/scrape/result/:jobId
- Returns complete analysis results
- Ensures user owns the analysis
- Formats data for frontend consumption
- Implements result caching for performance
```

```
Create analysis controller with endpoints:

GET /api/analysis/history
- Returns user's analysis history with pagination
- Includes filters by date, status, domain
- Provides search functionality across analyses
- Returns summary statistics and usage metrics

GET /api/analysis/:id
- Returns detailed analysis by ID
- Validates user ownership
- Includes related metadata and metrics
- Formats content for display

DELETE /api/analysis/:id
- Soft deletes analysis record
- Validates user ownership and permissions
- Updates user's analysis count
- Handles cascading deletions if needed

POST /api/analysis/:id/export
- Exports analysis in multiple formats (PDF, JSON, CSV)
- Generates shareable links with expiration
- Handles large datasets efficiently
- Includes formatting options
```

### **2.5 Background Job Processing**

```
Create Redis-based job queue system using Bull that:
- Manages scraping and analysis jobs
- Implements job priorities and delays
- Handles job retries with exponential backoff
- Provides job progress tracking
- Implements job concurrency limits
- Monitors queue health and performance
- Handles failed job cleanup
- Provides dashboard for job monitoring
```

```
Create job worker processes that:
- Process scraping jobs asynchronously
- Handle multiple concurrent jobs safely
- Update job status in real-time
- Implement proper error handling and logging
- Monitor resource usage (memory, CPU)
- Handle graceful shutdowns
- Implement job timeout handling
- Scale based on queue depth
```

---

## **⚛️ Phase 3: Frontend Development**

### **3.1 State Management & Context**

```
Create React Context for Application State that:
- Manages user authentication and profile
- Handles global loading states
- Manages theme and preferences
- Provides error handling and notifications
- Handles real-time updates from server
- Manages navigation and routing state
```

```
Create React Query setup for server state that:
- Configures cache policies for different data types
- Implements optimistic updates
- Handles background refetching
- Manages loading and error states
- Implements infinite queries for large datasets
- Provides offline support where appropriate
```

### **3.2 URL Analysis Components**

```
Create React component URLSubmissionForm that:
- Accepts URL input with validation
- Provides URL format suggestions and examples
- Shows real-time validation feedback
- Handles form submission with loading states
- Implements rate limiting UI feedback
- Provides batch URL submission option
- Uses React Hook Form for validation
- Includes accessibility features (ARIA labels)
```

```
Create React component AnalysisProgress that:
- Shows current analysis status and progress
- Displays estimated time remaining
- Provides cancel option for long-running jobs
- Shows queue position if applicable
- Updates in real-time using polling or WebSockets
- Handles connection errors gracefully
- Provides detailed progress breakdown
```

```
Create React component QuickAnalysis that:
- Provides one-click analysis for common websites
- Shows popular domains and suggested URLs
- Implements URL history and favorites
- Provides analysis templates for different content types
- Shows user's recent analyses for re-analysis
- Implements sharing functionality for results
```

### **3.3 Results Display Components**

```
Create React component AnalysisResults that:
- Displays comprehensive analysis in organized sections
- Implements tabbed interface for different analysis types
- Shows summary cards with key metrics
- Provides print and export functionality
- Implements responsive design for all devices
- Handles large content with pagination or virtualization
- Includes social sharing buttons
- Provides comparison with previous analyses
```

```
Create React component ContentPreview that:
- Shows extracted content with formatting preserved
- Displays images, links, and multimedia content
- Implements text highlighting for keywords
- Provides content outline and navigation
- Shows content structure (headings, paragraphs)
- Implements search within content
- Handles different content types appropriately
```

```
Create React component AnalysisCharts that:
- Visualizes sentiment analysis with charts
- Shows keyword frequency and importance
- Displays readability scores and metrics
- Implements interactive data exploration
- Provides comparison charts between analyses
- Shows trends and patterns in data
- Uses Recharts for responsive visualizations
- Implements export functionality for charts
```

### **3.4 Analysis Management**

```
Create React component AnalysisHistory that:
- Lists all user analyses with filtering and sorting
- Implements search across analysis titles and content
- Provides bulk operations (delete, export, share)
- Shows analysis statistics and trends
- Implements infinite scrolling for large datasets
- Provides analysis comparison functionality
- Includes favorite and bookmark features
```

```
Create React component AnalysisComparison that:
- Allows side-by-side comparison of analyses
- Highlights differences in content and metrics
- Shows trend analysis over time
- Implements diff visualization for content changes
- Provides export of comparison reports
- Handles multiple analysis selection
```

---

## **🔧 Phase 4: Advanced Features & Optimization**

### **4.1 Content Processing Enhancement**

```
Create content categorization service that:
- Automatically categorizes content by industry/topic
- Uses machine learning for classification
- Maintains category confidence scores
- Provides category suggestions for manual tagging
- Implements custom category creation
- Handles multi-category content appropriately
```

```
Create duplicate content detection that:
- Identifies similar or duplicate content across analyses
- Calculates content similarity scores
- Groups related analyses automatically
- Provides plagiarism detection capabilities
- Implements content clustering algorithms
- Suggests original sources for content
```

### **4.2 Performance & Scalability**

```
Implement caching strategies that:
- Cache analysis results for frequently requested URLs
- Implement Redis caching for expensive operations
- Use CDN for static assets and images
- Implement browser caching for API responses
- Cache user sessions and preferences
- Implement cache invalidation strategies
```

```
Optimize database performance with:
- Proper indexing for all query patterns
- Database query optimization and explain plans
- Connection pooling and connection management
- Read replicas for analytics queries
- Database monitoring and alerting
- Automated backup and recovery procedures
```

### **4.3 Security & Monitoring**

```
Implement security measures that:
- Validate and sanitize all URLs before processing
- Implement rate limiting per user and IP
- Block malicious domains and URLs
- Sanitize extracted content to prevent XSS
- Implement CSRF protection for state changes
- Monitor for abuse and suspicious activity
- Implement IP whitelisting for admin operations
```

```
Create monitoring and analytics that:
- Track application performance metrics
- Monitor scraping success rates and errors
- Analyze user behavior and usage patterns
- Implement error tracking and alerting
- Monitor resource usage (CPU, memory, disk)
- Track API usage and rate limit violations
- Generate usage reports and insights
```

---

## **🧪 Phase 5: Testing & Quality Assurance**

### **5.1 Backend Testing**

```
Create comprehensive Jest tests for:
- Web scraping functions with mock HTML responses
- Content analysis service with sample content
- API endpoints with various input scenarios
- Database models and operations
- Authentication and authorization flows
- Error handling and edge cases
- Performance testing for large content
- Integration tests for complete workflows
```

### **5.2 Frontend Testing**

```
Create React Testing Library tests for:
- URL submission form validation and submission
- Analysis results display and interaction
- Error handling and loading states
- Component integration and data flow
- User authentication and protected routes
- Responsive design across device sizes
- Accessibility compliance (ARIA, keyboard navigation)
```

### **5.3 End-to-End Testing**

```
Create Cypress E2E tests for:
- Complete analysis workflow from URL to results
- User registration and authentication flow
- Analysis history and management features
- Error scenarios and recovery procedures
- Performance testing with realistic data loads
- Cross-browser compatibility testing
```

---

## **🚀 Phase 6: Deployment & Production**

### **6.1 Environment Configuration**

```
Create production environment setup with:
- MongoDB Atlas cluster with proper security
- Redis instance for job queue and caching
- Environment variables for all secrets and configs
- Load balancer configuration for high availability
- CDN setup for static assets
- Backup and disaster recovery procedures
- Monitoring and logging infrastructure
```

### **6.2 Deployment Pipeline**

```
Create deployment configuration for:
- Docker containers for application components
- Kubernetes manifests for orchestration (optional)
- CI/CD pipeline with GitHub Actions
- Automated testing before deployment
- Blue-green deployment strategy
- Database migration procedures
- Health checks and monitoring setup
```

---

## **📝 Success Checklist**

### **Backend Complete ✅**
- [ ] Web scraping with Cheerio working for various sites
- [ ] OpenAI content analysis generating quality insights
- [ ] Background job processing with Redis queues
- [ ] Rate limiting and security measures implemented
- [ ] Comprehensive error handling and logging
- [ ] Database optimization and indexing complete
- [ ] API documentation and testing complete

### **Frontend Complete ✅**
- [ ] Intuitive URL submission and validation
- [ ] Real-time analysis progress tracking
- [ ] Comprehensive results visualization
- [ ] Analysis history and management features
- [ ] Responsive design for all devices
- [ ] Performance optimization for large datasets
- [ ] Accessibility compliance achieved

### **Production Ready ✅**
- [ ] Security audit completed and issues resolved
- [ ] Performance testing with realistic loads
- [ ] Monitoring and alerting configured
- [ ] Backup and recovery procedures tested
- [ ] Documentation complete and up-to-date
- [ ] Deployment pipeline automated and tested

---

## **💡 Troubleshooting Common Issues**

### **Web Scraping Issues**
```
Handle scraping problems with:
- User-agent rotation for blocked requests
- Proxy rotation for IP-based blocking
- Retry logic with exponential backoff
- Captcha detection and handling
- JavaScript rendering for dynamic content
- Cookie and session management
```

### **Performance Issues**
```
Optimize performance with:
- Content chunking for large webpages
- Parallel processing of multiple URLs
- Database connection pooling
- Memory management for large content
- Queue optimization and monitoring
- CDN for static content delivery
```

### **Analysis Quality Issues**
```
Improve analysis quality with:
- Content cleaning and preprocessing
- Context preservation in analysis prompts
- Multi-model analysis for better accuracy
- Human validation for edge cases
- Continuous prompt optimization
- Feedback collection and improvement
```

---

**Next:** [Project 03 Implementation Guide](Project-03-Implementation-Guide.md)
