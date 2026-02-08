# Implementation Plan: HerWay Platform

## Overview

This implementation plan breaks down the HerWay platform into discrete coding tasks. The approach follows an incremental development strategy: starting with core data structures and repository, then building the AI engine components, followed by the recommendation and chat systems, and finally the web UI. Each major component includes property-based tests to validate correctness properties from the design document.

## Tasks

- [ ] 1. Set up project structure and dependencies
  - Create TypeScript project with necessary configuration (tsconfig.json, package.json)
  - Install dependencies: fast-check (property testing), express (API), AI SDK (OpenAI/Anthropic)
  - Set up testing framework (Jest or Vitest)
  - Create directory structure: /src/models, /src/services, /src/api, /src/ui, /tests
  - _Requirements: All requirements (foundation)_

- [ ] 2. Implement core data models and types
  - [ ] 2.1 Create TypeScript interfaces for UserProfile, Scheme, and related types
    - Define UserProfile interface with age, educationLevel, incomeBracket, employmentStatus, language
    - Define Scheme interface with id, name, description, eligibility, benefits, applicationProcess
    - Define supporting types: LocalizedString, EligibilityCriteria, EducationLevel, IncomeBracket, EmploymentStatus, Language enums
    - Define validation types: ValidationResult, ValidationError
    - _Requirements: 1.2, 2.1, 2.2_
  
  - [ ]* 2.2 Write property test for profile data completeness
    - **Property 1: Profile Data Completeness**
    - **Validates: Requirements 1.2**
  
  - [ ]* 2.3 Write property test for scheme data completeness
    - **Property 4: Scheme Data Completeness**
    - **Validates: Requirements 2.1, 2.2**

- [ ] 3. Implement Profile Service with validation
  - [ ] 3.1 Create ProfileService class with validation logic
    - Implement validateProfile method to check required fields
    - Implement createProfile method to structure form data
    - Add validation for age range (15-100), enum values
    - Return structured ValidationResult with field-specific errors
    - _Requirements: 1.2, 1.3, 1.4_
  
  - [ ]* 3.2 Write property test for validation error identification
    - **Property 2: Validation Error Identification**
    - **Validates: Requirements 1.3**
  
  - [ ]* 3.3 Write unit tests for profile validation edge cases
    - Test boundary ages (14, 15, 100, 101)
    - Test invalid enum values
    - Test missing required fields
    - _Requirements: 1.3_

- [ ] 4. Implement Scheme Repository
  - [ ] 4.1 Create SchemeRepository class with in-memory storage
    - Implement getAllSchemes, getSchemeById, getSchemesByCategory methods
    - Load initial scheme data from JSON file or hardcoded fixtures
    - Include at least 10-15 sample schemes covering education, employment, healthcare, financial categories
    - Ensure all schemes have complete bilingual content (English and Hindi)
    - _Requirements: 2.1, 2.2, 2.3, 2.4_
  
  - [ ]* 4.2 Write property test for scheme retrieval round-trip
    - **Property 5: Scheme Retrieval Round-Trip**
    - **Validates: Requirements 2.3**

- [ ] 5. Checkpoint - Ensure core data layer works
  - Run all tests to verify data models and repository
  - Ensure all tests pass, ask the user if questions arise

- [ ] 6. Implement AI Engine - Summarization Module
  - [ ] 6.1 Create SummarizationService with AI integration
    - Implement summarizeScheme method using AI API (OpenAI or Anthropic)
    - Create prompt template that instructs AI to simplify government language
    - Extract scheme purpose, key benefits, and eligibility from AI response
    - Handle both English and Hindi summarization
    - Implement error handling with fallback to original description
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5_
  
  - [ ]* 6.2 Write property test for summary generation
    - **Property 6: Summary Generation**
    - **Validates: Requirements 3.1, 3.4**
  
  - [ ]* 6.3 Write unit tests for summarization error handling
    - Test AI API failure scenarios
    - Test fallback to original description
    - _Requirements: 9.1_

- [ ] 7. Implement AI Engine - Translation Module
  - [ ] 7.1 Create TranslationService for bilingual support
    - Implement translate method using AI API
    - Implement getLocalizedContent helper to extract correct language from LocalizedString
    - Cache translations to reduce API calls
    - _Requirements: 1.5, 3.3, 6.1, 6.2_
  
  - [ ]* 7.2 Write property test for bilingual content consistency
    - **Property 7: Bilingual Content Consistency**
    - **Validates: Requirements 1.5, 3.3, 5.4, 9.4**

- [ ] 8. Implement AI Engine - Eligibility Matching Module
  - [ ] 8.1 Create EligibilityMatcher class with matching logic
    - Implement matchSchemes method to evaluate profile against all schemes
    - Implement age matching: check if user age falls within scheme's min/max age
    - Implement education matching: check if user's education level is in scheme's accepted levels
    - Implement income matching: check if user's income bracket is in scheme's accepted brackets
    - Implement employment matching: check if user's employment status is in scheme's accepted statuses
    - Implement calculateRelevanceScore with weighted scoring (employment: 0.3, education: 0.3, income: 0.2, target audience: 0.2)
    - Return MatchResult array with scheme, relevanceScore, and matchedCriteria
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 4.6_
  
  - [ ]* 8.2 Write property test for comprehensive eligibility evaluation
    - **Property 8: Comprehensive Eligibility Evaluation**
    - **Validates: Requirements 4.1, 4.2, 4.3, 4.4, 4.5**
  
  - [ ]* 8.3 Write property test for relevance-based ranking
    - **Property 9: Relevance-Based Ranking**
    - **Validates: Requirements 4.6**

- [ ] 9. Implement Recommendation Engine
  - [ ] 9.1 Create RecommendationEngine class
    - Implement getRecommendations method that uses EligibilityMatcher
    - Filter schemes to only include those where user meets ALL eligibility criteria
    - Implement rankSchemes method to sort by relevance score (descending)
    - Generate "whyRecommended" explanation for each scheme
    - Return top 10 schemes (configurable)
    - _Requirements: 4.6, 4.7_
  
  - [ ]* 9.2 Write property test for eligibility filtering
    - **Property 10: Eligibility Filtering**
    - **Validates: Requirements 4.7**
  
  - [ ]* 9.3 Write unit tests for recommendation ranking
    - Test with known profiles and schemes
    - Verify correct ordering by relevance score
    - _Requirements: 4.6_

- [ ] 10. Checkpoint - Ensure recommendation system works
  - Run all tests to verify AI engine and recommendation logic
  - Manually test with sample profiles
  - Ensure all tests pass, ask the user if questions arise

- [ ] 11. Implement Chat Service
  - [ ] 11.1 Create ChatService class with conversation management
    - Implement processQuestion method that accepts question and ChatContext
    - Build prompt that includes user profile, current scheme, and conversation history
    - Use AI API to generate contextual responses
    - Implement generateResponse method with context awareness
    - Handle questions about eligibility, benefits, and application processes
    - Return ChatResponse with answer and optional related schemes
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.6_
  
  - [ ]* 11.2 Write property test for chat response generation
    - **Property 11: Chat Response Generation**
    - **Validates: Requirements 5.2**
  
  - [ ]* 11.3 Write property test for chat context preservation
    - **Property 12: Chat Context Preservation**
    - **Validates: Requirements 5.6**
  
  - [ ]* 11.4 Write unit tests for chat error handling
    - Test unanswerable questions
    - Test AI API failures
    - _Requirements: 5.5, 9.1_

- [ ] 12. Implement Session Management
  - [ ] 12.1 Create SessionService for browser session storage
    - Implement storeProfile and getProfile methods using sessionStorage
    - Implement storeChatHistory and getChatHistory methods
    - Implement storeLanguage and getLanguage methods
    - Add error handling for storage quota exceeded
    - Ensure data is session-only (not persisted after browser close)
    - _Requirements: 1.4, 5.6, 6.4, 7.2, 7.3_
  
  - [ ]* 12.2 Write property test for profile session round-trip
    - **Property 3: Profile Session Round-Trip**
    - **Validates: Requirements 1.4**
  
  - [ ]* 12.3 Write property test for session-only data storage
    - **Property 14: Session-Only Data Storage**
    - **Validates: Requirements 7.2**

- [ ] 13. Implement API Gateway
  - [ ] 13.1 Create Express API with endpoints
    - POST /api/profile - validate and store user profile, return recommendations
    - GET /api/schemes/:id - get scheme details by ID
    - POST /api/chat - process chat question with context
    - GET /api/schemes - get all schemes (for browsing)
    - Add language header handling for all endpoints
    - Implement error handling middleware
    - Return structured error responses with localized messages
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 4.7, 5.2, 8.3, 9.1, 9.3_
  
  - [ ]* 13.2 Write integration tests for API endpoints
    - Test profile submission and recommendation flow
    - Test chat question processing
    - Test error responses
    - _Requirements: 1.1, 4.7, 5.2_

- [ ] 14. Checkpoint - Ensure backend API works
  - Run all tests to verify API endpoints
  - Test API manually with Postman or curl
  - Ensure all tests pass, ask the user if questions arise

- [ ] 15. Implement Web UI - Core Components
  - [ ] 15.1 Create React/Vue components for main pages
    - Create WelcomePage component with language selection
    - Create ProfileForm component with all input fields (age, education, income, employment)
    - Add form validation with inline error display
    - Create RecommendationsPage component to display scheme cards
    - Create SchemeDetailPage component to show complete scheme information
    - Implement navigation between pages
    - _Requirements: 1.1, 1.2, 1.3, 8.1, 8.2, 8.3, 8.4, 8.5_
  
  - [ ]* 15.2 Write unit tests for form validation UI
    - Test validation error display
    - Test form submission
    - _Requirements: 1.3, 9.3_

- [ ] 16. Implement Web UI - Chat Interface
  - [ ] 16.1 Create ChatInterface component
    - Create chat message display with user/assistant roles
    - Create chat input field with submit button
    - Implement message sending and response display
    - Add loading indicator during AI processing
    - Make chat accessible from all pages (floating button or sidebar)
    - _Requirements: 5.1, 5.2, 5.6, 10.3_
  
  - [ ]* 16.2 Write property test for loading indicator visibility
    - **Property 17: Loading Indicator Visibility**
    - **Validates: Requirements 10.3**

- [ ] 17. Implement Web UI - Language Switching
  - [ ] 17.1 Add language toggle and localization
    - Create LanguageToggle component (English/Hindi switch)
    - Implement i18n for all UI text (labels, buttons, messages)
    - Update all displayed content when language changes
    - Persist language preference in session
    - Ensure error messages display in selected language
    - _Requirements: 1.5, 6.1, 6.2, 6.3, 6.4, 9.4_
  
  - [ ]* 17.2 Write property test for language switching consistency
    - **Property 13: Language Switching Consistency**
    - **Validates: Requirements 6.2, 6.3, 6.4**

- [ ] 18. Implement Web UI - Scheme Display
  - [ ] 18.1 Create scheme display components
    - Create SchemeCard component for list view (name, summary, benefits)
    - Create SchemeDetail component for full view (all fields, application steps)
    - Ensure all required information is displayed
    - Add "Back to recommendations" navigation
    - _Requirements: 8.2, 8.3, 8.4, 8.5_
  
  - [ ]* 18.2 Write property test for complete scheme display
    - **Property 15: Complete Scheme Display**
    - **Validates: Requirements 8.2, 8.3, 8.4**

- [ ] 19. Implement Error Handling UI
  - [ ] 19.1 Add error display components
    - Create ErrorMessage component for displaying errors
    - Add error boundaries for React components
    - Display user-friendly error messages for AI failures, validation errors, repository errors
    - Ensure errors display in selected language
    - Add actionable guidance in error messages
    - _Requirements: 9.1, 9.2, 9.3, 9.4, 9.5_
  
  - [ ]* 19.2 Write property test for error message display
    - **Property 16: Error Message Display**
    - **Validates: Requirements 9.1, 9.3, 9.4**

- [ ] 20. Add no-login notice and session warning
  - [ ] 20.1 Create informational components
    - Add notice on welcome page: "No login required - your data is only stored for this session"
    - Add session warning: "Your information will not be saved when you close the browser"
    - Display in both English and Hindi
    - _Requirements: 7.1, 7.4_

- [ ] 21. Implement sample scheme data
  - [ ] 21.1 Create comprehensive scheme dataset
    - Add 10-15 real government schemes for women in India
    - Include schemes for students (scholarships, education grants)
    - Include schemes for working women (skill development, entrepreneurship)
    - Include schemes for underserved communities (rural women, financial inclusion)
    - Ensure all schemes have complete bilingual content (English and Hindi)
    - Include varied eligibility criteria to test matching logic
    - _Requirements: 2.4_

- [ ] 22. Final integration and testing
  - [ ] 22.1 Wire all components together
    - Connect UI to API endpoints
    - Ensure data flows correctly from profile submission to recommendations
    - Test chat integration with scheme context
    - Verify language switching works across all components
    - Test error handling end-to-end
    - _Requirements: All requirements_
  
  - [ ]* 22.2 Write integration tests for complete user flows
    - Test: User submits profile → sees recommendations → views scheme details
    - Test: User asks chat questions → receives contextual answers
    - Test: User switches language → all content updates
    - _Requirements: All requirements_

- [ ] 23. Final checkpoint - Complete system verification
  - Run all unit tests and property tests
  - Manually test all user flows
  - Verify mobile responsiveness
  - Test with both English and Hindi
  - Ensure all tests pass, ask the user if questions arise

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- Each task references specific requirements for traceability
- Property tests validate universal correctness properties from the design document
- The implementation follows an incremental approach: data layer → AI engine → API → UI
- Checkpoints ensure validation at key milestones
- For hackathon timeline, prioritize core functionality over comprehensive testing
- AI API integration requires API keys (OpenAI or Anthropic) - ensure these are configured
