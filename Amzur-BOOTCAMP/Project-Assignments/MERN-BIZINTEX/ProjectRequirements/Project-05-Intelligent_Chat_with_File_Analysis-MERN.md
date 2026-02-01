# **Project 5: Intelligent Chat System with File Analysis (MERN Stack)**

## **Objective (Why?)**

Build an intelligent chat system that extends your Project 4 authentication system with file upload and AI-powered analysis capabilities in 3-4 days. This project transforms your secure user management platform into an interactive AI assistant that can analyze documents and data files. You will practice:

* **Full-Stack Extension**: Building upon your existing Express.js + React + MongoDB architecture from Project 4
* **File Processing**: Handle multiple file formats (PDF, DOCX, TXT, Excel) with secure upload
* **RAG Implementation**: Retrieval-Augmented Generation with vector embeddings and semantic search
* **Vector Database**: Store and query document embeddings for intelligent retrieval
* **Role-Based File Access**: Extend your RBAC system to include file upload permissions
* **Chat Interface**: Build modern chat UI similar to ChatGPT/Claude with file attachment support
* **Data Analysis**: Natural language queries on Excel/CSV data

## **Project Progression from Project 4**

### **What You're Building Upon (From Project 4):**
- ✅ **Authentication System**: User registration, login, JWT tokens, social auth
- ✅ **Authorization System**: Role-based access control (user/admin roles)
- ✅ **Database Foundation**: MongoDB with users, sessions, social accounts
- ✅ **Frontend Foundation**: React + Vite + Tailwind CSS with protected routes
- ✅ **Backend Foundation**: Express.js with secure endpoints and middleware

### **What You're Adding in Project 5:**
- 🆕 **Chat System**: Multi-threaded conversations with message history
- 🆕 **File Upload**: Secure file handling with role-based permissions
- 🆕 **Document Processing**: Text extraction from PDF, DOCX, TXT
- 🆕 **RAG System**: Vector embeddings + semantic search for document Q&A
- 🆕 **Vector Database**: Store and query document chunks with embeddings
- 🆕 **Data Analysis**: Excel/CSV processing with natural language queries
- 🆕 **Enhanced UI**: ChatGPT-like interface with file attachments
- 🆕 **Cross-Modal Intelligence**: Handle both document and data queries

## **Technology Stack (MERN + AI)**

### **Backend Extensions (From Project 4)**
- **Existing**: Express.js, MongoDB, Mongoose, Passport.js, JWT, bcrypt
- **New File Processing**:
  - pdf-parse (PDF text extraction)
  - mammoth (DOCX to text)
  - xlsx (Excel/CSV parsing)
  - multer (file upload handling)
  - file-type (file validation)
- **New AI/ML**:
  - OpenAI SDK (embeddings + completions)
  - LangChain.js (RAG implementation - optional)
  - Pinecone or MongoDB Atlas Vector Search
- **New Data Analysis**:
  - vm2 (safe code execution) OR
  - Direct LLM-based analysis (safer)

### **Frontend Extensions (From Project 4)**
- **Existing**: React 18+, Vite, Tailwind CSS, React Router, Axios
- **New Components**:
  - File upload with drag-and-drop
  - Chat interface with message bubbles
  - Code/chart rendering
  - Multi-file management
- **New Libraries**:
  - react-dropzone (file upload)
  - react-markdown (message formatting)
  - recharts or chart.js (data visualization)
  - react-syntax-highlighter (code display)

### **Database Extensions (From Project 4)**
- **Existing Collections**: users, sessions
- **New Collections**:
  - chatThreads
  - messages
  - uploadedFiles
  - documentChunks (for RAG)
- **Vector Database** (Choose one):
  - MongoDB Atlas Vector Search (integrated)
  - Pinecone (separate service)
  - Qdrant (self-hosted option)

## **Core Requirements (Must-have)**

### **Part 1: Extended Chat System (Building on Project 4)**

| Component | Requirement |
| :---: | ----- |
| **User Authentication** | Use existing Project 4 auth system - JWT tokens, role validation |
| **Chat Management** | Create new chat threads, manage conversations, persistent chat history in MongoDB |
| **File Upload Permissions** | Admin users can upload files (extending existing RBAC from Project 4) |
| **Database Extension** | Add chat/message collections to existing Project 4 MongoDB database |
| **Protected Chat Routes** | Extend existing route protection to include chat features |

### **Part 2: Document Intelligence (RAG System)**

| Component | Requirement |
| :---: | ----- |
| **Document Processing** | Extract text from PDF, DOCX, TXT files using Node.js libraries |
| **Text Chunking** | Split documents into semantic chunks for vector storage |
| **Vector Embeddings** | Generate embeddings using OpenAI's embedding API |
| **Vector Storage** | Store document chunks with embeddings in vector database |
| **Semantic Search** | Retrieve relevant chunks based on query similarity |
| **RAG Responses** | Use retrieved context + LLM to generate accurate answers |
| **Source Attribution** | Cite sources and page numbers in responses |

### **Part 3: Data Analysis Capabilities**

| Component | Requirement |
| :---: | ----- |
| **Excel/CSV Upload** | Parse Excel/CSV files and extract data using xlsx library |
| **Data Exploration** | Allow natural language queries about data |
| **Analysis Generation** | Use LLM to analyze data and answer business questions |
| **Visualization** | Generate charts/graphs for data insights (optional) |
| **Safe Execution** | Secure approach to data analysis (LLM-based recommended) |

## **Project Structure (Extending Project 4)**

```
mern-intelligent-chat/  (extends mern-auth-system from Project 4)
├── client/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Auth/              # From Project 4
│   │   │   ├── Chat/              # NEW
│   │   │   │   ├── ChatWindow.jsx
│   │   │   │   ├── ChatSidebar.jsx    # Thread list
│   │   │   │   ├── MessageList.jsx
│   │   │   │   ├── MessageBubble.jsx
│   │   │   │   ├── ChatInput.jsx      # With file upload
│   │   │   │   └── FileAttachment.jsx
│   │   │   ├── Files/             # NEW
│   │   │   │   ├── FileUpload.jsx
│   │   │   │   ├── FileList.jsx
│   │   │   │   ├── FilePreview.jsx
│   │   │   │   └── FileManager.jsx    # Admin only
│   │   │   └── Visualizations/    # NEW (optional)
│   │   │       ├── ChartRenderer.jsx
│   │   │       └── DataTable.jsx
│   │   ├── services/
│   │   │   ├── authService.js     # From Project 4
│   │   │   ├── chatService.js     # NEW
│   │   │   ├── fileService.js     # NEW
│   │   │   └── analysisService.js # NEW
│   │   └── pages/
│   │       ├── Login.jsx          # From Project 4
│   │       ├── Dashboard.jsx      # From Project 4
│   │       └── ChatPage.jsx       # NEW - Main chat interface
│
├── server/
│   ├── models/
│   │   ├── User.js               # From Project 4
│   │   ├── ChatThread.js         # NEW
│   │   ├── Message.js            # NEW
│   │   ├── UploadedFile.js       # NEW
│   │   └── DocumentChunk.js      # NEW (for RAG)
│   ├── controllers/
│   │   ├── authController.js     # From Project 4
│   │   ├── chatController.js     # NEW
│   │   ├── fileController.js     # NEW
│   │   └── analysisController.js # NEW
│   ├── routes/
│   │   ├── authRoutes.js         # From Project 4
│   │   ├── chatRoutes.js         # NEW
│   │   ├── fileRoutes.js         # NEW
│   │   └── analysisRoutes.js     # NEW
│   ├── middleware/
│   │   ├── auth.js               # From Project 4
│   │   ├── roleCheck.js          # From Project 4
│   │   └── fileUpload.js         # NEW - Multer config
│   ├── services/
│   │   ├── authService.js        # From Project 4
│   │   ├── fileProcessor.js      # NEW - Extract text
│   │   ├── ragService.js         # NEW - RAG implementation
│   │   ├── vectorService.js      # NEW - Vector DB operations
│   │   ├── embeddingService.js   # NEW - Generate embeddings
│   │   └── dataAnalysis.js       # NEW - Excel/CSV analysis
│   ├── utils/
│   │   ├── textChunker.js        # NEW - Chunk documents
│   │   ├── vectorSearch.js       # NEW - Similarity search
│   │   └── fileValidation.js     # NEW - Validate uploads
│   └── uploads/                  # NEW - Temporary file storage
│
└── .env (extends Project 4)
```

## **Development Approach: Milestone-Based Progression**

### **Pre-requisites (Must Complete First)**
- [ ] **Project 4 Completed**: Full authentication/authorization system working
- [ ] **Database Accessible**: MongoDB with existing user/role collections
- [ ] **Backend Running**: Express.js with JWT middleware and RBAC
- [ ] **Frontend Working**: React app with role-based UI and protected routes

### **Milestone 1: Chat System Integration**
**Estimated Time**: 6-8 hours (Day 1)

#### **Deliverables:**
- [ ] Extend MongoDB schema with chat collections (threads, messages)
- [ ] Create chat API endpoints using existing auth middleware
- [ ] Build chat UI components in existing React app
- [ ] Implement role-based chat permissions (all users can chat)
- [ ] Message history persistence and retrieval
- [ ] Thread creation and management

#### **Review Requirements:**
- [ ] **Security Review**: Chat endpoints properly authenticated
- [ ] **Code Quality Review**: Clean chat architecture
- [ ] **Integration Review**: Seamless extension of Project 4

### **Milestone 2: File Upload & Document Processing**
**Estimated Time**: 8-10 hours (Day 2)

#### **Deliverables:**
- [ ] File upload endpoint (admin-only, using existing RBAC)
- [ ] Text extraction from PDF, DOCX, TXT files
- [ ] File validation and security checks
- [ ] File storage in MongoDB GridFS or file system
- [ ] File association with chat messages
- [ ] Enhanced chat interface with file attachment support

#### **Review Requirements:**
- [ ] **Security Review**: File upload security, validation, size limits
- [ ] **Performance Review**: Efficient file processing
- [ ] **Code Quality Review**: Clean file processing architecture

### **Milestone 3: RAG System Implementation**
**Estimated Time**: 10-12 hours (Day 3)

#### **Deliverables:**
- [ ] Document chunking service (split text into semantic chunks)
- [ ] OpenAI embeddings integration
- [ ] Vector database setup (Pinecone or MongoDB Atlas)
- [ ] Vector storage for document chunks
- [ ] Semantic search implementation
- [ ] RAG query pipeline (retrieve + generate)
- [ ] Source attribution in responses

#### **Review Requirements:**
- [ ] **AI Integration Review**: Effective RAG implementation
- [ ] **Performance Review**: Fast semantic search (<2s)
- [ ] **Architecture Review**: Scalable RAG design

### **Milestone 4: Data Analysis (Optional but Recommended)**
**Estimated Time**: 6-8 hours (Day 4)

#### **Deliverables:**
- [ ] Excel/CSV parsing and data extraction
- [ ] Natural language to data analysis with LLM
- [ ] Safe approach to data queries (LLM-based analysis)
- [ ] Data visualization rendering (optional)
- [ ] Cross-modal query handling (document + data)

#### **Review Requirements:**
- [ ] **Security Review**: Safe data analysis approach
- [ ] **AI Integration Review**: Effective NL to analysis
- [ ] **User Experience Review**: Clear data presentation

### **Milestone 5: Production Readiness**
**Estimated Time**: 4-6 hours (Day 4-5)

#### **Deliverables:**
- [ ] File management interface for admins
- [ ] Comprehensive error handling
- [ ] Performance optimization
- [ ] Testing with existing authentication
- [ ] Documentation and deployment

#### **Review Requirements:**
- [ ] **Final Security Review**: Complete security assessment
- [ ] **Performance Review**: All performance targets met
- [ ] **Code Quality Review**: Production-ready code

## **Database Schema Extensions (MongoDB + Mongoose)**

### **ChatThread Model (NEW)**

```javascript
// Extends Project 4 database with chat functionality
{
  userId: ObjectId,           // Reference to User from Project 4
  title: String,              // Thread title (auto-generated or user-set)
  lastMessageAt: Date,        // Last message timestamp
  isActive: Boolean,          // Thread active status
  messageCount: Number,       // Total messages in thread
  createdAt: Date,
  updatedAt: Date
}
```

**Indexes:**
- userId (for user's threads lookup)
- lastMessageAt (for sorting)
- Compound: userId + isActive

### **Message Model (NEW)**

```javascript
{
  threadId: ObjectId,         // Reference to ChatThread
  userId: ObjectId,           // Reference to User
  role: String,               // 'user' or 'assistant'
  content: String,            // Message text
  hasFile: Boolean,           // Whether message has file attachment
  fileId: ObjectId,           // Reference to UploadedFile (if applicable)
  metadata: {                 // Optional metadata
    tokens: Number,           // Token count
    model: String,            // LLM model used
    processingTime: Number    // Response generation time
  },
  createdAt: Date
}
```

**Indexes:**
- threadId (for retrieving thread messages)
- createdAt (for chronological ordering)
- Compound: threadId + createdAt

### **UploadedFile Model (NEW)**

```javascript
{
  userId: ObjectId,           // Reference to User (uploader)
  threadId: ObjectId,         // Reference to ChatThread
  messageId: ObjectId,        // Reference to Message
  filename: String,           // Original filename
  fileType: String,           // MIME type
  fileSize: Number,           // File size in bytes
  filePath: String,           // Storage path or GridFS ID
  processedContent: String,   // Extracted text content
  processingStatus: String,   // 'pending', 'processed', 'failed'
  isVectorized: Boolean,      // Whether embeddings generated
  chunkCount: Number,         // Number of document chunks
  metadata: {                 // File-specific metadata
    pageCount: Number,        // For PDFs
    wordCount: Number,        // For all docs
    language: String          // Detected language
  },
  createdAt: Date,
  updatedAt: Date
}
```

**Indexes:**
- userId (for user's files)
- threadId (for thread files)
- Compound: userId + isVectorized

### **DocumentChunk Model (NEW - for RAG)**

```javascript
{
  fileId: ObjectId,           // Reference to UploadedFile
  chunkIndex: Number,         // Chunk sequence number
  content: String,            // Chunk text content
  embedding: [Number],        // Vector embedding (if using MongoDB Atlas)
  metadata: {
    pageNumber: Number,       // Source page (for PDFs)
    startChar: Number,        // Start position in document
    endChar: Number,          // End position in document
    tokens: Number            // Token count
  },
  createdAt: Date
}
```

**Indexes:**
- fileId (for file's chunks lookup)
- chunkIndex (for ordering)
- If using MongoDB Atlas Vector Search: Vector index on embedding field

## **Backend API Structure (Extending Project 4)**

### **Chat Endpoints (NEW)**

**1. Get User's Chat Threads**
```
GET /api/chat/threads
Authorization: Bearer {token}
Query: ?page=1&limit=20

Success Response (200):
{
  "success": true,
  "data": {
    "threads": [
      {
        "_id": "...",
        "title": "Document Analysis Discussion",
        "lastMessageAt": "2024-01-30T10:30:00.000Z",
        "messageCount": 15,
        "isActive": true
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

**2. Create New Thread**
```
POST /api/chat/threads
Authorization: Bearer {token}
Content-Type: application/json

Request:
{
  "title": "Q3 Sales Analysis"  // Optional
}

Success Response (201):
{
  "success": true,
  "data": {
    "thread": {
      "_id": "...",
      "title": "Q3 Sales Analysis",
      "messageCount": 0,
      "createdAt": "2024-01-30T10:30:00.000Z"
    }
  }
}
```

**3. Get Thread Messages**
```
GET /api/chat/threads/:threadId/messages
Authorization: Bearer {token}
Query: ?limit=50&before=messageId

Success Response (200):
{
  "success": true,
  "data": {
    "messages": [
      {
        "_id": "...",
        "role": "user",
        "content": "What are the key findings in the report?",
        "hasFile": true,
        "fileId": "...",
        "createdAt": "2024-01-30T10:30:00.000Z"
      },
      {
        "_id": "...",
        "role": "assistant",
        "content": "Based on the report, the key findings are...",
        "createdAt": "2024-01-30T10:30:05.000Z"
      }
    ],
    "hasMore": true
  }
}
```

**4. Send Message (Standard Chat)**
```
POST /api/chat/threads/:threadId/messages
Authorization: Bearer {token}
Content-Type: application/json

Request:
{
  "content": "Summarize this document"
}

Success Response (200):
{
  "success": true,
  "data": {
    "userMessage": {
      "_id": "...",
      "role": "user",
      "content": "Summarize this document",
      "createdAt": "2024-01-30T10:30:00.000Z"
    },
    "assistantMessage": {
      "_id": "...",
      "role": "assistant",
      "content": "Here's a summary of the document...",
      "createdAt": "2024-01-30T10:30:05.000Z"
    }
  }
}
```

**5. Send Message with RAG Query**
```
POST /api/chat/threads/:threadId/messages
Authorization: Bearer {token}
Content-Type: application/json

Request:
{
  "content": "What does the contract say about termination?",
  "useRAG": true,
  "fileIds": ["file_id_1", "file_id_2"]  // Optional: specific files
}

Success Response (200):
{
  "success": true,
  "data": {
    "userMessage": {...},
    "assistantMessage": {
      "_id": "...",
      "role": "assistant",
      "content": "According to the contract, termination clauses are...",
      "sources": [
        {
          "fileId": "...",
          "filename": "contract.pdf",
          "chunkIndex": 5,
          "pageNumber": 12,
          "relevanceScore": 0.92
        }
      ],
      "createdAt": "2024-01-30T10:30:05.000Z"
    }
  }
}
```

### **File Endpoints (NEW - Admin Only)**

**1. Upload File**
```
POST /api/files/upload
Authorization: Bearer {token}
Required Role: admin
Content-Type: multipart/form-data

Request:
- file: (binary)
- threadId: (string) - optional

Success Response (201):
{
  "success": true,
  "data": {
    "file": {
      "_id": "...",
      "filename": "contract.pdf",
      "fileType": "application/pdf",
      "fileSize": 1048576,
      "processingStatus": "pending",
      "createdAt": "2024-01-30T10:30:00.000Z"
    }
  },
  "message": "File uploaded successfully. Processing in progress."
}
```

**2. Get File Processing Status**
```
GET /api/files/:fileId/status
Authorization: Bearer {token}

Success Response (200):
{
  "success": true,
  "data": {
    "fileId": "...",
    "processingStatus": "processed",
    "isVectorized": true,
    "chunkCount": 45,
    "metadata": {
      "pageCount": 10,
      "wordCount": 3500
    }
  }
}
```

**3. Get User's Files**
```
GET /api/files
Authorization: Bearer {token}
Query: ?threadId=...&status=processed

Success Response (200):
{
  "success": true,
  "data": {
    "files": [
      {
        "_id": "...",
        "filename": "Q3_Report.pdf",
        "fileType": "application/pdf",
        "fileSize": 2097152,
        "processingStatus": "processed",
        "isVectorized": true,
        "createdAt": "2024-01-30T10:00:00.000Z"
      }
    ]
  }
}
```

**4. Delete File (Admin Only)**
```
DELETE /api/files/:fileId
Authorization: Bearer {token}
Required Role: admin

Success Response (200):
{
  "success": true,
  "message": "File and associated chunks deleted successfully"
}
```

### **Analysis Endpoints (NEW - Optional)**

**1. Analyze Excel/CSV Data**
```
POST /api/analysis/data
Authorization: Bearer {token}
Content-Type: application/json

Request:
{
  "fileId": "...",
  "query": "What were the top 3 products by revenue in Q3?"
}

Success Response (200):
{
  "success": true,
  "data": {
    "analysis": "The top 3 products by revenue in Q3 were:\n1. Product A: $1.2M\n2. Product B: $980K\n3. Product C: $750K",
    "visualization": {
      "type": "bar",
      "data": {...},
      "config": {...}
    }
  }
}
```

## **RAG System Implementation**

### **Document Processing Pipeline**

**Step 1: File Upload & Text Extraction**

Your file processor should:
1. Receive uploaded file (PDF, DOCX, TXT)
2. Validate file type and size
3. Extract text content:
   - PDF: Use pdf-parse library
   - DOCX: Use mammoth library
   - TXT: Use fs.readFile
4. Store extracted text in UploadedFile.processedContent
5. Calculate metadata (word count, page count, etc.)

**Step 2: Text Chunking**

Your chunking service should:
- Split document into semantic chunks (500-1000 tokens each)
- Use sliding window for overlap (50-100 tokens)
- Preserve context boundaries (paragraphs, sections)
- Create DocumentChunk documents with metadata
- Strategies:
  - Character-based chunking (simple)
  - Sentence-based chunking (better)
  - Paragraph-based chunking (best for semantic coherence)

**Step 3: Generate Embeddings**

Your embedding service should:
- Use OpenAI's text-embedding-ada-002 model
- Generate embeddings for each chunk
- Batch process for efficiency (up to 2048 inputs/request)
- Store embeddings in DocumentChunk.embedding field
- Handle API errors and rate limits

**Step 4: Vector Storage**

**Option A: MongoDB Atlas Vector Search**
- Use native MongoDB vector search capability
- Create vector search index on embedding field
- Query using $vectorSearch aggregation stage

**Option B: Pinecone**
- Initialize Pinecone client
- Create index with dimension=1536 (for OpenAI ada-002)
- Upsert vectors with metadata
- Query using similarity search

**Option C: Qdrant (Self-hosted)**
- Setup Qdrant server
- Create collection
- Upload vectors with payloads
- Search using REST API

### **RAG Query Pipeline**

**Step 1: Query Processing**

When user asks a question:
1. Generate embedding for the query (same model as documents)
2. Perform vector similarity search
3. Retrieve top-k most relevant chunks (k=3-5)
4. Extract chunk content and metadata

**Step 2: Context Building**

Build context for LLM:
- Combine retrieved chunks
- Add source information (filename, page number)
- Structure as context + query
- Stay within token limits (e.g., 3000 tokens for context)

**Step 3: LLM Generation**

Send to LLM:
- System prompt: "Answer based on provided context only"
- Context: Retrieved document chunks
- User query: Original question
- Request structured response with citations

**Step 4: Response Formatting**

Format response:
- Extract answer from LLM
- Add source citations
- Include confidence/relevance scores
- Return to user with sources

### **Vector Search Implementation**

**MongoDB Atlas Vector Search:**
```javascript
// Example vector search query structure
const results = await DocumentChunk.aggregate([
  {
    $vectorSearch: {
      queryVector: queryEmbedding,  // [1536 dimensions]
      path: "embedding",
      numCandidates: 100,
      limit: 5,
      index: "vector_index"
    }
  },
  {
    $project: {
      content: 1,
      metadata: 1,
      score: { $meta: "vectorSearchScore" }
    }
  }
]);
```

**Pinecone Vector Search:**
```javascript
// Example Pinecone query
const queryResponse = await index.query({
  vector: queryEmbedding,
  topK: 5,
  includeMetadata: true,
  filter: {
    fileId: { $eq: "specific_file_id" }  // Optional filter
  }
});
```

### **RAG Service Architecture**

Your RAG service should have these methods:

**processDocument(fileId):**
- Extract text from file
- Chunk text into segments
- Generate embeddings for chunks
- Store chunks with embeddings

**queryDocuments(query, options):**
- Generate query embedding
- Search vector database
- Retrieve relevant chunks
- Build context from chunks

**generateResponse(query, context, threadId):**
- Format prompt with context
- Call LLM API
- Parse and format response
- Store messages in database
- Return response with sources

## **File Processing Details**

### **Supported File Types**

**PDF Processing:**
- Library: pdf-parse
- Extract: Text content, page count
- Challenges: Tables, images, formatting
- Solution: Extract text only, preserve structure where possible

**DOCX Processing:**
- Library: mammoth
- Extract: Text content, basic formatting
- Convert: DOCX → HTML → Text
- Preserve: Headings, paragraphs

**TXT Processing:**
- Built-in: fs.readFile
- Encoding: UTF-8
- Simple and straightforward

**Excel/CSV Processing (Optional):**
- Library: xlsx
- Extract: All sheets, data rows
- Convert: Excel → JSON
- Analyze: Natural language queries on data

### **File Upload Security**

**Validation Requirements:**
- File type whitelist: PDF, DOCX, TXT, XLSX, CSV
- Maximum file size: 10MB (configurable)
- Filename sanitization
- MIME type verification
- Virus scanning (optional but recommended)

**Storage Strategy:**
- Temporary upload directory
- Process and delete original
- Store extracted text in database
- Or use MongoDB GridFS for large files

**Access Control:**
- Only admin users can upload
- Users can only access their own files
- Proper file ID validation
- No path traversal vulnerabilities

## **Data Analysis Implementation (Optional)**

### **Approach 1: LLM-Based Analysis (Recommended)**

**How it works:**
1. User uploads Excel/CSV file
2. Parse file and extract data structure
3. User asks natural language question
4. Send data schema + sample + question to LLM
5. LLM generates analysis in natural language
6. Display result to user

**Advantages:**
- No code execution required (safer)
- Simpler implementation
- Works for most business questions

**Limitations:**
- Limited to data that fits in context window
- No direct data manipulation
- Relies on LLM's analytical capabilities

### **Approach 2: Code Generation (Advanced)**

**How it works:**
1. User uploads data file
2. Parse and load data
3. User asks question in natural language
4. LLM generates JavaScript data processing code...
5. Execute code in sandboxed environment
6. Return results and/or visualization

**Advantages:**
- Powerful data manipulation
- Precise calculations
- Can generate charts

**Limitations:**
- Code execution security risks
- More complex implementation
- Requires sandboxing (vm2, containers)

**If implementing code generation:**
- Use vm2 for sandboxed execution
- Whitelist allowed libraries only
- Set strict timeouts
- Limit memory usage
- Validate generated code before execution
- Never execute user-provided code directly

## **Frontend Implementation**

### **Chat Interface Components**

**ChatWindow Component:**
Features needed:
- Message list with auto-scroll
- User/assistant message differentiation
- File attachment indicators
- Loading states for AI responses
- Typing indicators
- Error handling and retry

**ChatSidebar Component:**
Features needed:
- List of chat threads
- Create new thread button
- Thread selection
- Thread search/filter
- Delete thread option
- Active thread highlighting

**MessageBubble Component:**
Features needed:
- Different styles for user vs assistant
- Markdown rendering for formatted text
- Code block syntax highlighting
- Source citations for RAG responses
- Copy message button
- Timestamp display

**ChatInput Component:**
Features needed:
- Text input with auto-resize
- File upload button (admin only)
- Send button with keyboard shortcut
- Drag-and-drop file upload
- File preview before sending
- Character/token count (optional)

**FileAttachment Component:**
Features needed:
- File icon and name
- File size display
- Download/view button
- Processing status indicator
- Remove attachment option

### **File Upload Flow**

**1. User Selects File:**
- Validate file type client-side
- Check file size limits
- Show preview/confirmation

**2. Upload Process:**
- Show upload progress bar
- Display processing status
- Handle errors gracefully
- Update file list on success

**3. Processing Feedback:**
- "Uploading..." (0-10s)
- "Processing document..." (10-30s)
- "Generating embeddings..." (30-45s)
- "Ready for analysis" (complete)

**4. Integration with Chat:**
- Attach file to message
- Send message with file reference
- Display file in conversation
- Enable RAG queries on file

### **State Management**

**Chat State:**
- Current thread ID
- Thread list
- Messages by thread
- Active file uploads
- Processing status
- Error states

**Use Context API for:**
- Global chat state
- File upload progress
- User role (from Project 4 auth)
- Active thread management

## **Environment Variables (Extending Project 4)**

### **Server (.env) - Additional Variables**

```
# Existing from Project 4:
# PORT, NODE_ENV, MONGODB_URI, JWT_SECRET, etc.

# File Upload Configuration
UPLOAD_DIR=./uploads
MAX_FILE_SIZE=10485760  # 10MB in bytes
ALLOWED_FILE_TYPES=pdf,docx,txt,xlsx,csv

# OpenAI Configuration
OPENAI_API_KEY=your_openai_api_key
OPENAI_EMBEDDING_MODEL=text-embedding-ada-002
OPENAI_CHAT_MODEL=gpt-4

# Vector Database (choose one)
# Option A: MongoDB Atlas Vector Search
MONGODB_ATLAS_VECTOR_SEARCH=true

# Option B: Pinecone
PINECONE_API_KEY=your_pinecone_api_key
PINECONE_ENVIRONMENT=your_environment
PINECONE_INDEX_NAME=document-embeddings

# Option C: Qdrant
QDRANT_URL=http://localhost:6333
QDRANT_API_KEY=your_qdrant_key

# RAG Configuration
CHUNK_SIZE=1000
CHUNK_OVERLAP=100
TOP_K_RESULTS=5

# Data Analysis (if implementing)
ENABLE_CODE_EXECUTION=false  # Set true only if using sandboxed approach
```

### **Client (.env) - Additional Variables**

```
# Existing from Project 4:
# VITE_API_BASE_URL, etc.

# File Upload
VITE_MAX_FILE_SIZE_MB=10
VITE_ALLOWED_FILE_TYPES=pdf,docx,txt,xlsx,csv

# Features
VITE_ENABLE_RAG=true
VITE_ENABLE_DATA_ANALYSIS=true
```

## **Performance Optimization**

### **Document Processing**
- Process files asynchronously (background jobs)
- Show progress indicators
- Cache extracted text
- Batch embedding generation
- Target: < 30 seconds for 10MB files

### **Vector Search**
- Optimize vector index configuration
- Use appropriate similarity metric (cosine)
- Limit top-k results (5-10 max)
- Target: < 2 seconds for semantic search

### **Chat Experience**
- Lazy load old messages
- Infinite scroll for message history
- Optimistic UI updates
- WebSocket for real-time updates (optional)
- Target: Instant message sending, < 5s response

### **Embedding Generation**
- Batch chunks (up to 2048 at once)
- Parallel processing where possible
- Cache embeddings
- Target: < 15 seconds for typical document

## **Security Considerations**

### **File Upload Security**
- Validate file types (whitelist only)
- Check file size limits
- Sanitize filenames
- Scan for malware (if possible)
- Isolate upload directory
- Delete temporary files after processing

### **Data Privacy**
- User-specific file isolation
- Encrypted file storage (optional)
- Secure file deletion
- No cross-user file access
- Audit logging for file operations

### **API Security**
- Rate limiting on file uploads
- Authentication required for all endpoints
- Role-based access control (from Project 4)
- Input validation on all requests
- Secure API key storage

### **Code Execution (If Implemented)**
- Sandboxed environment only
- Whitelist allowed operations
- Timeout enforcement
- Memory limits
- No file system access
- Validate generated code structure

## **Testing Scenarios**

### **Chat Functionality**
- [ ] Create new chat thread
- [ ] Send and receive messages
- [ ] Message history persists
- [ ] Thread list updates correctly
- [ ] Authentication required for all operations

### **File Upload (Admin Only)**
- [ ] Admin can upload PDF, DOCX, TXT
- [ ] Non-admin users blocked from upload
- [ ] File size validation works
- [ ] File type validation works
- [ ] Processing completes successfully

### **RAG System**
- [ ] Document chunks created correctly
- [ ] Embeddings generated successfully
- [ ] Vector search returns relevant results
- [ ] Responses include source citations
- [ ] Multiple documents can be queried
- [ ] Context maintained across messages

### **Data Analysis (Optional)**
- [ ] Excel/CSV files parse correctly
- [ ] Natural language queries work
- [ ] Analysis results are accurate
- [ ] Error handling for invalid queries

### **Integration**
- [ ] Works with Project 4 authentication
- [ ] Role-based access control enforced
- [ ] No security vulnerabilities
- [ ] Performance targets met

## **Common Pitfalls to Avoid**

### **RAG Implementation**
- ❌ Chunks too large (>2000 tokens)
- ❌ No overlap between chunks
- ❌ Not filtering results by relevance score
- ❌ Including too much irrelevant context
- ❌ Not citing sources

### **File Processing**
- ❌ Processing files synchronously (blocks server)
- ❌ Not validating file types
- ❌ Storing original files unnecessarily
- ❌ No error handling for corrupted files
- ❌ Memory leaks from large files

### **Vector Database**
- ❌ Not creating proper indexes
- ❌ Using wrong similarity metric
- ❌ Not normalizing vectors
- ❌ Storing too many dimensions
- ❌ Not filtering by metadata

### **Security**
- ❌ Allowing any file type
- ❌ No file size limits
- ❌ Executing untrusted code
- ❌ Path traversal vulnerabilities
- ❌ Not requiring authentication

## **Success Checklist**

### **✅ Chat System**
- [ ] Chat threads create and display
- [ ] Messages send and persist
- [ ] Thread list updates correctly
- [ ] Authentication integrated (Project 4)
- [ ] Clean, usable UI

### **✅ File Upload**
- [ ] Admin users can upload files
- [ ] Regular users cannot upload
- [ ] File validation works
- [ ] Processing completes successfully
- [ ] Files associated with messages

### **✅ RAG System**
- [ ] Documents chunk correctly
- [ ] Embeddings generate successfully
- [ ] Vector search works
- [ ] Responses use retrieved context
- [ ] Sources cited correctly

### **✅ Performance**
- [ ] File processing < 30 seconds
- [ ] Vector search < 2 seconds
- [ ] Chat response < 5 seconds
- [ ] UI responsive and smooth

### **✅ Security**
- [ ] File upload properly secured
- [ ] Role-based access enforced
- [ ] No security vulnerabilities
- [ ] Proper error handling

## **Deliverables**

1. **GitHub Repository** with integrated platform
2. **Live Demo** showing document analysis
3. **DEMO.md** with:
   - Screenshots of chat interface
   - File upload and processing demos
   - RAG query examples with sources
   - Data analysis examples (if implemented)
4. **ARCHITECTURE.md** with:
   - System architecture diagram
   - RAG pipeline explanation
   - Database schema
   - API documentation
5. **Technical_Learnings.md** with:
   - RAG implementation insights
   - File processing challenges
   - Vector database learnings
   - Integration with Project 4

## **Evaluation Rubric (100 Points)**

| Criterion | Points | Details |
| ----- | :---: | ----- |
| **Chat System** | 20 pts | ✓ Thread management (8 pts) <br> ✓ Message persistence (7 pts) <br> ✓ UI/UX (5 pts) |
| **File Processing** | 25 pts | ✓ Multi-format support (10 pts) <br> ✓ Text extraction quality (8 pts) <br> ✓ Processing pipeline (7 pts) |
| **RAG System** | 30 pts | ✓ Chunking strategy (8 pts) <br> ✓ Vector search (10 pts) <br> ✓ Response quality (12 pts) |
| **Integration** | 15 pts | ✓ Project 4 integration (8 pts) <br> ✓ Role-based access (7 pts) |
| **Security & Quality** | 10 pts | ✓ File upload security (5 pts) <br> ✓ Code quality (5 pts) |

## **Quick Start Resources**

### **File Processing**
- **pdf-parse**: https://www.npmjs.com/package/pdf-parse
- **mammoth**: https://www.npmjs.com/package/mammoth
- **xlsx**: https://www.npmjs.com/package/xlsx

### **Vector Databases**
- **MongoDB Atlas Vector Search**: https://www.mongodb.com/docs/atlas/atlas-vector-search/
- **Pinecone**: https://docs.pinecone.io/
- **Qdrant**: https://qdrant.tech/documentation/

### **OpenAI**
- **Embeddings API**: https://platform.openai.com/docs/guides/embeddings
- **Chat Completions**: https://platform.openai.com/docs/guides/text-generation

### **RAG**
- **LangChain.js**: https://js.langchain.com/docs/get_started/introduction
- **RAG Concepts**: https://www.pinecone.io/learn/retrieval-augmented-generation/

### **React**
- **React Dropzone**: https://react-dropzone.js.org/
- **React Markdown**: https://github.com/remarkjs/react-markdown

---

**Remember**: This project teaches you how to build intelligent document analysis systems with RAG - a critical skill for modern AI applications. Focus on getting RAG working well before adding optional features!