# Scripts Directory

Utility scripts for setup, development, and maintenance.

## Available Scripts

```bash
scripts/
├── setup.sh              # Quick setup script
├── migrate.sh            # Run database migrations
├── seed.sh               # Populate seed data
└── dev.sh                # Start all dev servers
```

## Usage

### Quick Setup

```bash
./scripts/setup.sh
```

This runs:
- Install frontend dependencies
- Install backend dependencies
- Set up environment files
- Initialize database

### Database Migrations

```bash
./scripts/migrate.sh
```

Runs all SQL migrations in order.

### Seed Test Data

```bash
./scripts/seed.sh
```

Populates database with sample problems and skill trees.

### Start All Dev Servers

```bash
./scripts/dev.sh
```

Starts:
- Frontend (port 3000)
- Backend REST API (port 5000)
- Backend WebSocket (port 8080)

---

See [../docs/DEVELOPMENT.md](../docs/DEVELOPMENT.md) for manual setup steps.
