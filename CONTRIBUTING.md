# Contributing to Full-Stack Template

Thank you for wanting to contribute to the Full-Stack Template! This guide explains the standards and philosophy used throughout this project.

## Documentation Philosophy

All code in this template must follow these commenting principles to ensure it remains beginner-friendly and maintainable.

### Commenting Standards

Every file must follow these principles:

1. **Section-level comments only** - Group related code and explain the group's purpose
2. **Plain language** - Write like you're explaining to a friend over coffee
3. **Relatable analogies** - Use restaurant, house, office building metaphors
4. **Explain "why" not just "what"** - Help readers understand decisions
5. **Keep it concise** - Few sentences maximum per section
6. **No line-by-line comments** unless something is genuinely confusing

### Good vs Bad Comments

**Good comment:**
```
# Redirect visitors from the regular entrance (HTTP) to the secure entrance (HTTPS)
# Think of this like a sign saying "please use the main door"
```

**Bad comment:**
```
# HTTP to HTTPS redirect on port 80
```

**Good comment:**
```
# Don't start serving food until the kitchen is ready
# This ensures the app container is running before nginx tries to send requests to it
```

**Bad comment:**
```
# Container dependency declaration
```

### What to Comment

**DO comment:**
- Major configuration sections
- Non-obvious design decisions
- Relationships between services
- Common gotchas or mistakes
- Security or performance considerations

**DON'T comment:**
- Self-explanatory variable names
- Standard boilerplate
- Single obvious lines
- Things that are clear from context

## Architecture & Analogies

This template uses consistent analogies throughout to explain technical concepts:

| Component | Analogy | Explanation |
|-----------|---------|-------------|
| **Nginx** | Front door / Bouncer | Handles all incoming traffic, manages security (SSL/HTTPS) |
| **Next.js** | Kitchen and dining room | Handles both backend (API) and frontend (UI) |
| **Redis** | Whiteboard / Order ticket rail | Fast, temporary storage for sessions and caching |
| **Supabase** | Filing cabinet | Permanent data storage (database) |
| **Docker Volumes** | Storage unit | Persistent storage that survives container restarts |
| **Networks** | Internal phone system | Containers communicate privately, only Nginx faces outside |
| **Workers** | Staff members | More CPU cores = more workers to handle requests |

When adding comments, use these analogies to explain concepts in everyday language.

## Making Changes to the Template

### Adding a New API Endpoint

1. Create file in `app/app/api/[feature]/route.ts`
2. Follow the pattern from `app/app/api/health/route.ts`
3. Add a comprehensive header comment explaining:
   - What the endpoint does
   - What it checks or calculates
   - What success (200) vs error (500) responses look like
4. Use plain language in error messages

Example:
```typescript
// ============================================================================
// User Profile Endpoint - GET /api/user/profile
// ============================================================================
// Retrieves the logged-in user's profile from the filing cabinet (Supabase)
// Returns user details like name, email, preferences
// Returns 200 if successful, 401 if not authenticated, 500 if database error

export async function GET(request: Request) {
  // ... implementation
}
```

### Modifying Docker Configuration

1. Edit the relevant `docker-compose*.yml` file
2. Add section comments (using `# ====...`) for major changes
3. Explain:
   - What service or setting changed
   - Why the change was made
   - How it affects development or production

Example:
```yaml
  # ====================================================================
  # Database Service - PostgreSQL
  # ====================================================================
  # Added PostgreSQL for structured data storage
  # Complements Supabase for local development testing
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_PASSWORD: dev-password
```

### Adding a New Service to Docker

1. Add service definition to `docker-compose.yml`
2. Include comprehensive header comment explaining:
   - What the service does (use analogies)
   - Why it's needed
   - How it connects to other services
3. Add health check if applicable
4. Document any new environment variables in `.env.example`

Example:
```yaml
  # ====================================================================
  # Queue Service - Redis Queues
  # ====================================================================
  # Like a message board for background jobs
  # Allows the kitchen to process orders without the customer waiting
  # Tasks like "send email" or "generate report" run separately

  redis-queue:
    image: redis:7-alpine
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
```

### Updating Configuration Files

When modifying `.js`, `.json`, `.ts`, `.tsx`, or other config files:

1. Maintain existing comment structure and style
2. Add section-level comments for new sections (use `====` separators)
3. Explain non-obvious settings with plain language
4. Update `.env.example` if new environment variables are needed
5. Don't remove comments - code changes, but the "why" is permanent

### Adding Dependencies

1. **Modify `app/package.json`** - Add to dependencies or devDependencies
2. **Update documentation** in comments if it's a major library
3. **Test locally** - Run `npm install` and start the app
4. **Document in README.md** if the library needs user-facing setup

### Maintaining Code Quality

Before submitting changes:

1. **Run linter** - `npm run lint` should have no errors
2. **Check comments** - Follow the commenting standards above
3. **Test locally** - Run `docker-compose up` and verify everything works
4. **Use plain language** - Avoid jargon, explain technical terms
5. **Follow the analogies** - Stay consistent with the restaurant/building metaphors

## Success Criteria for Contributions

Your changes should allow developers to:

1. **Understand why** - Comments explain not just what the code does, but why it's built that way
2. **Modify it confidently** - Plain language means less guessing about side effects
3. **Debug issues** - Clear comments and structure make troubleshooting easier
4. **Stay beginner-friendly** - No jargon, use analogies, explain decisions

## Testing Your Changes

After making changes:

```bash
# Install dependencies
cd app && npm install

# Start the development stack
docker-compose -f docker-compose.yml -f docker-compose.dev.yml up

# Verify the app works
# - Open http://localhost in browser
# - Check http://localhost/api/health
# - Monitor logs: docker-compose logs -f app

# Stop when done
docker-compose down
```

## Questions?

Refer to:
- **For usage instructions:** See [README.md](./README.md)
- **For code examples:** Look at existing files in the project - they follow these standards
- **For architecture decisions:** Read comments in `docker-compose.yml` and Dockerfiles

## Tone and Style

Throughout all contributions:
- Be encouraging and supportive
- Use "we" and "you" (not "one must")
- Assume the reader is smart but may be new to some concepts
- Use concrete examples over abstract explanations
- Prefer simple words over technical terms
- When technical terms are necessary, briefly explain them

Thank you for contributing to making this template accessible to developers of all levels!
