# Contributing to Deepgram Starters

Thank you for your interest in contributing to Deepgram starters! This document provides guidelines for contributing to any starter application in the `deepgram-starters` organization.

## Quick Links

- [Management Repository](https://github.com/deepgram-starters/manage)
- [Deepgram Documentation](https://developers.deepgram.com)
- [Community Discussions](https://github.com/orgs/deepgram/discussions)
- [Discord Community](https://discord.gg/deepgram)

## Code of Conduct

All contributors are expected to follow our Code of Conduct. Please read it before contributing.

## Getting Started

### Prerequisites

Each starter has specific requirements based on its framework. Common requirements include:

- **For Node.js starters:** Node.js 24.0.0+, pnpm 10.0.0+ (via corepack)
- **For Python starters:** Python 3.10+, pip or poetry
- **For Go starters:** Go 1.21+
- **All starters:** Git, Make (for Makefile commands)

### Setting Up Development Environment

1. **Fork and clone the repository:**
   ```bash
   git clone --recursive https://github.com/YOUR_USERNAME/REPO_NAME.git
   cd REPO_NAME
   ```

2. **Initialize the project:**
   ```bash
   make init
   ```

3. **Set up environment variables:**
   ```bash
   cp sample.env .env
   # Edit .env and add your DEEPGRAM_API_KEY
   ```

4. **Start development servers:**
   ```bash
   make dev
   ```

5. **Access the application:**
   ```
   http://localhost:8080
   ```

## Architecture Standards

All Deepgram starters follow consistent patterns. Please review these documentation files in the [management repository](https://github.com/deepgram-starters/manage):

- **CLAUDE.md** - Framework-agnostic standards (required reading)
- **CLAUDE.rest.md** - REST API patterns (for prerecorded demos)
- **CLAUDE.websocket.md** - WebSocket patterns (for streaming demos)

### Key Standards

1. **Port Conventions:**
   - Port 8080: Main application (user-facing)
   - Port 8081: Frontend dev server (internal)

2. **Environment Variables:**
   - `DEEPGRAM_API_KEY` (required)
   - `PORT` (default: 8080)
   - `HOST` (default: 0.0.0.0)
   - `VITE_PORT` (default: 8081)

3. **Makefile Commands:**
   All starters must include: `init`, `dev`, `start`, `build`, `install`, `install-frontend`, `clean`, `update`, `status`

4. **Metadata Endpoint:**
   All starters must implement `GET /api/metadata` reading from `deepgram.toml`

5. **Error Response Format:**
   ```json
   {
     "error": {
       "type": "ValidationError" | "ProcessingError",
       "code": "ERROR_CODE",
       "message": "Human-readable message",
       "details": { "originalError": "..." }
     }
   }
   ```

6. **Dependency Versions:**
   Use exact versions (no `^` or `~` semver ranges)

## Types of Contributions

### 🐛 Bug Fixes

1. Check existing issues to avoid duplicates
2. Create an issue describing the bug
3. Fork the repository and create a branch: `fix/issue-description`
4. Fix the bug following existing code patterns
5. Test thoroughly in both dev and prod modes
6. Submit a pull request

### ✨ New Features

1. Open an issue to discuss the feature first
2. Ensure the feature aligns with the starter's scope
3. Get approval from maintainers before starting work
4. Implement following documented patterns
5. Update documentation if needed
6. Submit a pull request

### 📚 Documentation

Documentation improvements are always welcome:
- README updates
- Code comments
- Example improvements
- Error message clarification

### 🧪 Testing

Help improve test coverage:
- Add integration tests
- Test edge cases
- Verify cross-platform compatibility

## Development Workflow

### Branching Strategy

- `main` - Stable, production-ready code
- `feature/*` - New features
- `fix/*` - Bug fixes
- `docs/*` - Documentation updates
- `refactor/*` - Code refactoring

### Commit Messages

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat` - New feature
- `fix` - Bug fix
- `docs` - Documentation changes
- `style` - Code style changes (formatting, etc.)
- `refactor` - Code refactoring
- `perf` - Performance improvements
- `test` - Adding or updating tests
- `chore` - Maintenance tasks, dependency updates
- `ci` - CI/CD changes
- `build` - Build system changes

**Examples:**
```
feat(api): add sentiment analysis to text intelligence endpoint
fix(websocket): resolve race condition in connection cleanup
docs(readme): clarify environment setup instructions
refactor(config): simplify API key loading logic
```

### Pull Request Process

1. **Update your fork:**
   ```bash
   git fetch upstream
   git rebase upstream/main
   ```

2. **Test thoroughly:**
   ```bash
   make clean
   make init
   make dev    # Test development mode
   make build
   make start  # Test production mode
   ```

3. **Create pull request:**
   - Clear title describing the change
   - Reference related issues
   - Describe what changed and why
   - Include screenshots for UI changes
   - List breaking changes (if any)

4. **Code review:**
   - Address reviewer feedback
   - Keep discussion professional and constructive
   - Update your PR based on feedback

5. **Merge:**
   - PRs require approval from maintainers
   - Squash commits before merging
   - Delete branch after merge

## Reference Implementation

**Node.js starters are the source of truth** for all patterns. When implementing or updating starters in other frameworks:

1. Find the corresponding Node.js starter (e.g., `node-transcription`)
2. Review its implementation
3. Adapt patterns to your framework's idioms
4. Maintain functional parity
5. Follow the same API contracts

## Framework-Specific Guidelines

### Node.js
- Use CommonJS (`require`/`module.exports`)
- Use pnpm via corepack
- Include `--no-deprecation` flag in scripts
- Exact dependency versions

### Python (Flask/Django/FastAPI)
- Use `requirements.txt` with exact versions
- Use `python-dotenv` for environment variables
- Follow PEP 8 style guide
- Use type hints where appropriate

### Go
- Use Go modules (`go.mod`)
- Follow Go conventions and idioms
- Use `godotenv` for environment variables
- Include proper error handling

### Other Frameworks
- Maintain consistency with documented patterns
- Adapt to framework best practices
- Ensure functional parity with Node.js version

## WebSocket Starters (Critical)

WebSocket starters MUST implement graceful shutdown:

```javascript
// Node.js example
const activeConnections = new Set();

function gracefulShutdown(signal) {
  console.log(`${signal} received. Closing connections...`);
  activeConnections.forEach((ws) => {
    if (ws.readyState === WebSocket.OPEN) {
      ws.close(1000, "Server shutting down");
    }
  });
  // Close servers and exit
}

process.on("SIGTERM", () => gracefulShutdown("SIGTERM"));
process.on("SIGINT", () => gracefulShutdown("SIGINT"));
```

## Testing Your Changes

Before submitting a PR, verify:

- [ ] `make init` successfully initializes project
- [ ] `make dev` starts both backend and frontend
- [ ] Application works at `http://localhost:8080` in dev mode
- [ ] Hot reload works for code changes
- [ ] `make build` produces static files
- [ ] `make start` serves production build
- [ ] Production mode works identically to dev mode
- [ ] No console errors in browser
- [ ] `/api/metadata` endpoint returns correct data
- [ ] API endpoints handle errors properly
- [ ] No sensitive data (API keys) in commits
- [ ] Code follows project patterns and style

## Questions or Problems?

- **Issues:** Use the issue templates
- **Discussions:** [GitHub Discussions](https://github.com/orgs/deepgram/discussions)
- **Discord:** [Join our Discord](https://discord.gg/deepgram)
- **Documentation:** [developers.deepgram.com](https://developers.deepgram.com)

## License

By contributing, you agree that your contributions will be licensed under the same license as the project (typically MIT).

---

Thank you for contributing to Deepgram starters! 🎉
