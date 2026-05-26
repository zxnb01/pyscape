# Backend Directory

Express.js backend API for PyScape-Basic.

## Structure

```
backend/
├── src/
│   ├── routes/          # API routes and WebSocket handlers
│   ├── middleware/      # Express middleware
│   ├── services/        # Business logic
│   ├── models/          # Database models
│   ├── config/          # Configuration
│   └── utils/           # Helper functions
├── server.js            # REST API entry point (port 5000)
├── duel-server.js       # WebSocket server entry point (port 8080)
├── package.json
└── .env.example
```

## Running

```bash
npm install         # Install dependencies
npm run dev         # Start REST API server (http://localhost:5000)
npm run dev:duel    # Start WebSocket server (ws://localhost:8080)
npm start           # Production mode
npm test            # Run tests
```

## Architecture

- **Routes** — REST API endpoints and WebSocket event handlers
- **Services** — Business logic (code execution, matchmaking, gamification)
- **Models** — Database model definitions
- **Middleware** — Auth, CORS, error handling
- **Config** — Database, API keys, environment variables

### REST API Routes

```
/api/auth/*         — Authentication (login, signup, logout)
/api/problems/*     — Problem listing and fetching
/api/submissions/*  — Code submission and results
/api/users/*        — User profiles and stats
/api/duel/*         — Duel statistics and leaderboard
/api/roadmap/*      — Learning path generation
/api/portfolio/*    — Portfolio data and export
```

### WebSocket Events

Real-time events for code duels and chat:

```
join-queue          — Enter matchmaking
match-found         — Opponent matched, duel starting
code-submit         — Submit code for execution
code-result         — Submission result
opponent-progress   — Opponent's test pass count
duel-complete       — Duel finished
send-message        — Chat message to opponent
```

## Configuration

Create `.env` file with:

```env
# Supabase
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_SERVICE_ROLE_KEY=your-service-key

# Judge0 API (code execution)
JUDGE0_API_HOST=https://judge0-ce.p.rapidapi.com
JUDGE0_API_KEY=your-rapidapi-key

# Server
BACKEND_PORT=5000
WS_PORT=8080
NODE_ENV=development
```

## Key Technologies

- **Express.js** — Web framework
- **ws** — WebSocket library
- **@supabase/supabase-js** — Supabase client
- **axios** — HTTP client
- **helmet** — Security headers
- **cors** — CORS middleware

## Development

See [../docs/DEVELOPMENT.md](../docs/DEVELOPMENT.md) for:
- Complete setup guide
- Running servers
- Adding API endpoints
- Testing

See [../docs/API.md](../docs/API.md) for:
- REST API reference
- WebSocket event reference
- Request/response formats

---

Start building! 🚀
