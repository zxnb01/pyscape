# PyScape-Basic: Database Schema & Migrations

## Overview

PyScape-Basic uses **Supabase** (PostgreSQL) as its database. All data is stored in normalized tables with Row-Level Security (RLS) policies for security.

## Running Migrations

### First Time Setup

```bash
# Connect to your Supabase database
psql -h [YOUR_SUPABASE_HOST] -U postgres -d postgres

# Run migrations in order:
\i database/migrations/001_create_core_tables.sql
\i database/migrations/002_create_duel_tables.sql
\i database/migrations/003_seed_dummy_problem.sql
\i database/migrations/004_create_roadmap_tables.sql
\i database/migrations/005_seed_python_skills.sql
```

Or use the Supabase SQL Editor:
1. Go to SQL Editor in Supabase dashboard
2. Create a new query
3. Copy-paste migration SQL
4. Execute

## Core Tables

### `users`

Stores user profile and progress information.

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY REFERENCES auth.users(id),
  username VARCHAR(255) UNIQUE NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  xp_total INTEGER DEFAULT 0,
  level INTEGER DEFAULT 1,
  avatar_url VARCHAR(500),
  bio TEXT,
  interests JSONB,                    -- Topics selected during onboarding
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

**Fields:**
- `id` — Linked to Supabase Auth user ID
- `username` — Unique user display name
- `email` — User email
- `xp_total` — Cumulative experience points
- `level` — User level (calculated from XP)
- `avatar_url` — Profile picture
- `interests` — Selected topics from onboarding (JSON array)

**Example:**
```json
{
  "id": "uuid",
  "username": "john_dev",
  "email": "john@example.com",
  "xp_total": 1500,
  "level": 3,
  "interests": ["Python", "Machine Learning", "Data Science"]
}
```

---

### `problems`

Stores coding challenge problems.

```sql
CREATE TABLE problems (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title VARCHAR(255) NOT NULL,
  description TEXT NOT NULL,
  difficulty VARCHAR(50),             -- easy, medium, hard
  language VARCHAR(50),                -- python, javascript, java, cpp
  starter_code TEXT,
  solution_code TEXT,                  -- Hidden from users
  test_cases JSONB,                    -- Array of {input, expected_output}
  editorial TEXT,                      -- Solution explanation
  tags JSONB,                          -- Problem categories/skills
  category VARCHAR(100),
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

**Fields:**
- `test_cases` — JSON array with hidden test inputs/outputs
- `tags` — Problem categorization (e.g., ["arrays", "sorting"])
- `editorial` — Solution walkthrough

**Example:**
```json
{
  "id": "uuid",
  "title": "Two Sum",
  "description": "Given an array of integers, return indices of two numbers...",
  "difficulty": "easy",
  "language": "python",
  "test_cases": [
    {"input": "[2,7,11,15], target=9", "expected": "[0,1]"},
    {"input": "[3,2,4], target=6", "expected": "[1,2]"}
  ],
  "tags": ["arrays", "hash-table"]
}
```

---

### `submissions`

Stores code submissions and execution results.

```sql
CREATE TABLE submissions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id),
  problem_id UUID NOT NULL REFERENCES problems(id),
  code TEXT NOT NULL,
  language VARCHAR(50),
  status VARCHAR(50),                  -- pending, accepted, wrong_answer, timeout
  runtime_ms INTEGER,
  memory_kb INTEGER,
  test_results JSONB,                  -- Array of {passed: bool, output: string}
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

**Fields:**
- `status` — Compilation and execution result
- `test_results` — Detailed pass/fail for each test case
- `runtime_ms` — Execution time in milliseconds

---

### `duels`

Stores real-time 1v1 coding battles.

```sql
CREATE TABLE duels (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  player1_id UUID NOT NULL REFERENCES users(id),
  player2_id UUID NOT NULL REFERENCES users(id),
  problem_id UUID NOT NULL REFERENCES problems(id),
  winner_id UUID REFERENCES users(id),
  player1_score INTEGER DEFAULT 0,
  player2_score INTEGER DEFAULT 0,
  status VARCHAR(50),                  -- waiting, active, completed
  started_at TIMESTAMP,
  completed_at TIMESTAMP,
  duration_seconds INTEGER,
  created_at TIMESTAMP DEFAULT NOW()
);
```

**Fields:**
- `status` — Duel lifecycle (waiting for start, active, finished)
- `player1_score` / `player2_score` — Test cases passed (out of total)
- `winner_id` — NULL if draw/timeout

---

### `duel_submissions`

Stores code submissions during duels for replay/analysis.

```sql
CREATE TABLE duel_submissions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  duel_id UUID NOT NULL REFERENCES duels(id),
  user_id UUID NOT NULL REFERENCES users(id),
  code TEXT NOT NULL,
  language VARCHAR(50),
  submitted_at TIMESTAMP DEFAULT NOW(),
  test_results JSONB
);
```

---

### `duel_stats`

Aggregated leaderboard statistics (updated via triggers).

```sql
CREATE TABLE duel_stats (
  user_id UUID PRIMARY KEY REFERENCES users(id),
  total_duels INTEGER DEFAULT 0,
  wins INTEGER DEFAULT 0,
  losses INTEGER DEFAULT 0,
  win_rate FLOAT,                      -- Calculated: wins/total_duels
  current_streak INTEGER DEFAULT 0,
  best_streak INTEGER DEFAULT 0,
  rank INTEGER,                        -- Leaderboard position
  updated_at TIMESTAMP DEFAULT NOW()
);
```

---

### `roadmap`

Stores personalized learning paths generated from user interests.

```sql
CREATE TABLE roadmap (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL UNIQUE REFERENCES users(id),
  topics JSONB,                        -- Selected topics (e.g., ["Python", "ML"])
  skills JSONB,                        -- Hierarchical skill tree
  modules JSONB,                       -- Sequenced learning modules
  levels JSONB,                        -- Levels and their requirements
  generated_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

**Example Structure:**
```json
{
  "topics": ["Python", "Machine Learning"],
  "skills": {
    "Python Basics": {
      "level": 1,
      "lessons": ["intro", "variables", "loops"]
    },
    "ML Fundamentals": {
      "level": 2,
      "lessons": ["supervised", "unsupervised", "evaluation"]
    }
  },
  "levels": [
    {"level": 1, "xp_required": 0, "skills": ["Python Basics"]},
    {"level": 2, "xp_required": 500, "skills": ["ML Fundamentals"]}
  ]
}
```

---

### `lessons`

Stores learning content (reading, quizzes, coding challenges).

```sql
CREATE TABLE lessons (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  module_id VARCHAR(100) NOT NULL,
  title VARCHAR(255) NOT NULL,
  description TEXT,
  content TEXT,                        -- Markdown content
  lesson_type VARCHAR(50),             -- reading, quiz, coding
  difficulty VARCHAR(50),              -- beginner, intermediate, advanced
  problem_id UUID REFERENCES problems(id),  -- If type = coding
  quiz_questions JSONB,                -- If type = quiz
  lesson_order INTEGER,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

---

### `lesson_progress`

Tracks individual user progress through lessons.

```sql
CREATE TABLE lesson_progress (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id),
  lesson_id UUID NOT NULL REFERENCES lessons(id),
  status VARCHAR(50),                  -- not_started, in_progress, completed
  xp_earned INTEGER,
  completion_date TIMESTAMP,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

---

### `portfolio_projects`

Stores user portfolio projects for showcasing.

```sql
CREATE TABLE portfolio_projects (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id),
  project_name VARCHAR(255) NOT NULL,
  description TEXT,
  skills JSONB,                        -- Skills demonstrated
  metrics JSONB,                       -- Performance, accuracy, time taken
  code_url VARCHAR(500),               -- GitHub link
  demo_url VARCHAR(500),               -- Live demo link
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

---

## Row-Level Security (RLS)

All tables have RLS policies to ensure users can only access their own data:

```sql
-- Example: Users can only view/edit their own profile
ALTER TABLE users ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view own profile"
ON users FOR SELECT
USING (auth.uid() = id);

CREATE POLICY "Users can update own profile"
ON users FOR UPDATE
USING (auth.uid() = id);
```

---

## Indexes for Performance

```sql
-- Speed up problem lookups
CREATE INDEX idx_problems_difficulty ON problems(difficulty);
CREATE INDEX idx_problems_language ON problems(language);

-- Speed up submission queries
CREATE INDEX idx_submissions_user_id ON submissions(user_id);
CREATE INDEX idx_submissions_problem_id ON submissions(problem_id);

-- Speed up duel queries
CREATE INDEX idx_duels_player1_id ON duels(player1_id);
CREATE INDEX idx_duels_player2_id ON duels(player2_id);
CREATE INDEX idx_duels_status ON duels(status);

-- Speed up leaderboard
CREATE INDEX idx_duel_stats_rank ON duel_stats(rank);
```

---

## Seed Data

Seed data is provided in `database/seeds/`:

### `dummy_problems.sql`
Inserts sample problems for testing.

### `python_skills.sql`
Inserts Python skill tree for roadmap generation.

To apply:
```bash
psql -h [HOST] -U postgres -d postgres -f database/seeds/dummy_problems.sql
```

---

## Backup & Recovery

### Manual Backup

```bash
pg_dump -h [HOST] -U postgres [DATABASE] > backup.sql
```

### Restore from Backup

```bash
psql -h [HOST] -U postgres [DATABASE] < backup.sql
```

### Supabase Automatic Backups

Supabase provides automatic daily backups. Access via dashboard:
1. Go to Supabase dashboard
2. Click "Settings" → "Database"
3. View backups under "Backup Schedule"

---

## Adding New Migrations

### Steps:

1. **Create migration file** with sequence number:
   ```bash
   touch database/migrations/NNN_description.sql
   ```

2. **Write SQL** with clear comments:
   ```sql
   -- Migration NNN: Add new feature
   -- Date: YYYY-MM-DD
   
   CREATE TABLE new_table (
     id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
     ...
   );
   ```

3. **Test locally** before committing

4. **Document** changes in this file

5. **Commit** to git with clear message:
   ```bash
   git commit -m "migration: NNN add new feature"
   ```

---

## Common Queries

### Get user's learning progress

```sql
SELECT 
  l.title,
  lp.status,
  lp.xp_earned,
  lp.completion_date
FROM lessons l
JOIN lesson_progress lp ON l.id = lp.lesson_id
WHERE lp.user_id = 'USER_ID'
ORDER BY l.lesson_order;
```

### Get duel leaderboard

```sql
SELECT 
  u.username,
  ds.wins,
  ds.losses,
  ds.win_rate,
  ds.rank
FROM duel_stats ds
JOIN users u ON ds.user_id = u.id
ORDER BY ds.rank ASC
LIMIT 100;
```

### Get submission results for a problem

```sql
SELECT 
  u.username,
  s.status,
  s.runtime_ms,
  s.created_at
FROM submissions s
JOIN users u ON s.user_id = u.id
WHERE s.problem_id = 'PROBLEM_ID'
ORDER BY s.created_at DESC;
```

---

See also:
- [ARCHITECTURE.md](./ARCHITECTURE.md) — System design
- [API.md](./API.md) — API endpoints
- [DEVELOPMENT.md](./DEVELOPMENT.md) — Setup guide
