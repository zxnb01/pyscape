# PyScape-Basic: API Reference

## Overview

PyScape-Basic provides both REST and WebSocket APIs for frontend-backend communication.

- **REST API** — Stateless queries, data fetching (runs on port 5000)
- **WebSocket API** — Real-time events (runs on port 8080)

## Base URLs

```
REST API:      http://localhost:5000
WebSocket:     ws://localhost:8080
Authentication: Bearer Token (Supabase JWT)
```

---

## Authentication

All requests require a valid Supabase JWT token:

```javascript
// Frontend example
const token = session.access_token;
const headers = {
  Authorization: `Bearer ${token}`
};
```

---

## REST API Endpoints

### Authentication

#### `POST /api/auth/login`
Login with email and password.

**Request:**
```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "user": { "id": "uuid", "email": "user@example.com" },
  "session": { "access_token": "jwt_token" }
}
```

#### `POST /api/auth/signup`
Create a new user account.

**Request:**
```json
{
  "email": "user@example.com",
  "password": "password123",
  "username": "john_dev"
}
```

**Response:**
```json
{
  "user": { "id": "uuid", "email": "user@example.com", "username": "john_dev" },
  "session": { "access_token": "jwt_token" }
}
```

#### `POST /api/auth/logout`
Logout current user session.

**Response:**
```json
{ "message": "Logout successful" }
```

---

### Problems

#### `GET /api/problems?difficulty=easy&language=python`
Fetch coding problems with optional filters.

**Query Parameters:**
- `difficulty` — `easy`, `medium`, `hard`
- `language` — `python`, `javascript`, `java`, `cpp`
- `tags` — Comma-separated skill tags
- `limit` — Number of results (default: 20)
- `offset` — Pagination offset (default: 0)

**Response:**
```json
{
  "problems": [
    {
      "id": "uuid",
      "title": "Two Sum",
      "description": "Given an array of integers...",
      "difficulty": "easy",
      "language": "python",
      "tags": ["arrays", "hash-table"]
    }
  ],
  "total": 150
}
```

#### `GET /api/problems/:id`
Fetch a specific problem.

**Response:**
```json
{
  "id": "uuid",
  "title": "Two Sum",
  "description": "Given an array of integers...",
  "difficulty": "easy",
  "starter_code": "def twoSum(nums, target):\n    # Your code here",
  "tags": ["arrays", "hash-table"]
}
```

Note: `solution_code` and `test_cases` are NOT returned to users.

---

### Submissions

#### `POST /api/submissions`
Submit code for a problem.

**Request:**
```json
{
  "problem_id": "uuid",
  "code": "def twoSum(nums, target):\n    # Solution code",
  "language": "python"
}
```

**Response:**
```json
{
  "id": "uuid",
  "status": "accepted",
  "runtime_ms": 45,
  "memory_kb": 12800,
  "test_results": [
    { "test_case": 1, "passed": true, "output": "[0,1]" },
    { "test_case": 2, "passed": true, "output": "[1,2]" }
  ],
  "xp_earned": 100
}
```

**Possible Status Values:**
- `accepted` — All tests passed, XP awarded
- `wrong_answer` — Test output mismatch
- `compile_error` — Syntax error
- `timeout` — Execution exceeded time limit
- `runtime_error` — Exception during execution

#### `GET /api/submissions?limit=10`
Get user's submission history.

**Response:**
```json
{
  "submissions": [
    {
      "id": "uuid",
      "problem_id": "uuid",
      "problem_title": "Two Sum",
      "status": "accepted",
      "created_at": "2025-06-01T10:30:00Z"
    }
  ]
}
```

---

### Users

#### `GET /api/users/:id`
Get user profile information.

**Response:**
```json
{
  "id": "uuid",
  "username": "john_dev",
  "email": "john@example.com",
  "xp_total": 1500,
  "level": 3,
  "avatar_url": "https://...",
  "interests": ["Python", "Machine Learning"],
  "created_at": "2025-01-15T00:00:00Z"
}
```

#### `PUT /api/users/:id`
Update user profile.

**Request:**
```json
{
  "username": "john_dev_pro",
  "avatar_url": "https://..."
}
```

**Response:**
```json
{
  "id": "uuid",
  "username": "john_dev_pro",
  "avatar_url": "https://..."
}
```

---

### Roadmap

#### `POST /api/roadmap`
Generate personalized learning roadmap from topics.

**Request:**
```json
{
  "topics": ["Python Basics", "Machine Learning", "Data Science"]
}
```

**Response:**
```json
{
  "id": "uuid",
  "topics": ["Python Basics", "Machine Learning", "Data Science"],
  "skills": {
    "Python Basics": {
      "level": 1,
      "lessons": ["intro_to_python", "variables_types", "control_flow"]
    },
    "Machine Learning": {
      "level": 2,
      "lessons": ["supervised_learning", "unsupervised_learning"]
    }
  },
  "levels": [
    { "level": 1, "xp_required": 0 },
    { "level": 2, "xp_required": 500 }
  ]
}
```

#### `GET /api/roadmap/:userId`
Fetch user's roadmap.

**Response:**
```json
{
  "id": "uuid",
  "topics": ["Python Basics", "Machine Learning"],
  "skills": { ... },
  "levels": [ ... ],
  "generated_at": "2025-06-01T00:00:00Z"
}
```

---

### Lessons

#### `GET /api/lessons/:lessonId`
Fetch lesson content and questions.

**Response:**
```json
{
  "id": "uuid",
  "title": "Introduction to Python",
  "type": "reading",
  "content": "# Intro\nPython is a high-level...",
  "quiz_questions": [
    {
      "id": "q1",
      "question": "What is Python?",
      "options": ["A", "B", "C"],
      "correct_answer": 0
    }
  ]
}
```

#### `POST /api/lessons/:lessonId/complete`
Mark lesson as complete and earn XP.

**Response:**
```json
{
  "xp_earned": 50,
  "new_total_xp": 1550,
  "level_up": false
}
```

---

### Duels

#### `GET /api/duel/stats`
Get current user's duel statistics.

**Response:**
```json
{
  "total_duels": 25,
  "wins": 18,
  "losses": 7,
  "win_rate": 0.72,
  "current_streak": 3,
  "best_streak": 8,
  "rank": 42
}
```

#### `GET /api/duel/leaderboard?limit=100`
Get global leaderboard.

**Response:**
```json
{
  "leaderboard": [
    {
      "rank": 1,
      "username": "pro_coder",
      "wins": 250,
      "win_rate": 0.85
    },
    {
      "rank": 2,
      "username": "john_dev",
      "wins": 180,
      "win_rate": 0.72
    }
  ]
}
```

---

### Portfolio

#### `GET /api/portfolio/:userId`
Get user's portfolio.

**Response:**
```json
{
  "user": { "username": "john_dev", "xp_total": 1500 },
  "projects": [
    {
      "id": "uuid",
      "project_name": "Sentiment Analyzer",
      "description": "ML project analyzing text sentiment",
      "skills": ["Python", "NLP", "Scikit-learn"],
      "metrics": { "accuracy": 0.87, "f1_score": 0.85 },
      "created_at": "2025-05-20T00:00:00Z"
    }
  ]
}
```

#### `POST /api/portfolio/export`
Export portfolio as PDF.

**Request:**
```json
{
  "format": "pdf"
}
```

**Response:**
- Returns PDF file as binary

---

## WebSocket API

### Connection

```javascript
// Frontend example
const ws = new WebSocket('ws://localhost:8080');
ws.onopen = () => {
  // Send auth message
  ws.send(JSON.stringify({
    type: 'auth',
    token: 'jwt_token'
  }));
};
```

---

### Duel Events

#### Client → Server: `join-queue`
Join matchmaking queue.

```json
{
  "type": "join-queue",
  "difficulty": "intermediate",
  "language": "python"
}
```

#### Server → Client: `match-found`
Opponent found, duel starting.

```json
{
  "type": "match-found",
  "duel_id": "uuid",
  "opponent": {
    "username": "opponent_user",
    "level": 5
  },
  "problem": {
    "id": "uuid",
    "title": "Two Sum",
    "description": "Given an array...",
    "time_limit_seconds": 900
  },
  "starts_at": "2025-06-01T10:30:30Z"
}
```

#### Client → Server: `code-submit`
Submit code during duel.

```json
{
  "type": "code-submit",
  "duel_id": "uuid",
  "code": "def twoSum(nums, target):\n    ..."
}
```

#### Server → Client: `code-result`
Result of code submission.

```json
{
  "type": "code-result",
  "player": "self",
  "status": "accepted",
  "passed_tests": 5,
  "total_tests": 5,
  "xp_earned": 100
}
```

#### Server → Client: `opponent-progress`
Real-time opponent progress.

```json
{
  "type": "opponent-progress",
  "opponent": "opponent_user",
  "passed_tests": 3,
  "total_tests": 5,
  "status": "solving"
}
```

#### Client → Server: `forfeit`
Abandon duel.

```json
{
  "type": "forfeit",
  "duel_id": "uuid"
}
```

#### Server → Client: `duel-complete`
Duel finished.

```json
{
  "type": "duel-complete",
  "winner": "opponent_user",
  "player1": {
    "username": "john_dev",
    "passed_tests": 3,
    "time_seconds": 245
  },
  "player2": {
    "username": "opponent_user",
    "passed_tests": 5,
    "time_seconds": 180
  }
}
```

---

### Chat Events

#### Client → Server: `send-message`
Send chat message during duel.

```json
{
  "type": "send-message",
  "duel_id": "uuid",
  "message": "Good luck!"
}
```

#### Server → Client: `receive-message`
Receive message from opponent.

```json
{
  "type": "receive-message",
  "from": "opponent_user",
  "message": "Thanks! You too!",
  "timestamp": "2025-06-01T10:32:15Z"
}
```

---

## Error Responses

All endpoints return consistent error format:

```json
{
  "error": "Description of error",
  "code": "ERROR_CODE",
  "status": 400
}
```

**Common Error Codes:**
- `UNAUTHORIZED` (401) — Missing/invalid JWT
- `FORBIDDEN` (403) — Access denied
- `NOT_FOUND` (404) — Resource not found
- `INVALID_INPUT` (400) — Bad request data
- `INTERNAL_ERROR` (500) — Server error

**Example:**
```json
{
  "error": "Problem not found",
  "code": "NOT_FOUND",
  "status": 404
}
```

---

## Rate Limiting

API calls are rate-limited to prevent abuse:

- **Authenticated users:** 1000 requests/hour
- **Per IP:** 100 requests/minute

Rate limit headers included in responses:
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1625097600
```

---

See also:
- [DEVELOPMENT.md](./DEVELOPMENT.md) — Setup guide
- [ARCHITECTURE.md](./ARCHITECTURE.md) — System design
- [DATABASE.md](./DATABASE.md) — Data schema
