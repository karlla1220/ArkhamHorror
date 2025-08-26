# Arkham Horror LCG - Codebase Instructions

## Repository Overview

This repository implements a web-based version of the Arkham Horror: The Card Game with comprehensive rules implementation. It consists of a frontend SPA and a backend API server.

**Tech Stack:**
- **Frontend**: Vue.js 3 + TypeScript + Vite
- **Backend**: Haskell + Yesod web framework + PostgreSQL
- **Database**: PostgreSQL with Sqitch migrations
- **Build Tools**: Stack (Haskell), npm/Vite (Frontend), Docker (Deployment)
- **Repository Size**: Large (~50K+ lines of code across Haskell, TypeScript, Vue)

## Architecture & Project Layout

### Directory Structure
```
/
├── frontend/          # Vue.js/TypeScript SPA (applies to frontend.instructions.md)
├── backend/           # Haskell API server (applies to backend.instructions.md)
│   ├── arkham-api/    # Main API server package
│   ├── cards-discover/ # Card discovery service
│   ├── validate/      # Game state validation
│   └── Makefile       # Backend build commands
├── migrations/        # Database schema (Sqitch)
├── docs/              # Documentation
├── .github/workflows/ # CI/CD pipelines
├── Dockerfile         # Production deployment
├── docker-compose.yml # Development environment
└── Makefile          # Root-level commands

```

### Key Configuration Files
- `frontend/package.json` - Frontend dependencies and scripts
- `frontend/.eslintrc.cjs` - ESLint configuration for TypeScript/Vue
- `frontend/vite.config.js` - Vite build configuration
- `backend/stack.yaml` - Haskell package manager configuration
- `backend/.hlint.yaml` - Haskell linting rules
- `backend/fourmolu.yaml` - Haskell code formatting rules
- `migrations/sqitch.conf` - Database migration configuration

## Which Part to Modify

**For frontend changes (UI, client-side logic, API consumption):**
- Modify files in `frontend/`
- Use frontend.instructions.md for build/test guidance

**For backend changes (API endpoints, game logic, database):**
- Modify files in `backend/`
- Use backend.instructions.md for build/test guidance

**For database schema changes:**
- Add migrations in `migrations/`
- Use Sqitch for migration management

## Prerequisites

**Required Tools:**
- Docker (for quick setup)
- Node.js 20+ and npm (for frontend)
- Haskell Stack 3.7.1+ (for backend)
- PostgreSQL 14+ (for database)
- Sqitch (optional, for migrations)

## Quick Start Options

### Option 1: Docker (Recommended for Testing)
```bash
docker compose up
# Access at http://localhost:3000
```

### Option 2: Full Development Setup
```bash
# Database setup (one-time)
createuser arkham-horror-backend --password arkham-horror-backend --superuser
createdb arkham-horror-backend

# Start backend
cd backend && make api.watch

# Start frontend (separate terminal)
cd frontend && npm install && npm run serve
```

## Continuous Integration

### GitHub Actions Workflows
- `.github/workflows/haskell.yml` - Backend CI (builds, tests Haskell code)
- `.github/workflows/hadolint.yml` - Docker linting

### CI Pipeline Steps
1. Install system dependencies (libpcre3, libpcre3-dev)
2. Setup Haskell Stack with GHC 9.12.2
3. Build dependencies: `stack build --system-ghc --dependencies-only`
4. Build projects: `stack build --system-ghc`
5. Run tests: `stack test --system-ghc`

**Important**: The CI uses `--system-ghc` flag consistently for all Stack commands.

## Common Issues & Workarounds

### Network/Dependency Issues
- If Stack fails with network timeouts, retry the command
- Frontend may have TypeScript errors - this is expected during development
- Use `--system-ghc` flag with Stack commands for consistency with CI

### Build Performance
- Backend builds are slow (Haskell compilation) - expect 10-30 minutes for full build
- Use `make api.watch` for incremental backend builds
- Frontend builds are fast (~10-30 seconds)

### Database Issues
- Always create both `arkham-horror-backend` and `arkham-horror-backend_test` databases
- Use migrations in `migrations/` directory for schema changes
- Database user needs superuser privileges for testing

## Code Quality Tools

### Frontend
- **Linting**: ESLint v9.27.0 (`npx eslint`)
- **Type Checking**: TypeScript via vue-tsc
- **Build**: Vite

### Backend  
- **Linting**: HLint with custom configuration
- **Formatting**: Fourmolu
- **Build**: Stack with GHC 9.12.2

## Trust These Instructions

These instructions contain the validated commands and workflows for this repository. Only search for additional information if these instructions are incomplete or found to be incorrect. Most build and development workflows are documented here.