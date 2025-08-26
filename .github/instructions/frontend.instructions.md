---
applyTo: "frontend/**"
---
# Frontend Instructions

## Tech Stack
- **Language**: TypeScript
- **Framework**: Vue.js 3 (Composition API)
- **Build Tool**: Vite 6.3.5
- **Package Manager**: npm
- **Linting**: ESLint 9.27.0
- **Styling**: SASS

## Dependencies Setup

### Install Dependencies
```bash
cd frontend
npm install
```

**Always run `npm install` after pulling updates** - dependencies change frequently.

### Key Dependencies
- Vue.js 3.5.14 with Composition API
- TypeScript 5.8.3
- Vite for fast builds and dev server
- Vue Router 4.5.1 for routing
- Pinia 3.0.2 for state management
- Axios 1.9.0 for API calls
- ESLint + Prettier for code quality

## Development Workflow

### Development Server
```bash
cd frontend
npm run serve
# or
npm run dev
```
- Runs on http://localhost:5173 (Vite default)
- Hot module replacement enabled
- Auto-reloads on file changes

### Build Commands
```bash
# Development build
npm run dev

# Production build
npm run build

# Type checking (expect errors - see Known Issues)
npm run tc
```

### Code Quality
```bash
# Lint code
npx eslint .

# Check ESLint version
npx eslint --version  # Should be v9.27.0
```

## Project Structure

### Source Code Layout
```
frontend/src/
├── App.vue           # Root component
├── main.ts           # Application entry point
├── api.ts            # API client configuration
├── types.ts          # TypeScript type definitions
├── arkham/           # Game-specific components and logic
│   ├── components/   # Vue components for game UI
│   ├── views/        # Route views
│   ├── api.ts        # Arkham API calls
│   └── helpers.ts    # Utility functions
├── components/       # Generic Vue components
├── views/            # Route views
├── stores/           # Pinia stores
├── router/           # Vue Router configuration
└── assets/           # Static assets
```

### Key Files
- `frontend/package.json` - Dependencies and scripts
- `frontend/vite.config.js` - Vite build configuration
- `frontend/.eslintrc.cjs` - ESLint rules
- `frontend/tsconfig.json` - TypeScript configuration

## API Integration

### Backend Communication
- API base URL configured in `src/api.ts`
- Game API calls in `src/arkham/api.ts`
- Uses Axios for HTTP requests
- WebSocket connection for real-time game updates

### Type Safety
- TypeScript interfaces in `src/types.ts`
- Game types in `src/arkham/` files
- Decoders for API response validation

## Known Issues & Workarounds

### TypeScript Errors
**Issue**: `npm run tc` reports ~275 TypeScript errors
**Status**: Expected during active development
**Workaround**: Focus on new code being type-safe, ignore existing errors
**Root Cause**: Type definitions don't match API responses exactly

### ESLint Configuration
**Issue**: ESLint may report Vue 3 composition API issues
**Workaround**: Rules are configured in `.eslintrc.cjs` to handle Vue 3 patterns
**Note**: `vue/multi-word-component-names` is disabled

### Build Performance
- Vite dev server starts in ~2-5 seconds
- Production builds take ~10-30 seconds
- Hot reload works but may be slower with large component trees

## Testing

### Type Checking
```bash
npm run tc  # TypeScript checking (will show errors - expected)
```

### No Unit Tests
- Repository does not currently have frontend unit tests
- Manual testing via development server is primary validation method

## Component Development

### Vue 3 Patterns Used
- Composition API (not Options API)
- `<script setup>` syntax
- Pinia for state management
- Vue Router for navigation

### Styling
- SASS/SCSS support enabled
- Component-scoped styles recommended
- Global styles in `src/assets/`

## Build & Deployment

### Development Build
```bash
npm run dev  # Start development server
```

### Production Build
```bash
npm run build
# Output: frontend/dist/
```

### Build Verification
- Check `frontend/dist/` directory exists after build
- Static files should be generated
- Index.html should be present

## Docker Integration
- Frontend builds in multi-stage Dockerfile
- Uses Node.js LTS image
- Installs Vite globally for build process
- Assets copied to `/opt/arkham/src/frontend/dist`

## Environment Variables
- `VITE_ASSET_HOST` - Asset hosting URL (optional)
- Vite automatically loads `.env` files

## Troubleshooting

### Common Commands That Work
```bash
cd frontend
npm install              # Install dependencies
npm run serve           # Start dev server  
npm run build           # Build for production
npx eslint --version    # Check linting tool
```

### Common Commands That Fail
```bash
npm run tc              # TypeScript check (275+ errors expected)
```

### If Build Fails
1. Delete `node_modules/` and `package-lock.json`
2. Run `npm install`
3. Retry build command
4. Check Node.js version (should be 20+)
