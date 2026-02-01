# **Project 6: Image-Generating Chatbot (MERN Stack)**

## **Objective (Why?)**

Build an intelligent image-generating chatbot that creates high-quality images from natural language descriptions in 4 days. This project introduces multimodal AI capabilities to your existing chat platform from Project 5. You will practice:

* **Multimodal AI Integration**: Combining text processing with image generation APIs
* **Direct API Communication**: Simple request-response pattern with DALL-E 3 API
* **Image Storage & Management**: Storing generated images in MongoDB (GridFS or Base64)
* **Real-time UI Feedback**: Loading states and immediate image display
* **Chat Enhancement**: Seamlessly integrate image generation into Project 5 chat system

## **Project Progression from Project 5**

### **What You're Building Upon (From Project 5):**
- ✅ **Chat System**: Multi-threaded conversations with message history
- ✅ **Authentication**: JWT-based auth from Project 4
- ✅ **File Handling**: File upload and processing capabilities
- ✅ **Database Foundation**: MongoDB with chat threads and messages
- ✅ **React UI**: Modern chat interface with file attachments

### **What You're Adding in Project 6:**
- 🆕 **Image Generation**: DALL-E 3 API integration for text-to-image
- 🆕 **Image Storage**: MongoDB GridFS or Base64 storage with metadata
- 🆕 **Gallery System**: Image organization, search, and download
- 🆕 **Enhanced Chat UI**: Image display within conversations
- 🆕 **Creative Tools**: Prompt assistance and generation history
- 🆕 **Cost Management**: Rate limiting and usage tracking

## **Technology Stack (MERN + Image AI)**

### **Backend Extensions (From Project 5)**
- **Existing**: Express.js, MongoDB, Mongoose, JWT, Multer
- **New AI Integration**:
  - OpenAI SDK (DALL-E 3 API)
  - Image processing libraries (sharp - optional for optimization)
- **New Storage**:
  - MongoDB GridFS (for large images) OR
  - Base64 storage in documents (simpler, for moderate usage)
  - Image metadata management

### **Frontend Extensions (From Project 5)**
- **Existing**: React, Vite, Tailwind CSS, Axios
- **New Components**:
  - Image generation input
  - Loading/progress indicators
  - Image gallery grid
  - Image preview and download
  - Prompt suggestion UI

## **Core Requirements (Must-have)**

| Component | Requirement |
| ----- | ----- |
| **Image Generation** | Integrate OpenAI DALL·E 3 for direct text-to-image conversion with immediate response |
| **Simple Processing** | Direct API calls to DALL-E with loading states - no background processing needed |
| **Database Storage** | Store generated images in MongoDB (GridFS or Base64) with metadata |
| **Chat Integration** | Seamlessly integrate image generation into existing chat platform from Project 5 |
| **Gallery Management** | Store, organize, and retrieve generated images with search and download functionality |
| **Role-Based Access** | Use existing RBAC from Project 4 for image generation permissions (optional: admin-only or all users) |

## **Project Structure (Extending Project 5)**

```
mern-image-chat/  (extends mern-intelligent-chat from Project 5)
├── client/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Chat/              # From Project 5
│   │   │   ├── ImageGeneration/   # NEW
│   │   │   │   ├── ImagePromptInput.jsx
│   │   │   │   ├── ImageLoadingState.jsx
│   │   │   │   ├── GeneratedImage.jsx
│   │   │   │   ├── ImageGallery.jsx
│   │   │   │   ├── ImageCard.jsx
│   │   │   │   └── PromptSuggestions.jsx
│   │   │   └── Gallery/           # NEW
│   │   │       ├── GalleryGrid.jsx
│   │   │       ├── ImagePreview.jsx
│   │   │       ├── ImageFilters.jsx
│   │   │       └── DownloadButton.jsx
│   │   ├── services/
│   │   │   ├── chatService.js     # From Project 5
│   │   │   └── imageService.js    # NEW
│   │   └── pages/
│   │       ├── ChatPage.jsx       # From Project 5 (enhanced)
│   │       └── GalleryPage.jsx    # NEW
│
├── server/
│   ├── models/
│   │   ├── User.js               # From Project 4
│   │   ├── ChatThread.js         # From Project 5
│   │   ├── Message.js            # From Project 5
│   │   └── GeneratedImage.js     # NEW
│   ├── controllers/
│   │   ├── chatController.js     # From Project 5
│   │   └── imageController.js    # NEW
│   ├── routes/
│   │   ├── chatRoutes.js         # From Project 5
│   │   └── imageRoutes.js        # NEW
│   ├── services/
│   │   ├── ragService.js         # From Project 5
│   │   └── imageGenerator.js     # NEW - DALL-E integration
│   ├── middleware/
│   │   ├── auth.js               # From Project 4
│   │   └── rateLimiter.js        # From Project 4 (enhanced)
│   └── utils/
│       └── imageStorage.js       # NEW - GridFS or Base64 helpers
```

## **Development Approach: Milestone-Based Progression**

### **Pre-requisites (Must Complete First)**
- [ ] **Project 5 Completed**: Chat system with file analysis working
- [ ] **Project 4 Auth Working**: Authentication and RBAC functional
- [ ] **MongoDB Accessible**: Database with existing chat collections
- [ ] **OpenAI Account**: API key for DALL-E 3 access

### **Milestone 1: Direct Image Generation & Storage**

#### **Deliverables:**
- [ ] DALL-E 3 API integration with secure key management
- [ ] Direct API call implementation with proper error handling
- [ ] MongoDB schema for image metadata
- [ ] Image storage implementation (GridFS or Base64)
- [ ] Basic image generation endpoint with loading states
- [ ] Testing with sample prompts

#### **Review Requirements:**
- [ ] **Security Review**: API key security, input validation, rate limiting
- [ ] **Architecture Review**: Clean direct API integration
- [ ] **Performance Review**: Efficient image generation and storage

### **Milestone 2: Chat Integration & UI Enhancement**

#### **Deliverables:**
- [ ] Enhanced chat interface with image generation commands
- [ ] Loading states and progress indicators during API calls
- [ ] Image display components within chat
- [ ] Integration with existing Project 5 chat system
- [ ] Error handling and user feedback
- [ ] Basic image gallery view

#### **Review Requirements:**
- [ ] **AI Integration Review**: Seamless image generation within conversations
- [ ] **Architecture Review**: Clean chat and image generation integration
- [ ] **Performance Review**: Responsive UI during API calls

### **Milestone 3: Gallery & Management Features**

#### **Deliverables:**
- [ ] Complete image gallery with grid view
- [ ] Search and filter functionality
- [ ] Image download capability
- [ ] Generation history tracking
- [ ] Image metadata display
- [ ] Delete/manage images

#### **Review Requirements:**
- [ ] **User Experience Review**: Intuitive gallery interface
- [ ] **Performance Review**: Fast gallery loading and search
- [ ] **Code Quality Review**: Clean component architecture

### **Milestone 4: Production Features & Optimization**

#### **Deliverables:**
- [ ] Advanced prompt assistance and suggestions
- [ ] Rate limiting and cost management
- [ ] Usage tracking and analytics
- [ ] Performance optimization and caching
- [ ] Comprehensive testing
- [ ] Production deployment preparation

#### **Review Requirements:**
- [ ] **Final Security Review**: Production security assessment
- [ ] **AI Integration Review**: Creative AI workflow optimization
- [ ] **Performance Review**: All performance targets met
- [ ] **Code Quality Review**: Production-ready code

## **Database Schema Extensions (MongoDB + Mongoose)**

### **GeneratedImage Model (NEW)**

**Required Fields:**
Your GeneratedImage schema should include:

**Core Fields:**
- `userId`: ObjectId (reference to User from Project 4)
- `threadId`: ObjectId (reference to ChatThread from Project 5, optional)
- `messageId`: ObjectId (reference to Message from Project 5, optional)
- `prompt`: String (user's original prompt, required)
- `revisedPrompt`: String (DALL-E's revised prompt, optional)
- `imageData`: String (Base64 encoded image) OR GridFS reference
- `imageUrl`: String (DALL-E temporary URL, for reference)
- `status`: String (enum: 'generating', 'completed', 'failed')

**Metadata:**
- `size`: String (e.g., "1024x1024")
- `quality`: String (e.g., "standard" or "hd")
- `style`: String (e.g., "vivid" or "natural")
- `model`: String (e.g., "dall-e-3")
- `fileSize`: Number (in bytes)
- `mimeType`: String (e.g., "image/png")

**Tracking:**
- `generationTime`: Number (milliseconds)
- `cost`: Number (API cost in dollars, optional)
- `downloads`: Number (download count)
- `views`: Number (view count)
- `isPublic`: Boolean (for sharing, optional)

**Timestamps:**
- `createdAt`: Date
- `updatedAt`: Date

**Indexes:**
- userId (for user's images lookup)
- threadId (for chat-related images)
- status (for filtering)
- createdAt (for sorting)
- Compound: userId + createdAt (for user's timeline)

**Schema Methods:**
- `getImageData()`: Return Base64 data URI or GridFS stream
- `incrementViews()`: Track image views
- `incrementDownloads()`: Track downloads
- `toPublicJSON()`: Return safe public data without sensitive info

## **Backend API Structure (Extending Project 5)**

### **Image Generation Endpoints (NEW)**

**1. Generate Image**
```
POST /api/images/generate
Authorization: Bearer {token}
Content-Type: application/json

Request:
{
  "prompt": "A serene mountain landscape at sunset",
  "threadId": "optional_thread_id",
  "options": {
    "size": "1024x1024",      // "1024x1024", "1792x1024", "1024x1792"
    "quality": "standard",     // "standard" or "hd"
    "style": "vivid"           // "vivid" or "natural"
  }
}

Success Response (200):
{
  "success": true,
  "data": {
    "image": {
      "_id": "...",
      "imageData": "data:image/png;base64,...",  // Base64 data URI
      "prompt": "A serene mountain landscape at sunset",
      "revisedPrompt": "A breathtaking mountain landscape...",
      "metadata": {
        "size": "1024x1024",
        "quality": "standard",
        "generationTime": 8500,
        "fileSize": 1048576
      },
      "createdAt": "2024-01-30T10:30:00.000Z"
    }
  },
  "message": "Image generated successfully"
}

Error Response (429 - Rate Limit):
{
  "success": false,
  "error": "Rate limit exceeded. Please try again in 60 seconds.",
  "retryAfter": 60
}

Error Response (500 - Generation Failed):
{
  "success": false,
  "error": "Image generation failed: Content policy violation",
  "code": "CONTENT_POLICY_VIOLATION"
}
```

**2. Get User's Generated Images**
```
GET /api/images
Authorization: Bearer {token}
Query: ?page=1&limit=20&threadId=...&sort=createdAt

Success Response (200):
{
  "success": true,
  "data": {
    "images": [
      {
        "_id": "...",
        "prompt": "A serene mountain landscape",
        "thumbnail": "data:image/png;base64,...",  // Smaller version
        "metadata": {
          "size": "1024x1024",
          "views": 5,
          "downloads": 2
        },
        "createdAt": "2024-01-30T10:30:00.000Z"
      }
    ],
    "pagination": {
      "total": 50,
      "page": 1,
      "pages": 3
    }
  }
}
```

**3. Get Single Image**
```
GET /api/images/:imageId
Authorization: Bearer {token}

Success Response (200):
{
  "success": true,
  "data": {
    "image": {
      "_id": "...",
      "imageData": "data:image/png;base64,...",
      "prompt": "A serene mountain landscape at sunset",
      "revisedPrompt": "...",
      "metadata": { ... },
      "views": 10,
      "downloads": 3,
      "createdAt": "2024-01-30T10:30:00.000Z"
    }
  }
}
```

**4. Download Image**
```
GET /api/images/:imageId/download
Authorization: Bearer {token}

Success Response:
- Content-Type: image/png
- Content-Disposition: attachment; filename="generated_image_123.png"
- Binary image data
```

**5. Delete Image**
```
DELETE /api/images/:imageId
Authorization: Bearer {token}

Success Response (200):
{
  "success": true,
  "message": "Image deleted successfully"
}
```

**6. Get Generation History**
```
GET /api/images/history
Authorization: Bearer {token}
Query: ?startDate=2024-01-01&endDate=2024-01-31

Success Response (200):
{
  "success": true,
  "data": {
    "stats": {
      "totalGenerated": 25,
      "totalCost": 0.50,
      "averageGenerationTime": 9200
    },
    "history": [
      {
        "date": "2024-01-30",
        "count": 5,
        "prompts": ["..."]
      }
    ]
  }
}
```

## **DALL-E 3 API Integration**

### **Image Generation Requirements**

**API Configuration:**
- Use OpenAI's DALL-E 3 model
- Endpoint: https://api.openai.com/v1/images/generations
- Authentication: Bearer token with OpenAI API key
- Rate limiting: Respect OpenAI's limits

**Request Parameters:**

**Required:**
- `model`: "dall-e-3"
- `prompt`: String (max 4000 characters)
- `n`: 1 (DALL-E 3 only supports n=1)

**Optional:**
- `size`: "1024x1024" (default), "1792x1024", or "1024x1792"
- `quality`: "standard" (default) or "hd"
- `style`: "vivid" (default) or "natural"
- `response_format`: "url" (get temporary URL) or "b64_json" (get Base64)
- `user`: User identifier (for abuse monitoring)

**Response Format:**
- `created`: Timestamp
- `data`: Array with single image object
  - `url`: Temporary image URL (expires in 1 hour) OR
  - `b64_json`: Base64 encoded image
  - `revised_prompt`: DALL-E's interpretation of prompt

### **Image Generation Service Architecture**

**Your image generator service should:**

**1. Validate Input:**
- Check prompt length (max 4000 chars)
- Sanitize prompt for offensive content (optional pre-check)
- Validate size, quality, style parameters
- Check user's rate limit status

**2. Call DALL-E API:**
- Use OpenAI SDK for Node.js
- Set appropriate timeout (30-60 seconds)
- Include user identifier for tracking
- Handle API errors gracefully

**3. Process Response:**
- If using URL response:
  - Download image from temporary URL
  - Convert to Base64 or store in GridFS
- If using b64_json response:
  - Directly store Base64 data
- Save revised prompt for reference

**4. Store in Database:**
- Create GeneratedImage document
- Store image data (Base64 or GridFS reference)
- Save all metadata
- Associate with user and thread

**5. Return to Client:**
- Format response with image data
- Include metadata and timestamps
- Provide download URL

### **Error Handling**

**Handle these DALL-E API errors:**

**400 Bad Request:**
- Invalid prompt (too long, empty)
- Invalid parameters (unsupported size, etc.)
- Return user-friendly error message

**401 Unauthorized:**
- Invalid API key
- Log error, return generic message to user

**429 Rate Limit:**
- Exceeded requests per minute
- Return retry-after time
- Implement client-side backoff

**500 Server Error:**
- DALL-E API internal error
- Retry with exponential backoff (2-3 attempts)
- If fails, return error to user

**Content Policy Violation:**
- Prompt violates OpenAI's usage policies
- Return clear error explaining issue
- Log for monitoring

### **Image Storage Strategies**

**Option A: Base64 in MongoDB (Recommended for Moderate Usage)**

**Advantages:**
- Simpler implementation
- No additional setup
- Easy to retrieve with documents

**Disadvantages:**
- Larger database size
- 16MB document size limit (DALL-E 3 images usually < 2MB)
- Less efficient for very large collections

**Implementation:**
- Store Base64 string directly in GeneratedImage.imageData
- Prefix with data URI (data:image/png;base64,...)
- Can be used directly in <img> tags

**Option B: MongoDB GridFS (Recommended for Heavy Usage)**

**Advantages:**
- Handles large files efficiently
- No document size limit
- Better for streaming
- Cleaner data model

**Disadvantages:**
- More complex setup
- Requires GridFS understanding
- Slightly more code

**Implementation:**
- Use GridFS to store binary image data
- Store GridFS file ID in GeneratedImage document
- Retrieve using GridFS stream
- Serve as downloadable file or convert to Base64

**Recommendation:**
- Start with Base64 for simplicity
- Migrate to GridFS if scaling issues arise
- DALL-E 3 images are typically 1-2MB, well within limits

## **Frontend Implementation**

### **Image Generation Components**

**ImagePromptInput Component:**

**Features Required:**
- Large textarea for prompt input
- Character counter (max 4000)
- Size selection dropdown (1024x1024, 1792x1024, 1024x1792)
- Quality toggle (standard/hd)
- Style toggle (vivid/natural)
- Generate button with loading state
- Prompt suggestions/examples
- Clear button

**ImageLoadingState Component:**

**Features Required:**
- Animated loading spinner or progress indicator
- Status message ("Generating your image...")
- Estimated time remaining (optional)
- Cancel button (optional, may not be supported)
- Progress percentage (if available from API)
- Fun facts or tips during wait

**GeneratedImage Component:**

**Features Required:**
- Large image display
- Original prompt display
- Revised prompt display (expandable)
- Metadata display (size, quality, generation time)
- Action buttons:
  - Download button
  - Copy to clipboard (image or data URI)
  - Share (optional)
  - Delete (with confirmation)
  - Add to gallery
- View counter display

**ImageGallery Component:**

**Features Required:**
- Grid layout (responsive: 1-4 columns)
- Lazy loading for images
- Infinite scroll or pagination
- Filter options:
  - Date range
  - Size
  - Quality
  - Associated thread
- Sort options:
  - Newest first
  - Oldest first
  - Most viewed
  - Most downloaded
- Search by prompt keywords
- Bulk select for delete (optional)

**ImageCard Component:**

**Features Required:**
- Thumbnail image
- Prompt preview (truncated)
- Metadata badges (size, quality)
- View/download count
- Date created
- Hover effects
- Click to enlarge
- Quick actions menu

**PromptSuggestions Component:**

**Features Required:**
- Curated prompt examples by category:
  - Landscapes
  - Portraits
  - Abstract
  - Animals
  - Architecture
  - Fantasy
- Click to use suggestion
- Random suggestion button
- User's recent prompts
- Popular prompts (optional)

### **Chat Integration**

**Enhanced ChatInput (from Project 5):**

**Add Image Generation Trigger:**
- Detect "/generate" command or similar
- Show image generation UI inline
- Or: dedicated "Generate Image" button
- Maintain chat context while generating

**Message Display Enhancement:**

**For Image Messages:**
- Display generated image inline
- Show prompt below image
- Click to enlarge
- Download button
- Include in conversation flow
- Preserve in message history

**Chat Thread Integration:**

**Image Association:**
- Link generated images to chat threads
- Display in conversation timeline
- Allow referencing in subsequent messages
- Maintain context for follow-up requests

### **State Management**

**Image Generation State:**
- Current generation status (idle, generating, completed, error)
- Generated image data
- Error messages
- Generation history
- Gallery images
- Filters and sort preferences

**Use Context API or Redux for:**
- Global image gallery state
- User's generation history
- Active image being viewed
- Filter/sort preferences
- Loading states across components

## **Rate Limiting & Cost Management**

### **Rate Limiting Strategy**

**Implementation:**
- Store generation timestamps per user
- Check limits before API call
- Return 429 error if exceeded
- Show remaining quota to user

**Admin Override:**
- Admins may have higher limits
- Configurable limits per role
- Usage monitoring for all users

### **Cost Tracking**

**DALL-E 3 Pricing (as of 2024):**
- Standard quality: $0.040 per image
- HD quality: $0.080 per image
- Track costs in database

**Implementation:**
- Calculate cost per generation
- Store in GeneratedImage.cost field
- Aggregate for analytics
- Display to admins
- Set budget alerts (optional)

**Budget Management:**
- Set monthly budget limits
- Alert when approaching limit
- Option to restrict usage
- Cost reports for admins

## **Performance Optimization**

### **Image Generation Performance**

**API Calls:**
- Use appropriate timeout (30-60s)
- Implement retry logic for transient failures
- Use response_format: "b64_json" to avoid extra download
- Target: < 30 seconds per generation (API dependent)

**Image Storage:**
- Compress images before storage (optional with sharp)
- Use appropriate image format (PNG default)
- Consider creating thumbnails for gallery
- Target: < 2 seconds for storage

**Image Retrieval:**
- Index database properly
- Paginate gallery results
- Lazy load images
- Use CDN for serving (optional)
- Target: < 2 seconds for gallery load

### **UI Performance**

**Image Display:**
- Use image loading placeholders
- Implement progressive image loading
- Optimize image sizes for display
- Cache images in browser

**Gallery:**
- Virtual scrolling for large collections
- Lazy load images as user scrolls
- Prefetch next page
- Debounce search/filter inputs

## **Security Considerations**

### **API Key Security**

**Protection:**
- Store OpenAI API key in environment variables only
- Never expose to client-side code
- Use server-side API calls only
- Rotate keys periodically
- Monitor API usage for anomalies

### **Content Safety**

**Input Validation:**
- Validate prompt length and content
- Pre-filter offensive prompts (optional)
- Comply with OpenAI's usage policies
- Log flagged prompts for review

**Output Handling:**
- DALL-E 3 has built-in safety systems
- Handle content policy violations
- Provide user feedback on violations
- Don't store violating images

### **Access Control**

**Authentication:**
- Require authentication for all image operations
- Use existing JWT from Project 4
- Verify user owns images before allowing access

**Authorization:**
- Check user permissions before generation
- Enforce rate limits
- Restrict based on roles (if applicable)
- Audit all generation requests

### **Data Privacy**

**User Data:**
- Isolate user's images from others
- Secure image storage
- Option to delete images
- No cross-user access

**Compliance:**
- Follow data retention policies
- Provide data export (download all)
- Honor deletion requests
- GDPR considerations if applicable

## **Testing Scenarios**

### **Image Generation**
- [ ] Generate image with simple prompt
- [ ] Generate image with complex prompt
- [ ] Different sizes work correctly
- [ ] Quality options work correctly
- [ ] Style options work correctly
- [ ] Error handling for invalid prompts
- [ ] Rate limiting works
- [ ] Cost tracking accurate

### **Chat Integration**
- [ ] Generate image from chat
- [ ] Image displays in conversation
- [ ] Image associated with thread
- [ ] Can download from chat
- [ ] Conversation history preserved

### **Gallery**
- [ ] Gallery displays all images
- [ ] Filtering works correctly
- [ ] Sorting works correctly
- [ ] Search finds images
- [ ] Pagination works
- [ ] Download works
- [ ] Delete works

### **Performance**
- [ ] Generation completes in < 30s
- [ ] Gallery loads in < 2s
- [ ] UI stays responsive
- [ ] No memory leaks
- [ ] Image display optimized

## **Common Pitfalls to Avoid**

### **API Integration**
- ❌ Not handling API rate limits
- ❌ Not implementing timeouts
- ❌ Exposing API key to client
- ❌ Not handling content policy violations
- ❌ Ignoring revised prompts

### **Storage**
- ❌ Not checking document size limits
- ❌ Storing too many large images
- ❌ Not indexing properly
- ❌ Not cleaning up failed generations
- ❌ Not compressing images

### **UI/UX**
- ❌ No loading feedback
- ❌ Blocking UI during generation
- ❌ Not showing errors clearly
- ❌ Gallery not responsive
- ❌ Images not lazy loaded

### **Security**
- ❌ Not rate limiting
- ❌ No cost tracking
- ❌ Missing authentication checks
- ❌ Not validating inputs
- ❌ Storing prompts with sensitive info

## **Success Checklist**

### **✅ Image Generation**
- [ ] DALL-E 3 integration working
- [ ] Images generate successfully
- [ ] All size/quality/style options work
- [ ] Error handling comprehensive
- [ ] Loading states clear

### **✅ Storage**
- [ ] Images stored in database
- [ ] Metadata saved correctly
- [ ] Retrieval fast and reliable
- [ ] Storage strategy scalable

### **✅ Chat Integration**
- [ ] Generate from chat works
- [ ] Images display inline
- [ ] Thread association correct
- [ ] History preserved

### **✅ Gallery**
- [ ] All images display
- [ ] Filter/sort working
- [ ] Search functional
- [ ] Download works
- [ ] Responsive design

### **✅ Performance**
- [ ] Generation < 30s
- [ ] Gallery load < 2s
- [ ] UI responsive
- [ ] No lag or freezing

### **✅ Security**
- [ ] API key secure
- [ ] Rate limiting active
- [ ] Auth enforced
- [ ] Cost tracked

## **Deliverables**

1. **GitHub Repository** with integrated platform
2. **Live Demo** showing image generation
3. **DEMO.md** with:
   - Screenshots of image generation flow
   - Gallery screenshots
   - Chat integration examples
   - Various image styles/sizes
4. **Technical_Learnings.md** with:
   - DALL-E integration insights
   - Storage strategy decisions
   - Chat integration challenges
   - Performance optimizations
   - Cost management learnings

## **Evaluation Rubric (100 Points)**

| Criterion | Points | Details |
| ----- | :---: | ----- |
| **Image Generation** | 35 pts | ✓ DALL-E 3 integration (15 pts) <br> ✓ All options working (10 pts) <br> ✓ Error handling (10 pts) |
| **Storage & Retrieval** | 20 pts | ✓ Database storage (10 pts) <br> ✓ Fast retrieval (10 pts) |
| **Chat Integration** | 20 pts | ✓ Seamless chat integration (12 pts) <br> ✓ Image display (8 pts) |
| **Gallery** | 15 pts | ✓ Gallery UI (8 pts) <br> ✓ Filter/search (7 pts) |
| **Security & Quality** | 10 pts | ✓ Security measures (5 pts) <br> ✓ Code quality (5 pts) |

## **Quick Start Resources**

### **OpenAI DALL-E**
- **DALL-E API**: https://platform.openai.com/docs/guides/images
- **OpenAI Node.js SDK**: https://github.com/openai/openai-node
- **Pricing**: https://openai.com/pricing

### **Image Processing**
- **Sharp**: https://sharp.pixelplumbing.com/ (optional optimization)
- **MongoDB GridFS**: https://www.mongodb.com/docs/manual/core/gridfs/

### **React Image Components**
- **React Image Gallery**: https://www.npmjs.com/package/react-image-gallery
- **React Lazy Load**: https://www.npmjs.com/package/react-lazyload

---

**Remember**: This project teaches you multimodal AI integration - combining text and image generation. Focus on clean integration with your existing chat system and proper image management!