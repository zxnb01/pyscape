# 🗺️ Adaptive Skill Graph Roadmap System

## Overview

The Adaptive Skill Graph Roadmap is a personalized learning system that:
- Uses a **DAG (Directed Acyclic Graph)** to enforce skill dependencies
- Tracks **user mastery** (0.0 to 1.0) for each skill
- **Unlocks skills** automatically when prerequisites are met
- **Ranks and recommends** the next best skills to learn
- Provides a **clean, uncluttered UI** for skill visualization

---

## 📁 Project Structure

```
migrations/
├── 004_create_roadmap_tables.sql    # Database schema & functions
└── 005_seed_python_skills.sql       # 30 Python skills with dependencies

src/
├── services/
│   └── roadmapService.js            # Business logic layer
├── components/
│   ├── roadmap/
│   │   └── SkillNode.js             # Individual skill card component
│   └── dashboard/
│       └── UserRoadmap.js           # Dashboard preview widget
└── pages/
    └── RoadmapPage.js               # Main roadmap visualization page
```

---

## 🗄️ Database Schema

### Tables

#### 1. `skills`
Core skill definitions
- `id` - UUID primary key
- `name` - Skill name
- `domain` - python | ml | dsa | ai
- `difficulty` - 1-5 stars
- `estimated_minutes` - Time to master
- `icon` - Emoji
- `description` - What you'll learn

#### 2. `skill_dependencies`
DAG structure (prerequisite relationships)
- `skill_id` - The skill that has dependencies
- `depends_on` - Required prerequisite skill
- **Constraint**: Prevents circular dependencies

#### 3. `user_skill_mastery`
User progress tracking
- `user_id` - User reference
- `skill_id` - Skill reference
- `mastery` - 0.0 to 1.0 (progress)
- `status` - locked | eligible | in_progress | mastered
- Timestamps: `started_at`, `completed_at`, `updated_at`

#### 4. `lesson_skills`
Maps lessons to skills they teach
- `lesson_id` - Lesson reference
- `skill_id` - Skill reference
- `contribution` - 0.0 to 1.0 (how much this lesson contributes to mastery)

### Helper Functions

#### `increment_mastery(user_id, skill_id, delta)`
Safely increments user mastery (capped at 1.0) and updates status.

#### `check_dependencies_met(user_id, skill_id)`
Returns `true` if all dependencies are mastered.

---

## 🔄 System Flow

### 1. **User completes a lesson**
```javascript
// When lesson is completed with score (0-1)
await roadmapService.updateMasteryFromLesson(userId, lessonId, score);
```

### 2. **Mastery is updated**
- Gets all skills linked to the lesson via `lesson_skills`
- For each skill: `mastery += score * contribution`
- Status updates automatically:
  - `mastery >= 0.75` → `mastered`
  - `0 < mastery < 0.75` → `in_progress`
  - `mastery == 0` → `eligible` or `locked`

### 3. **Dependencies are checked**
- For all locked skills, check if prerequisites are met
- If all dependencies are mastered → unlock skill (set status to `eligible`)

### 4. **Ranking algorithm**
Eligible skills are ranked by priority:
```javascript
priority = (1 - mastery) * 0.6 +        // Learning potential (60%)
           (difficulty / 5) * 0.2 +     // Challenge level (20%)
           Math.random() * 0.2          // Variety (20%)
```

### 5. **UI displays updated roadmap**
- Recommended: Top 5 eligible skills
- All skills: Filterable by status

---

## 🎨 UI Components

### RoadmapPage (`/app/roadmap`)
Main page features:
- **Header**: Progress circle, stats (mastered, in progress, eligible, locked)
- **Filters**: View all skills or filter by status
- **Recommended Section**: Top 5 skills for user
- **All Skills Grid**: Complete skill catalog
- **Skill Detail Modal**: Shows dependencies, linked lessons, action buttons

### SkillNode Component
Visual skill card with:
- Color coding by status:
  - 🟢 Green = Mastered
  - 🟡 Yellow = In Progress
  - 🔵 Blue = Eligible
  - ⚫ Gray = Locked
- Progress bar (mastery %)
- Difficulty stars
- Estimated time
- Click to view details

### Dashboard Widget
Quick preview showing:
- Stats overview
- Overall progress bar
- Top 3 recommended skills
- Link to full roadmap

---

## 🚀 Setup Instructions

### 1. Run Database Migrations

```sql
-- In your PostgreSQL client (or Supabase SQL editor)

-- Step 1: Create tables and functions
\i migrations/004_create_roadmap_tables.sql

-- Step 2: Seed Python skills
\i migrations/005_seed_python_skills.sql
```

### 2. Initialize User Roadmap

For a new user:
```javascript
await roadmapService.initializeUserRoadmap(userId);
```

This creates mastery records for all skills and unlocks root skills (those with no dependencies).

### 3. Link Lessons to Skills

When creating lessons in your admin panel or seeding script:
```sql
INSERT INTO lesson_skills (lesson_id, skill_id, contribution)
VALUES 
  ('lesson-uuid', 'skill-uuid', 0.3),
  ('lesson-uuid', 'another-skill-uuid', 0.2);
```

---

## 📊 Usage Examples

### Get User's Roadmap
```javascript
import roadmapService from './services/roadmapService';

const roadmap = await roadmapService.getRoadmap(userId, 'python');

console.log(roadmap.recommended);  // Top 5 skills
console.log(roadmap.stats);        // { mastered: 5, inProgress: 2, ... }
console.log(roadmap.all);          // All skills with status
```

### Update Mastery After Lesson
```javascript
// User completes lesson with 90% score
await roadmapService.updateMasteryFromLesson(userId, lessonId, 0.9);

// This will:
// 1. Update mastery for linked skills
// 2. Check for newly unlocked skills
// 3. Update rankings
```

### Get Skill Details
```javascript
const skill = await roadmapService.getSkillDetails(skillId);

console.log(skill.dependencies);   // Prerequisites
console.log(skill.lesson_skills);  // Related lessons
```

---

## 🎯 Current Status

### ✅ Completed (Week 1)
- [x] Database schema with DAG enforcement
- [x] 30 Python skills with dependencies
- [x] Helper functions (increment_mastery, check_dependencies_met)
- [x] Roadmap service layer
- [x] RoadmapPage UI with filtering
- [x] SkillNode component with animations
- [x] Dashboard widget integration
- [x] Navigation setup

### 🔜 Next Steps (Week 2-4)

#### Week 2: Integration
- [ ] Link existing lessons to skills via `lesson_skills` table
- [ ] Trigger mastery updates on lesson completion
- [ ] Test full flow: complete lesson → unlock skill → UI updates
- [ ] Add skill detail page with lesson list

#### Week 3: Enhancement
- [ ] Add "Start Learning" button that routes to first lesson
- [ ] Implement skill search & filtering
- [ ] Add visual DAG graph (optional)
- [ ] Mastery decay for inactive skills (optional)

#### Week 4: Polish & Scale
- [ ] Add ML/DSA/AI domain skills
- [ ] Performance optimization for large graphs
- [ ] Add animations for skill unlocking
- [ ] Admin panel for skill management

---

## 🔧 Configuration

### Toggle Development Mode
The system works with the dev bypass in `supabaseClient.js`. To use real data:

```javascript
// In supabaseClient.js
const DEV_BYPASS = false;  // Set to false for production
```

### Customize Priority Ranking
Edit the `_calculatePriority` method in `roadmapService.js`:

```javascript
_calculatePriority(skill) {
  const masteryGap = 1 - skill.mastery;
  const difficultyScore = skill.difficulty / 5;
  
  return (
    masteryGap * 0.6 +      // Adjust weights here
    difficultyScore * 0.2 +
    Math.random() * 0.2
  );
}
```

---

## 🐛 Troubleshooting

### Skills not unlocking?
Check dependencies are mastered:
```sql
SELECT s.name, usm.mastery, usm.status
FROM skills s
JOIN user_skill_mastery usm ON s.id = usm.skill_id
WHERE usm.user_id = 'your-user-id'
  AND s.id IN (
    SELECT depends_on FROM skill_dependencies WHERE skill_id = 'locked-skill-id'
  );
```

### Roadmap not loading?
1. Check browser console for errors
2. Verify tables exist in database
3. Check `DEV_BYPASS` is enabled if testing locally
4. Ensure user has mastery records initialized

### Circular dependency detected?
Run verification query from `005_seed_python_skills.sql`:
```sql
WITH RECURSIVE skill_path AS (
  SELECT skill_id, depends_on, ARRAY[skill_id, depends_on] as path
  FROM skill_dependencies
  UNION ALL
  SELECT sp.skill_id, sd.depends_on, sp.path || sd.depends_on
  FROM skill_path sp
  JOIN skill_dependencies sd ON sp.depends_on = sd.skill_id
  WHERE sd.depends_on = ANY(sp.path)
)
SELECT * FROM skill_path WHERE depends_on = ANY(path);
```

---

## 📝 API Reference

### RoadmapService Methods

#### `getRoadmap(userId, domain)`
Returns personalized roadmap with recommended and all skills.

**Parameters:**
- `userId` (string) - User ID
- `domain` (string) - 'python' | 'ml' | 'dsa' | 'ai'

**Returns:**
```javascript
{
  recommended: Skill[],      // Top 5 eligible skills
  all: Skill[],              // All skills with status
  stats: {                   // Progress statistics
    total, mastered, inProgress, eligible, locked,
    percentComplete, averageMastery
  },
  dependencies: Object       // Dependency map for visualization
}
```

#### `updateMasteryFromLesson(userId, lessonId, score)`
Updates mastery for all skills linked to a lesson.

#### `checkAndUnlockSkills(userId)`
Checks all locked skills and unlocks those with met dependencies.

#### `initializeUserRoadmap(userId)`
Creates mastery records for all published skills.

#### `getSkillDetails(skillId)`
Gets full details for a skill including dependencies and lessons.

---

## 🎓 Learning Path Example

A typical user journey:

1. **Start**: User has Python Syntax, Math Operations, Strings mastered
2. **Unlocked**: Boolean Logic becomes eligible
3. **Progress**: User completes lessons → Boolean Logic mastery increases
4. **Unlock**: At 75% mastery → if-else statements unlock
5. **Recommended**: System suggests if-else as top priority
6. **Continue**: Pattern repeats, building toward advanced skills

The DAG ensures logical progression: Loops → Lists → Functions → OOP → Advanced patterns.

---

## 🤝 Contributing

When adding new skills:

1. Add to `skills` table with domain, difficulty, description
2. Define dependencies in `skill_dependencies`
3. Verify no circular dependencies
4. Link relevant lessons via `lesson_skills`
5. Test unlock flow

---

## 📚 References

- **Graph Theory**: Skills form a DAG (Directed Acyclic Graph)
- **Mastery Learning**: Threshold-based progression (75% = mastered)
- **Adaptive Systems**: Personalized recommendations based on current state
- **UI/UX**: Duolingo-style visual progression

---

**Built with:** React, Supabase (PostgreSQL), Framer Motion

**License:** MIT

**Version:** 1.0.0 (Week 1 Complete)
