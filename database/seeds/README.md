# PyScape-Basic: Hidden Test Cases & Seed Data

This directory contains hidden test cases for problems and seed data used during development.

## Structure

```
seeds/
├── dummy_problems.sql      # Sample coding problems with hidden test cases
└── python_skills.sql       # Python skill tree for roadmap generation
```

## Purpose

These seed files help:
- **Development:** Test the system with real data
- **Testing:** Populate test databases
- **Demos:** Show system with populated data

## Running Seeds

```bash
# Using psql
psql -h [your-host] -U postgres -d [database] -f database/seeds/dummy_problems.sql
psql -h [your-host] -U postgres -d [database] -f database/seeds/python_skills.sql

# Using Supabase SQL Editor
# 1. Go to https://app.supabase.co
# 2. Open SQL Editor
# 3. Create new query
# 4. Copy-paste content from seed file
# 5. Run query
```

## Important Notes

⚠️ Seed data is for testing/development only.

⚠️ Do not use in production without customization.

⚠️ Hidden test cases stay in database — not sent to frontend.

---

See [../docs/DATABASE.md](../docs/DATABASE.md) for schema details.
