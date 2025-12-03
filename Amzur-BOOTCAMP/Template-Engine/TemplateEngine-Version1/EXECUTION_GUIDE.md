# Code Review Template Execution Guide

## Overview
This comprehensive guide provides detailed step-by-step instructions for executing AI prompt templates to generate thorough code reviews and filled checklists for bootcamp projects. Each category produces both detailed analysis documents and completed checklists with specific, actionable feedback.

## What This Guide Covers
- **8 Review Categories**: AI Integration, Performance, Security, Architecture, Code Quality, and 3 Difficulty Levels
- **Systematic Execution**: Step-by-step prompts with expected outputs
- **Quality Assurance**: Validation steps and troubleshooting
- **Sample Project**: Ready-to-use demonstration codebase
- **Time Estimates**: Realistic time allocation for each review type

---

## Prerequisites & Setup

### Required Tools
1. **VS Code** with GitHub Copilot Chat extension (active subscription)
2. **Project workspace** loaded in VS Code
3. **Terminal access** for folder creation and navigation
4. **File system access** for saving review outputs

### Initial Setup Verification
```bash
# 1. Navigate to your project root
cd /path/to/your/project

# 2. Verify VS Code Copilot status
# Check bottom-right status bar shows "Copilot: Ready"

# 3. Test #codebase access
# In Copilot Chat, type: "Can you see my #codebase?"
# Should respond with project file structure

# 4. Create base review directory
mkdir code-reviews-$(date +%Y-%m-%d)
cd code-reviews-$(date +%Y-%m-%d)
```

### Pre-Review Checklist
- [ ] VS Code workspace contains target project
- [ ] Copilot Chat extension is active and connected
- [ ] #codebase command returns project file listing
- [ ] Terminal access available for folder creation
- [ ] At least 2-8 hours available depending on review scope

---

## Expected Outputs & Time Estimates

### Per Category Review Outputs
Each review category produces:
- **6-8 detailed analysis files** (numbered 00-07)
- **1 comprehensive report** (99-COMPREHENSIVE-[CATEGORY]-REPORT.md)
- **1 filled checklist** ([Category]-Checklist-FILLED.md)
- **Total files per category**: 8-10 documents

### Time Requirements
- **Single Category Review**: 30-60 minutes
- **Difficulty Level Review**: 45-90 minutes  
- **Complete Assessment**: 4-6 hours
- **Sample Project Demo**: 15-30 minutes per category

### Quality Indicators
✅ **Good Review Output**:
- All numbered files complete (00-07)
- Comprehensive report summarizes findings
- Filled checklist has specific scores (X/10)
- Concrete recommendations provided
- File naming convention followed

❌ **Incomplete Review**:
- Missing numbered analysis files
- Generic feedback without specifics
- Empty checklist sections
- No actionable recommendations

---

## Sample Project Setup

To demonstrate the review system, let's create a sample bootcamp project:
```
<sampleProjectSetupInstructions>
```

### Sample Project for Testing Reviews

A complete **Task Management System** is included in the `sample-bootcamp-project/` folder:

```
sample-bootcamp-project/
├── README.md                    # Project documentation
├── backend/
│   ├── main.py                 # FastAPI application with AI integration
│   ├── models.py               # SQLAlchemy database models
│   └── requirements.txt        # Python dependencies
├── frontend/
│   ├── Dashboard.jsx           # Main React dashboard component
│   ├── AITaskSuggestions.jsx   # AI-powered task suggestions
│   └── package.json            # Node.js dependencies
```

**Sample Project Features:**
- ✅ FastAPI backend with AI integration (OpenAI)
- ✅ React frontend with Tailwind CSS
- ✅ PostgreSQL database models
- ✅ JWT authentication
- ✅ CRUD operations for tasks
- ✅ AI-powered task suggestions
- ✅ Real-time analytics dashboard
- ✅ Responsive design with modern UI

**To Test Reviews with Sample Project:**
1. Navigate to the sample project: `cd sample-bootcamp-project`
2. Open in VS Code as workspace
3. Use the execution prompts below to review the sample code

---

## Category-Specific Execution Instructions

## 1. AI Integration Focus Review

### Time Required: 2-2.5 hours
### Output: 8 review files + 1 filled checklist

#### Phase 1: Setup (5 minutes)
```bash
# Create review folder structure
mkdir ai-integration-review
```

#### Phase 2: Execute AI Integration Prompts (75-90 minutes)

**Step 1: Architecture Analysis (15 minutes)**
1. Open GitHub Copilot Chat in VS Code
2. Copy and paste prompt from `AI-Integration-Focus/AI-Integration-Review-Prompts.md` - **Prompt #0**
3. Execute prompt - saves output as: `ai-integration-review/00-ai-architecture-analysis.md`

**Step 2: Service Integration Review (15 minutes)**
1. Execute **Prompt #1** from AI-Integration-Review-Prompts.md
2. Output saved as: `ai-integration-review/01-service-integration.md`

**Step 3: Prompt Engineering Review (15 minutes)**
1. Execute **Prompt #2** from AI-Integration-Review-Prompts.md
2. Output saved as: `ai-integration-review/02-prompt-engineering.md`

**Step 4: Response Processing Review (15 minutes)**
1. Execute **Prompt #3** from AI-Integration-Review-Prompts.md
2. Output saved as: `ai-integration-review/03-ai-response-processing.md`

**Step 5: Performance Optimization (15 minutes)**
1. Execute **Prompt #4** from AI-Integration-Review-Prompts.md
2. Output saved as: `ai-integration-review/04-ai-performance.md`

**Step 6: Security & Safety Review (15 minutes)**
1. Execute **Prompt #5** from AI-Integration-Review-Prompts.md
2. Output saved as: `ai-integration-review/05-ai-security.md`

**Step 7: User Experience Review (15 minutes)**
1. Execute **Prompt #6** from AI-Integration-Review-Prompts.md
2. Output saved as: `ai-integration-review/06-ai-user-experience.md`

#### Phase 3: Generate Comprehensive Report (15-20 minutes)
1. Execute **Prompt #7** from AI-Integration-Review-Prompts.md
2. Output saved as: `ai-integration-review/99-COMPREHENSIVE-AI-REPORT.md`

#### Phase 4: Create Filled Checklist (10 minutes)
1. Copy the blank checklist from `AI-Integration-Focus/AI-Integration-Review-Checklist.md`
2. Use this prompt to fill it:
```
Based on all the AI integration review files in the ai-integration-review/ folder, please fill out the AI Integration checklist template with specific scores, feedback, and recommendations for each section. For each checklist item, provide:
- Score (X/10) 
- Specific analysis of what was found
- Concrete recommendations for improvement
- Mark checkboxes based on implementation quality

Save the filled checklist as: ai-integration-review/AI-Integration-Checklist-FILLED.md
```

---

## 2. Performance Focus Review

### Time Required: 1.5-2 hours
### Output: 8 review files + 1 filled checklist

#### Phase 1: Setup (5 minutes)
```bash
mkdir performance-review
```

#### Phase 2: Execute Performance Prompts (60-75 minutes)

**Step 1: Performance Baseline (15 minutes)**
1. Execute **Prompt #0** from `Performance-Focus/Performance-Review-Prompts.md`
2. Output: `performance-review/00-performance-baseline-analysis.md`

**Step 2: Database Performance (15 minutes)**
1. Execute **Prompt #1**
2. Output: `performance-review/01-database-performance.md`

**Step 3: API Performance (15 minutes)**
1. Execute **Prompt #2**
2. Output: `performance-review/02-api-performance.md`

**Step 4: Frontend Performance (15 minutes)**
1. Execute **Prompt #3**
2. Output: `performance-review/03-frontend-performance.md`

**Step 5: Memory & Resource Performance (10 minutes)**
1. Execute **Prompt #4**
2. Output: `performance-review/04-memory-resource-performance.md`

**Step 6: Caching Performance (10 minutes)**
1. Execute **Prompt #5**
2. Output: `performance-review/05-caching-performance.md`

**Step 7: Scalability Performance (15 minutes)**
1. Execute **Prompt #6**
2. Output: `performance-review/06-scalability-performance.md`

#### Phase 3: Generate Comprehensive Report (15 minutes)
1. Execute **Prompt #7**
2. Output: `performance-review/99-COMPREHENSIVE-PERFORMANCE-REPORT.md`

#### Phase 4: Create Filled Checklist (10 minutes)
Use this prompt:
```
Based on all the performance review files in the performance-review/ folder, please fill out the Performance Review checklist template from Performance-Focus/Performance-Review-Checklist.md with specific scores, performance metrics, and recommendations for each section.

Save the filled checklist as: performance-review/Performance-Checklist-FILLED.md
```

---

## 3. Security Focus Review

### Time Required: 1.5 hours
### Output: 8 review files + 1 filled checklist

#### Phase 1: Setup (5 minutes)
```bash
mkdir security-review
```

#### Phase 2: Execute Security Prompts (45-60 minutes)

**Step 1: Security Context Analysis (10 minutes)**
1. Execute **Prompt #0** from `Security-Focus/Security-Review-Prompts.md`
2. Output: `security-review/00-security-context-analysis.md`

**Step 2: Authentication Security (10 minutes)**
1. Execute **Prompt #1**
2. Output: `security-review/01-authentication-security.md`

**Step 3: Input Validation Security (10 minutes)**
1. Execute **Prompt #2**
2. Output: `security-review/02-input-validation-security.md`

**Step 4: Data Protection Security (10 minutes)**
1. Execute **Prompt #3**
2. Output: `security-review/03-data-protection-security.md`

**Step 5: API Security (10 minutes)**
1. Execute **Prompt #4**
2. Output: `security-review/04-api-security.md`

**Step 6: Frontend Security (10 minutes)**
1. Execute **Prompt #5**
2. Output: `security-review/05-frontend-security.md`

**Step 7: Infrastructure Security (10 minutes)**
1. Execute **Prompt #6**
2. Output: `security-review/06-infrastructure-security.md`

#### Phase 3: Generate Comprehensive Report (10-15 minutes)
1. Execute **Prompt #7**
2. Output: `security-review/99-COMPREHENSIVE-SECURITY-REPORT.md`

#### Phase 4: Create Filled Checklist (10 minutes)
```
Based on all the security review files in the security-review/ folder, please fill out the Security Review checklist template from Security-Focus/Security-Review-Checklist.md with specific security scores, vulnerability assessments, and remediation recommendations.

Save the filled checklist as: security-review/Security-Checklist-FILLED.md
```

---

## 4. Code Quality Focus Review

### Time Required: 1.5-2 hours
### Output: 8 review files + 1 filled checklist

#### Phase 1: Setup (5 minutes)
```bash
mkdir code-quality-review
```

#### Phase 2: Execute Code Quality Prompts (60-75 minutes)

**Step 1: Quality Baseline Analysis (15 minutes)**
1. Execute **Prompt #0** from `Code-Quality-Focus/Code-Quality-Review-Prompts.md`
2. Output: `code-quality-review/00-quality-baseline-analysis.md`

**Step 2: Readability & Clarity (15 minutes)**
1. Execute **Prompt #1**
2. Output: `code-quality-review/01-readability-clarity.md`

**Step 3: Structure & Organization (15 minutes)**
1. Execute **Prompt #2**
2. Output: `code-quality-review/02-structure-organization.md`

**Step 4: Error Handling & Robustness (10 minutes)**
1. Execute **Prompt #3**
2. Output: `code-quality-review/03-error-handling.md`

**Step 5: Type Safety & Validation (10 minutes)**
1. Execute **Prompt #4**
2. Output: `code-quality-review/04-type-safety.md`

**Step 6: Performance & Efficiency (10 minutes)**
1. Execute **Prompt #5**
2. Output: `code-quality-review/05-performance-efficiency.md`

**Step 7: Testing & Quality Assurance (10 minutes)**
1. Execute **Prompt #6**
2. Output: `code-quality-review/06-testing-quality.md`

#### Phase 3: Generate Comprehensive Report (15 minutes)
1. Execute **Prompt #7**
2. Output: `code-quality-review/99-COMPREHENSIVE-QUALITY-REPORT.md`

#### Phase 4: Create Filled Checklist (10 minutes)
```
Based on all the code quality review files in the code-quality-review/ folder, please fill out the Code Quality Review checklist template from Code-Quality-Focus/Code-Quality-Review-Checklist.md with specific quality scores, code analysis, and improvement recommendations.

Save the filled checklist as: code-quality-review/Code-Quality-Checklist-FILLED.md
```

---

## 5. Architecture Focus Review

### Time Required: 1.5 hours
### Output: Review files + 1 filled checklist

#### Phase 1: Setup (5 minutes)
```bash
mkdir architecture-review
```

#### Phase 2: Execute Architecture Prompts (60 minutes)
1. Execute prompts from `Architecture-Focus/Architecture-Review-Prompts.md` sequentially
2. Save outputs with descriptive names in `architecture-review/` folder

#### Phase 3: Create Filled Checklist (15 minutes)
```
Based on all the architecture review files in the architecture-review/ folder, please fill out the Architecture Review checklist template from Architecture-Focus/Architecture-Review-Checklist.md with specific architectural assessments and recommendations.

Save the filled checklist as: architecture-review/Architecture-Checklist-FILLED.md
```

---

## 6. Difficulty Level Reviews

### For Beginner Level (1 hour)
```bash
mkdir beginner-review
```
Execute prompts from `Difficulty-Levels/Beginner/Beginner-Review-Prompts.md`

### For Intermediate Level (1.5 hours)
```bash
mkdir intermediate-review
```
Execute prompts from `Difficulty-Levels/Intermediate/Intermediate-Review-Prompts.md`

### For Advanced Level (2-2.5 hours)
```bash
mkdir advanced-review
```
Execute prompts from `Difficulty-Levels/Advanced/Advanced-Review-Prompts.md`

---

## How to Execute Reviews - Detailed Instructions

### Quick Start Prompts for Each Category

When you want me to execute a specific review category, use these exact prompts:

#### For AI Integration Review:
```
Please execute the AI Integration Focus review for my #codebase. Follow the prompts from Templates/AI-Integration-Focus/AI-Integration-Review-Prompts.md and create a complete analysis with:
1. All 8 individual review files (00-07)
2. Comprehensive report (99-COMPREHENSIVE)
3. Filled checklist with specific scores and feedback
Save all outputs in ai-integration-review/ folder with proper naming.
```

#### For Security Focus Review:
```
Please execute the Security Focus review for my #codebase. Follow the prompts from Templates/Security-Focus/Security-Review-Prompts.md and create:
1. All 8 security analysis files (00-07)
2. Comprehensive security report (99-COMPREHENSIVE)
3. Filled security checklist with vulnerability assessments
Save all outputs in security-review/ folder.
```

#### For Performance Focus Review:
```
Please execute the Performance Focus review for my #codebase. Follow the prompts from Templates/Performance-Focus/Performance-Review-Prompts.md and create:
1. All 8 performance analysis files (00-07)
2. Comprehensive performance report (99-COMPREHENSIVE)
3. Filled performance checklist with optimization recommendations
Save all outputs in performance-review/ folder.
```

#### For Architecture Focus Review:
```
Please execute the Architecture Focus review for my #codebase. Follow the prompts from Templates/Architecture-Focus/Architecture-Review-Prompts.md and create:
1. All architectural analysis files
2. Comprehensive architecture report
3. Filled architecture checklist with design recommendations
Save all outputs in architecture-review/ folder.
```

#### For Code Quality Focus Review:
```
Please execute the Code Quality Focus review for my #codebase. Follow the prompts from Templates/Code-Quality-Focus/Code-Quality-Review-Prompts.md and create:
1. All 8 code quality analysis files (00-07)
2. Comprehensive quality report (99-COMPREHENSIVE)
3. Filled code quality checklist with improvement recommendations
Save all outputs in code-quality-review/ folder.
```

#### For Beginner Level Review:
```
Please execute the Beginner Level review for my #codebase. Follow the prompts from Templates/Difficulty-Levels/Beginner/Beginner-Review-Prompts.md and create:
1. All 6 beginner analysis files (00-05)
2. Comprehensive beginner learning report (99-COMPREHENSIVE)
3. Filled beginner checklist with encouraging feedback
Save all outputs in beginner-review/ folder.
```

#### For Intermediate Level Review:
```
Please execute the Intermediate Level review for my #codebase. Follow the prompts from Templates/Difficulty-Levels/Intermediate/Intermediate-Review-Prompts.md and create:
1. All intermediate analysis files
2. Comprehensive intermediate report
3. Filled intermediate checklist with growth recommendations
Save all outputs in intermediate-review/ folder.
```

#### For Advanced Level Review:
```
Please execute the Advanced Level review for my #codebase. Follow the prompts from Templates/Difficulty-Levels/Advanced/Advanced-Review-Prompts.md and create:
1. All advanced analysis files
2. Comprehensive advanced report
3. Filled advanced checklist with leadership assessment
Save all outputs in advanced-review/ folder.
```

#### For Complete Project Assessment:
```
Please execute a comprehensive review of my #codebase covering all aspects:
1. Start with appropriate difficulty level assessment (Beginner/Intermediate/Advanced)
2. Follow with all specialized focus areas (AI Integration, Security, Performance, Architecture, Code Quality)
3. Create master summary report combining all assessments
4. Provide prioritized recommendations and learning path
This is a complete bootcamp project evaluation - please be thorough and detailed.
```

---
## General Execution Tips

### Best Practices
1. **Execute prompts in sequence** - Each builds on previous analysis
2. **Wait for complete output** - Don't interrupt Copilot while generating
3. **Review each output** - Ensure quality before proceeding
4. **Customize when needed** - Adapt prompts for specific project contexts

### Troubleshooting
- **If #codebase not working**: Refresh VS Code and ensure all files are indexed
- **If prompts fail**: Check Copilot connection and retry
- **If output incomplete**: Ask Copilot to continue from where it stopped

### Quality Assurance
Before completing any review category:
- [ ] All prompts executed successfully
- [ ] Review files generated and complete
- [ ] Comprehensive report compiled
- [ ] Filled checklist created with specific feedback
- [ ] All files saved in correct folder structure

---

## Troubleshooting Common Issues

### Setup Issues
**Problem**: Copilot Chat not working  
**Solution**: 
- Check Copilot subscription status
- Restart VS Code
- Ensure workspace is properly loaded

**Problem**: #codebase returns empty  
**Solution**:
- Refresh VS Code workspace
- Ensure all files are saved
- Check file indexing in VS Code

**Problem**: Review outputs are incomplete  
**Solution**:
- Break large prompts into smaller sections
- Ask me to "continue" if output stops mid-generation
- Verify all template files are accessible

### Review Quality Issues
**Problem**: Generic feedback without specifics  
**Solution**: 
- Ensure #codebase is properly accessible
- Use more specific prompts
- Request examples and code snippets in feedback

**Problem**: Missing checklist scores  
**Solution**: 
- Explicitly request scored checklists
- Ask for specific numerical ratings (X/10)
- Request justification for each score

### File Organization Issues
**Problem**: Review files scattered  
**Solution**:
- Create folder structure before starting
- Use consistent naming conventions
- Save files as requested in prompts

---

## Summary: Your Complete Review System

You now have:

### ✅ **8 Complete Review Categories**
1. **Beginner Level** (Learning-focused, encouraging)
2. **Intermediate Level** (Production-ready skills)
3. **Advanced Level** (Enterprise and leadership assessment)
4. **AI Integration Focus** (AI service integration and prompt engineering)
5. **Security Focus** (Vulnerability assessment and security best practices)
6. **Performance Focus** (Optimization and scalability)
7. **Architecture Focus** (System design and patterns)
8. **Code Quality Focus** (Maintainability and best practices)

### ✅ **Ready-to-Use Execution**
- **Copy-paste prompts** for each category
- **Estimated time requirements** for planning
- **Expected output structure** for validation
- **Sample project** for immediate testing

### ✅ **Flexible Usage Options**
- **Single Category**: Focus on specific areas (30-60 minutes)
- **Difficulty Assessment**: Match candidate skill level (45-90 minutes)
- **Complete Evaluation**: Full project assessment (4-6 hours)
- **Progressive Reviews**: Build understanding over time

### 🚀 **Getting Started**
1. **Choose your target**: Real project or sample project
2. **Select review type**: Single category or comprehensive
3. **Copy the appropriate prompt** from this guide
4. **Paste into our conversation** to begin review
5. **Receive detailed analysis** with scores and recommendations

**Your review system is now ready for production use with bootcamp candidates!**
