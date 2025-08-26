# Backend Instructions

**applyTo: "backend/**"**

## Tech Stack
- **Language**: Haskell
- **Framework**: Yesod web framework
- **Build Tool**: Stack 3.7.1+
- **Compiler**: GHC 9.12.2
- **Database**: PostgreSQL
- **Package Management**: Stack + Cabal

## Project Structure

### Haskell Packages
```
backend/
├── arkham-api/        # Main API server (Yesod application)
├── cards-discover/    # Card discovery microservice
├── validate/          # Game state validation service
├── stack.yaml         # Stack configuration
├── Makefile          # Build automation
└── cabal.project     # Cabal multi-package project
```

### Key Configuration Files
- `backend/stack.yaml` - Stack build configuration (resolver: nightly-2025-07-31)
- `backend/.hlint.yaml` - HLint linting rules and language extensions
- `backend/fourmolu.yaml` - Code formatting configuration
- `backend/arkham-api/package.yaml` - Main package dependencies
- `backend/hie.yaml` - Haskell Language Server configuration

## Dependencies Setup

### System Dependencies
**Required before building:**
```bash
# Ubuntu/Debian
sudo apt-get install -y libpcre3 libpcre3-dev libpq-dev
```

### Haskell Setup
```bash
cd backend

# First time setup (downloads GHC if needed)
stack setup

# Install Yesod CLI (optional but recommended)
stack install yesod-bin --install-ghc

# Build dependencies only (SLOW - 10-30 minutes)
stack build --dependencies-only --fast --system-ghc
```

**ALWAYS use `--system-ghc` flag** - required for CI compatibility.

## Build Commands

### Development Server
```bash
cd backend
make api.watch
```
- Builds and runs API server with file watching
- Auto-restarts on code changes
- Runs on default Yesod port (usually 3000)
- Uses DEVELOPMENT=true environment variable

### Alternative Development Commands
```bash
# Watch with tests
make api.watch.test

# Manual development server
cd arkham-api && stack exec -- yesod devel
```

### Build Commands
```bash
# Fast development build
stack build --fast --system-ghc

# Full build (all packages)
stack build --system-ghc cards-discover validate arkham-api

# Build with tests (but don't run)
stack build --test --no-run-tests --system-ghc
```

## Testing

### Run Tests
```bash
cd backend
make test
# or
cd arkham-api && stack test --system-ghc
```

### Test Configuration
```bash
# Run with specific flags (matches development environment)
stack test --flag arkham-horror-backend:library-only --flag arkham-horror-backend:dev --system-ghc
```
**Important**: Use the same flags as `yesod devel` to avoid recompilation.

### Validation Service
```bash
make validate        # Run validation once
make validate.watch  # Run with file watching
```

## Code Quality

### Linting with HLint
```bash
# Lint all code
stack exec -- hlint .

# Lint specific package
cd arkham-api && stack exec -- hlint src/
```

### Code Formatting with Fourmolu
```bash
# Format all code
stack exec -- fourmolu --mode inplace $(find . -name '*.hs')

# Check formatting
stack exec -- fourmolu --mode check $(find . -name '*.hs')
```

### Generate Tags (for navigation)
```bash
make tags
```

## Database Setup

### PostgreSQL Setup
```bash
# Create user and databases
createuser arkham-horror-backend --password arkham-horror-backend --superuser
createdb arkham-horror-backend
createdb arkham-horror-backend_test
```

### Run Migrations
```bash
cd migrations
sqitch deploy db:pg:arkham-horror-backend
```

### Manual Migration (if no Sqitch)
```bash
cd migrations
cat deploy/* | psql arkham-horror-backend
```
**Note**: Run `users` and `arkham_games` tables first if doing manual migration.

## REPL and Debugging

### GHCi REPL
```bash
make ghci
# or
stack ghci arkham-api:exe:arkham-api --test --no-run-tests --system-ghc
```

### Profiling Build
```bash
make api.prof
```

## Package-Specific Commands

### arkham-api (Main Server)
```bash
cd arkham-api
stack build --fast --system-ghc arkham-api
stack exec arkham-api
```

### cards-discover Service
```bash
cd cards-discover  
stack build --fast --system-ghc cards-discover
stack exec cards-discover
```

### validate Service
```bash
cd validate
stack build --fast --system-ghc validate
stack exec validate
```

## Known Issues & Workarounds

### Network Issues
**Issue**: Stack may fail with "ConnectionTimeout" when downloading packages
**Workaround**: Retry the command - network issues are transient
**Example**: Stackage snapshot downloads may timeout

### Build Performance
**Issue**: First build is extremely slow (30+ minutes)
**Workaround**: Use `--fast` flag for development builds
**Reason**: GHC optimization and large dependency tree

### Missing System Libraries
**Issue**: Build fails with missing libpcre or libpq
**Solution**: Install system dependencies (see Dependencies Setup)
**Required**: libpcre3-dev, libpq-dev are essential

### GHC Version Issues
**Issue**: Stack may try to download wrong GHC version
**Solution**: Always use `--system-ghc` flag
**Note**: CI uses system GHC, so local builds should match

## Docker Build

### Multi-stage Docker Build
- Uses Ubuntu 22.04 base image
- Installs GHC 9.12.2, Cabal 3.16.0.0, Stack 3.7.1
- Caches Stack builds for performance
- Final binary in `/opt/arkham/bin/arkham-api`

### Docker Build Flags
```bash
# In Dockerfile, Stack uses:
--system-ghc --no-terminal --ghc-options '-j4 +RTS -A128m -n2m -RTS'
```

## Environment Variables

### Development
- `DEVELOPMENT=true` - Enables development mode
- `DATABASE_URL` - PostgreSQL connection string
- `PORT=3000` - Server port

### GHC Options Used
```haskell
# From stack.yaml
-O0                                 # No optimization in development
-fwrite-ide-info -hiedir=.hie      # IDE support
-fhide-source-paths                # Clean output
-fignore-optim-changes             # Faster rebuilds
```

## Troubleshooting

### Commands That Always Work
```bash
cd backend
stack --version                    # Check Stack version
stack setup --system-ghc          # Setup GHC
make api.watch                     # Start development server
stack exec -- hlint .             # Lint code
```

### Commands That May Fail
```bash
stack build --dependencies-only   # May timeout on network issues
stack test                        # May fail if DB not setup
```

### If Build Completely Fails
1. Delete `.stack-work` directories: `find . -name .stack-work -exec rm -rf {} +`
2. Check system dependencies are installed
3. Retry with `--system-ghc` flag
4. Check PostgreSQL is running and accessible

### Stack Build Order
1. First build `cards-discover`
2. Then build `arkham-api` (depends on shared libraries)
3. Finally build `validate`

Use Makefile targets (`make api.watch`) which handle the correct build order automatically.