# Database Directory

This directory contains all database-related files for PyScape-Basic.

## Structure

```
database/
├── migrations/          # SQL migration files (run in sequence)
└── seeds/              # Seed data for testing and development
```

## Migrations

Migration files are numbered sequentially and should be run in order:

```bash
001_create_core_tables.sql      # Core user, problem, submission tables
002_create_duel_tables.sql      # Duel-related tables
003_seed_dummy_problem.sql      # Initial test problems
004_create_roadmap_tables.sql   # Roadmap and lesson tracking
005_seed_python_skills.sql      # Python skill tree
```

### Running Migrations

Using Supabase SQL Editor:
1. Go to https://app.supabase.co
2. Select your project
3. Go to "SQL Editor"
4. Click "New Query"
5. Copy-paste migration content
6. Click "Run"

Or via command line:
```bash
psql -h [your-host] -U postgres -d postgres -f migrations/001_create_core_tables.sql
```

## Seeds

Seed files populate the database with test data for development:

```bash
database/seeds/
├── dummy_problems.sql      # Sample coding problems
└── python_skills.sql       # Python skill tree for roadmap
```

Run seeds after migrations:
```bash
psql -h [your-host] -U postgres -d postgres -f seeds/dummy_problems.sql
```

## Schema Reference

See [../docs/DATABASE.md](../docs/DATABASE.md) for:
- Table schemas
- Relationships
- Indexes
- Row-Level Security policies

## Important Notes

⚠️ **Always back up production data before migrations**

⚠️ **Test migrations locally before production**

⚠️ **Never delete migrations** — only add new ones

## Adding New Migrations

1. Create file: `NNN_description.sql` (increment the number)
2. Write SQL with clear comments
3. Test locally with Supabase
4. Commit to git
5. Document changes in [../docs/DATABASE.md](../docs/DATABASE.md)

Example:
```bash
touch database/migrations/007_add_user_achievements.sql
```

---

See [../docs/DATABASE.md](../docs/DATABASE.md) for detailed schema documentation.
