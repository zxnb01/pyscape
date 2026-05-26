# PyScape-Basic: System Architecture

## Overview

PyScape-Basic is a **foundational adaptive learning platform** designed to teach Python, AI, Machine Learning, and Data Science through personalized learning paths, interactive visualizations, real-time coding challenges, and guided projects.

This document outlines the system architecture, data flow, component relationships, and key design decisions.

## System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                         PYSCAPE-BASIC                               │
└─────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────┐          ┌──────────────────────────────┐
│      FRONTEND (React)         │          │      BACKEND (Express)       │
├──────────────────────────────┤          ├──────────────────────────────┤
│  Pages (Feature-organized)    │          │  REST API Routes             │
│  ├─ Onboarding              │          │  ├─ /api/auth               │
│  ├─ Learning                │          │  ├─ /api/problems           │
│  ├─ Practice                │◄────────►│  ├─ /api/submissions        │
│  ├─ Portfolio               │  HTTP    │  ├─ /api/duel               │
│  └─ Explore                 │          │  ├─ /api/roadmap            │
│                              │          │  └─ /api/portfolio          │
│  Components                  │          │                              │
│  ├─ Layout                  │          │  WebSocket Server (port 8080)│
│  ├─ Learning                │◄────────►│  ├─ Duel Events             │
│  ├─ Visualizers             │  WS      │  ├─ Chat Events             │
│  ├─ Editor                  │          │  └─ Notifications           │
│  └─ Common                  │          │                              │
│                              │          │  Services                    │
│  Features                    │          │  ├─ Code Execution (Judge0) │
│  ├─ Auth                    │          │  ├─ Matchmaking             │
│  ├─ Duel                    │          │  ├─ XP/Gamification         │
│  ├─ Roadmap                 │          │  └─ Portfolio Generation    │
│  └─ Portfolio               │          │                              │
└──────────────────────────────┘          └──────────────────────────────┘
         │                                            │
         │                                            │
         └────────────────┬─────────────────────────────┘
                          │
                          ▼
        ┌──────────────────────────────────┐
        │  SUPABASE (PostgreSQL + Auth)     │
        ├──────────────────────────────────┤
        │  Tables:                          │
        │  ├─ users (profiles, XP, level)  │
        │  ├─ problems (coding challenges) │
        │  ├─ submissions (code runs)      │
        │  ├─ duels (1v1 matches)          │
        │  ├─ duel_stats (leaderboard)     │
        │  ├─ roadmap (learning paths)     │
        │  ├─ lessons (content)            │
        │  ├─ portfolio (projects)         │
        │  └─ duel_submissions             │
        └──────────────────────────────────┘
         │
         └─ Realtime subscriptions (WebSocket)
```

## Frontend Architecture

### Page Structure (Feature-Based)

```
frontend/src/pages/
├─ onboarding/           # User signup, topic selection, profile creation
│  ├─ SplashScreen.js
│  ├─ Auth.js
│  ├─ TopicSelection.js
│  ├─ OnboardingQuiz.js
│  └─ ProfileBuild.js
│
├─ learning/             # Core learning experience
│  ├─ Dashboard.js       # Progress overview, next recommended lesson
│  ├─ RoadmapPage.js    # Interactive roadmap visualization
│  ├─ LevelPage.js      # Level details
│  ├─ ModulePage.js     # Module details
│  ├─ LessonPage.js     # Individual lesson
│  └─ Learn.js          # Active learning interface
│
├─ practice/             # Coding practice and real-time challenges
│  ├─ CodeDuel.js       # Real-time 1v1 battles
│  ├─ MLSandbox.js      # Multi-language code execution
│  ├─ AlgorithmVisualizer.js  # Algorithm visualization hub
│  └─ ProjectLabs.js    # Guided project labs
│
├─ portfolio/            # User portfolio and showcasing
│  ├─ Portfolio.js      # Portfolio overview
│  ├─ ProjectDetailPage.js   # Individual project detail
│  ├─ UserProfile.js    # User profile page
│  └─ SentimentAnalyzerPDF.js # PDF export
│
└─ explore/              # Leaderboards, trends, community
   ├─ AllNews.js        # AI news feed
   └─ DuelLeaderboard.js # Global leaderboard
```

### Component Organization

```
frontend/src/components/
├─ auth/                 # Authentication-related components
│  └─ ProtectedRoute.js
│
├─ layout/               # Shell components
│  ├─ Header.js
│  ├─ Sidebar.js
│  └─ Layout.js
│
├─ common/               # Reusable UI components
│  ├─ Button.js
│  ├─ Modal.js
│  ├─ Card.js
│  └─ ... (other shared components)
│
├─ learning/             # Learning-specific components
│  ├─ SkillNode.js
│  ├─ SkillLessonsModal.js
│  └─ ... (lesson-specific UI)
│
├─ visualizers/          # Algorithm visualization components
│  ├─ SortingVisualizer.js
│  ├─ PathfindingVisualizer.js
│  ├─ KMeansVisualizer.js
│  ├─ NeuralNetworkVisualizer.js
│  ├─ GradientDescentVisualizer.js
│  ├─ DataStructureVisualizer.js
│  ├─ TheoryCard.js
│  └─ TutorialOverlay.js
│
├─ editor/               # Code editor wrapper
│  └─ UniversalCodePlayground.js
│
├─ codeDuel/             # Code duel UI components
│  ├─ DuelChallengeList.js
│  └─ DuelLeaderboard.js
│
├─ dashboard/            # Dashboard preview components
│  ├─ CodeDuelPreview.js
│  ├─ MLSandboxPreview.js
│  ├─ ProjectLabPreview.js
│  ├─ TrendingNews.js
│  └─ UserRoadmap.js
│
├─ sandbox/              # ML Sandbox components
│  └─ UniversalCodePlayground.js
│
└─ portfolio/            # Portfolio components
   └─ SentimentAnalyzerPDF.js
```

### State Management

```
frontend/src/
├─ context/
│  ├─ AuthContext.js     # User authentication & profile state
│  ├─ DuelContext.js     # (Future) Duel session state
│  └─ LearningContext.js # (Future) Learning progress state
│
├─ hooks/                # Custom React hooks
│  ├─ useAuth.js
│  ├─ useDuel.js
│  └─ ... (feature-specific hooks)
│
└─ features/             # Feature-specific logic & hooks
   ├─ auth/              # Authentication feature
   ├─ duel/              # Code duel feature
   ├─ roadmap/           # Roadmap/learning path feature
   └─ portfolio/         # Portfolio feature
```

### Services Layer

```
frontend/src/services/
├─ supabaseClient.js     # Supabase initialization & client
├─ roadmapService.js     # Roadmap data fetching & generation
├─ authService.js        # Authentication service
├─ duelService.js        # (Future) Duel-specific service
└─ portfolioService.js   # (Future) Portfolio service
```

## Backend Architecture

### REST API Routes

```
backend/src/routes/api/
├─ auth.js              # POST /api/auth/login, /api/auth/signup, /api/auth/logout
├─ problems.js          # GET /api/problems, POST /api/problems/submit
├─ submissions.js       # GET /api/submissions/:id, POST /api/submissions
├─ users.js             # GET /api/users/:id, PUT /api/users/:id
├─ duel.js              # GET /api/duel/stats, POST /api/duel/join
├─ roadmap.js           # GET /api/roadmap/:userId
└─ portfolio.js         # GET /api/portfolio/:userId, POST /api/portfolio/export
```

### WebSocket Event Handlers

```
backend/src/routes/ws/
├─ duel-events.js       # join-queue, match, code-submit, forfeit, complete
├─ chat-events.js       # send-message, receive-message
└─ notifications.js     # (Future) Real-time notifications
```

### Business Logic

```
backend/src/services/
├─ codeExecutionService.js   # Judge0 API integration for code execution
├─ matchmakingService.js     # Player matching algorithm
├─ xpService.js              # XP calculation and awards
├─ portfolioService.js       # Portfolio generation and export
└─ roadmapService.js         # Roadmap generation from interests
```

### Models & Configuration

```
backend/src/
├─ models/               # Database model definitions
│  ├─ User.js
│  ├─ Problem.js
│  ├─ Duel.js
│  └─ ... (other models)
│
├─ config/               # Application configuration
│  ├─ database.js        # Database connection
│  ├─ judge0.js          # Judge0 API configuration
│  └─ environment.js     # Environment variables
│
├─ middleware/           # Express middleware
│  ├─ auth.js            # JWT authentication
│  ├─ errorHandler.js    # Global error handling
│  └─ cors.js            # CORS configuration
│
└─ utils/                # Helper functions
   ├─ validators.js
   ├─ formatters.js
   └─ logger.js
```

## Database Schema

### Core Tables

```
users
├─ id (UUID, PK)
├─ auth_id (UUID, FK to auth.users)
├─ username (VARCHAR)
├─ email (VARCHAR, UNIQUE)
├─ xp_total (INTEGER)
├─ level (INTEGER)
├─ avatar_url (VARCHAR)
├─ created_at (TIMESTAMP)
└─ updated_at (TIMESTAMP)

problems
├─ id (UUID, PK)
├─ title (VARCHAR)
├─ description (TEXT)
├─ difficulty (ENUM: easy, medium, hard)
├─ language (VARCHAR)
├─ starter_code (TEXT)
├─ test_cases (JSON)
├─ editorial (TEXT)
└─ created_at (TIMESTAMP)

submissions
├─ id (UUID, PK)
├─ user_id (UUID, FK)
├─ problem_id (UUID, FK)
├─ code (TEXT)
├─ language (VARCHAR)
├─ status (ENUM: pending, accepted, wrong_answer, time_limit)
├─ test_results (JSON)
├─ created_at (TIMESTAMP)
└─ updated_at (TIMESTAMP)

duels
├─ id (UUID, PK)
├─ player1_id (UUID, FK)
├─ player2_id (UUID, FK)
├─ problem_id (UUID, FK)
├─ winner_id (UUID, FK)
├─ player1_score (INTEGER)
├─ player2_score (INTEGER)
├─ status (ENUM: waiting, active, completed)
├─ created_at (TIMESTAMP)
├─ completed_at (TIMESTAMP)
└─ duration_seconds (INTEGER)

duel_stats
├─ user_id (UUID, PK, FK)
├─ total_duels (INTEGER)
├─ wins (INTEGER)
├─ losses (INTEGER)
├─ streak (INTEGER)
├─ rank (INTEGER)
└─ updated_at (TIMESTAMP)

roadmap
├─ id (UUID, PK)
├─ user_id (UUID, FK)
├─ topics (JSON)
├─ skills (JSON)
├─ levels (JSON)
└─ created_at (TIMESTAMP)

lessons
├─ id (UUID, PK)
├─ module_id (UUID, FK)
├─ title (VARCHAR)
├─ content (TEXT)
├─ lesson_type (ENUM: reading, quiz, coding)
├─ order (INTEGER)
└─ created_at (TIMESTAMP)

portfolio_projects
├─ id (UUID, PK)
├─ user_id (UUID, FK)
├─ project_name (VARCHAR)
├─ description (TEXT)
├─ showcase_url (VARCHAR)
├─ metrics (JSON)
└─ created_at (TIMESTAMP)
```

## Data Flow: Key User Journeys

### Journey 1: Onboarding → First Lesson

```
1. User signs up (Auth.js)
   └─> Supabase Auth.users created
       └─> Trigger: users table populated

2. User selects topics (TopicSelection.js)
   └─> POST /api/roadmap with selected topics
       └─> Backend generates roadmap
           └─> Roadmap persisted in Supabase

3. User starts first lesson (Learn.js)
   └─> GET /api/roadmap/:userId
       └─> Fetch learning path
   └─> GET /api/lessons/:lessonId
       └─> Fetch lesson content
   └─> Display and track progress
```

### Journey 2: Code Duel

```
1. User joins duel queue (CodeDuel.js)
   └─> WS: send "join-queue"
       └─> Backend: tryMatchmaking()

2. Player matched with opponent
   └─> WS: send "match" event with duel_id
   └─> Duel session created in duels table

3. User submits code (MLSandbox.js)
   └─> POST /api/submissions with code
       └─> Backend: executeCodeWithJudge0()
           └─> Judge0 API executes code
           └─> Test results returned
           └─> XP awarded if passed

4. Duel completes (first to solve or time ends)
   └─> Backend: completeDuel()
       └─> Winner determined
       └─> XP/stats updated
       └─> WS: send "duel-complete" event
```

### Journey 3: Portfolio Export

```
1. User navigates to portfolio (Portfolio.js)
   └─> GET /api/portfolio/:userId

2. User exports portfolio (SentimentAnalyzerPDF.js)
   └─> POST /api/portfolio/export
       └─> Backend generates PDF
           └─> React-PDF renderer
           └─> PDF returned to client
           └─> User downloads file
```

## Key Design Decisions

### Why Frontend/Backend Separation?

- **Flexibility:** Frontend can be deployed independently (CDN, S3)
- **Scalability:** Backend can scale separately based on API load
- **Clarity:** Clear responsibility boundary

### Why WebSockets for Duels?

- **Real-time:** Players see opponent progress live
- **Efficiency:** Reduces polling vs REST
- **Engagement:** Live chat and notifications

### Why Judge0 API?

- **Security:** Code runs in isolated Docker containers
- **Reliability:** Proven execution platform
- **Multi-language:** Supports Python, JavaScript, Java, C++
- **Scalability:** Handled by external service

### Why Supabase?

- **Backend-as-a-Service:** Reduces DevOps complexity
- **Real-time:** Built-in WebSocket subscriptions
- **Authentication:** Integrated auth with JWT
- **Database:** PostgreSQL with Row Level Security

## Extension Points

This architecture supports future enhancements:

- **Multi-agent orchestration** (pyscape-multi-agent): Extend services with orchestrator layer
- **Research experiments** (pyscape-research): Use this as baseline for A/B testing
- **Mobile app:** Reuse backend API
- **API-first design:** Easy for other frontends to consume

---

See also:
- [DEVELOPMENT.md](./DEVELOPMENT.md) — Setup and running
- [API.md](./API.md) — REST and WebSocket API reference
- [DATABASE.md](./DATABASE.md) — Schema details and migrations
