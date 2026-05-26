# PyScape-Basic: Development Guide

## Quick Start

### Prerequisites

- **Node.js** v16+ (for both frontend and backend)
- **Python** 3.8+ (for code execution testing)
- **Git** (for version control)
- **npm** or **yarn** (for package management)

### 1. Clone Repository

```bash
git clone https://github.com/yourusername/pyscape-basic.git
cd pyscape-basic
```

### 2. Set Up Environment Variables

Copy environment templates and fill in your credentials:

```bash
cp .env.example .env
```

Required variables in `.env`:

```env
# Supabase
REACT_APP_SUPABASE_URL=https://your-project.supabase.co
REACT_APP_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key

# Judge0 API (for code execution)
JUDGE0_API_HOST=https://judge0-ce.p.rapidapi.com
JUDGE0_API_KEY=your-rapidapi-key

# Backend
BACKEND_PORT=5000
WS_PORT=8080
NODE_ENV=development

# Frontend
REACT_APP_BACKEND_URL=http://localhost:5000
REACT_APP_WS_URL=ws://localhost:8080
```

### 3. Frontend Setup

```bash
# Navigate to frontend
cd frontend

# Install dependencies
npm install

# Start development server (runs on http://localhost:3000)
npm start
```

### 4. Backend Setup

```bash
# Navigate to backend (in a new terminal)
cd backend

# Install dependencies
npm install

# Start REST API server (runs on http://localhost:5000)
npm run dev

# In another terminal, start WebSocket server (runs on http://localhost:8080)
npm run dev:duel
```

### 5. Database Setup (Supabase)

The database is hosted on Supabase. Migrations are automatically applied.

To manually run migrations:

```bash
cd database

# Create tables (run once)
psql -h your-supabase-host -U postgres -d postgres -f migrations/001_create_core_tables.sql
psql -h your-supabase-host -U postgres -d postgres -f migrations/002_create_duel_tables.sql
# ... run other migrations

# Seed test data
psql -h your-supabase-host -U postgres -d postgres -f seeds/dummy_problems.sql
```

---

## Project Structure Overview

```
pyscape-basic/
├─ frontend/              # React frontend application
│  ├─ src/
│  │  ├─ pages/          # Feature-based page components
│  │  ├─ components/     # Reusable UI components
│  │  ├─ features/       # Feature-specific logic
│  │  ├─ context/        # State management
│  │  ├─ services/       # API services
│  │  ├─ utils/          # Helper functions
│  │  ├─ hooks/          # Custom React hooks
│  │  ├─ styles/         # Global styles
│  │  └─ assets/         # Images, icons
│  ├─ package.json
│  ├─ tailwind.config.js
│  └─ postcss.config.js
│
├─ backend/               # Express backend API
│  ├─ src/
│  │  ├─ routes/         # API routes and WS handlers
│  │  ├─ middleware/     # Express middleware
│  │  ├─ services/       # Business logic
│  │  ├─ models/         # Database models
│  │  ├─ config/         # Configuration
│  │  └─ utils/          # Helper functions
│  ├─ server.js          # REST API entry point
│  ├─ duel-server.js     # WebSocket server entry point
│  ├─ package.json
│  └─ .env.example
│
├─ database/              # Database schema and migrations
│  ├─ migrations/        # SQL migration files
│  └─ seeds/             # Data seeding scripts
│
├─ docs/                  # Documentation
│  ├─ ARCHITECTURE.md    # System design
│  ├─ API.md             # REST/WS API reference
│  ├─ DATABASE.md        # Schema documentation
│  └─ FEATURES/          # Feature-specific docs
│
└─ scripts/               # Utility scripts
```

---

## Running Different Servers

### Option A: Run Everything Locally

**Terminal 1 - Frontend:**
```bash
cd frontend
npm start
```

**Terminal 2 - Backend REST API:**
```bash
cd backend
npm run dev
```

**Terminal 3 - Backend WebSocket:**
```bash
cd backend
npm run dev:duel
```

Then visit `http://localhost:3000`

### Option B: Production Build

```bash
# Build frontend
cd frontend
npm run build

# Start backend with production setting
cd ../backend
NODE_ENV=production npm start
```

---

## Common Development Tasks

### Adding a New Page

1. Create page component in `frontend/src/pages/[feature]/NewPage.js`
2. Add route in `frontend/src/App.js`
3. Link from navigation

### Adding a New API Endpoint

1. Create route handler in `backend/src/routes/api/[resource].js`
2. Add to `backend/server.js` routing
3. Document in `docs/API.md`
4. Create service/model files as needed

### Adding a WebSocket Event

1. Create event handler in `backend/src/routes/ws/[feature]-events.js`
2. Register in `backend/duel-server.js`
3. Use from frontend in component
4. Document in `docs/API.md`

### Database Changes

1. Create migration file: `database/migrations/NNN_description.sql`
2. Number sequentially
3. Test locally with Supabase
4. Document schema changes in `docs/DATABASE.md`

---

## Testing

### Frontend Tests

```bash
cd frontend
npm test
```

### Backend Tests

```bash
cd backend
npm test
```

---

## Code Style & Linting

### Frontend Formatting

```bash
cd frontend
npm run lint
npm run format
```

### Backend Formatting

```bash
cd backend
npm run lint
npm run format
```

---

## Debugging

### Debug Frontend (Chrome DevTools)

1. Frontend runs on http://localhost:3000
2. Open Chrome DevTools (F12)
3. Use Console, Network, React DevTools tabs

### Debug Backend (Node Inspector)

```bash
# Start with inspector
cd backend
node --inspect server.js

# Open chrome://inspect in Chrome
```

### View Database

Access Supabase dashboard:
1. Visit https://app.supabase.co
2. Select your project
3. Browse tables in "SQL Editor"

---

## Troubleshooting

### Port Already in Use

```bash
# Kill process on port 3000
lsof -ti:3000 | xargs kill -9

# Or change port
PORT=3001 npm start
```

### Supabase Connection Issues

```bash
# Check connection string
# Verify REACT_APP_SUPABASE_URL and keys in .env
# Test with curl
curl -H "Authorization: Bearer $SUPABASE_ANON_KEY" \
     "https://your-project.supabase.co/rest/v1/users"
```

### Judge0 API Errors

- Verify API key in backend `.env`
- Check RapidAPI subscription is active
- Verify request format in backend logs

---

## Useful Commands

```bash
# Frontend
npm start               # Dev server
npm run build          # Production build
npm test               # Run tests
npm run lint           # Check code style

# Backend
npm run dev            # Dev server with hot reload
npm start              # Production server
npm run dev:duel       # WebSocket server
npm test               # Run tests
npm run lint           # Check code style

# Database
npm run migrate        # Run migrations
npm run seed           # Seed test data

# Git
git status             # Check changes
git add .              # Stage all changes
git commit -m "msg"    # Commit changes
git push               # Push to remote
git pull               # Pull latest
```

---

See also:
- [ARCHITECTURE.md](./ARCHITECTURE.md) — System design
- [API.md](./API.md) — API reference
- [DATABASE.md](./DATABASE.md) — Database schema
- [CONTRIBUTING.md](../CONTRIBUTING.md) — Contribution guidelines
