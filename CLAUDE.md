# Full-Stack Template - Build Instructions for Claude Code

## Project Overview

Build a reusable full-stack template that can be cloned and configured for any web application project. This template connects four essential pieces that work together: Nginx (traffic manager), Next.js (application), Redis (fast cache), and Supabase (database). Everything runs in Docker containers that work identically on a developer's laptop and in production.

## Target Audience for Generated Code

The code you generate will be read by developers of varying experience levels, including career changers and self-taught programmers. All comments should use everyday language and relatable analogies. Avoid unnecessary jargon.

## File Structure to Create

```
fullstack-template/
├── README.md
├── .env.example
├── .gitignore  
├── docker-compose.yml
├── docker-compose.dev.yml
├── docker-compose.prod.yml
├── nginx/
│   ├── nginx.conf
│   └── Dockerfile
├── app/
│   ├── Dockerfile
│   ├── Dockerfile.dev
│   ├── package.json
│   ├── next.config.js
│   ├── tsconfig.json
│   ├── tailwind.config.js
│   ├── postcss.config.js
│   ├── .eslintrc.json
│   ├── app/
│   │   ├── globals.css
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   └── api/
│   │       └── health/
│   │           └── route.ts
│   ├── lib/
│   │   ├── supabase.ts
│   │   └── redis.ts
│   └── public/
│       └── .gitkeep
└── scripts/
    ├── setup.sh
    └── deploy.sh
```

## Documentation Philosophy

### Commenting Standards

Every file you create must follow these commenting principles:

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

## Root Level Files

### README.md

Create a comprehensive README with the following sections:

1. **What This Template Does** - One paragraph explaining the complete setup
2. **The Four Pieces** - Short explanation of each service using analogies
3. **Quick Start** - Step-by-step commands to get running
4. **Project Structure** - Brief overview of main directories
5. **Development Workflow** - How to work with the template locally
6. **Deployment** - How to push to production
7. **Customization Guide** - What to change for new projects
8. **Troubleshooting** - Common issues and fixes

Use friendly, encouraging language. This should feel like a helpful guide, not technical documentation.

### .env.example

Create a template environment file with clear section comments for:

- **General Settings** - NODE_ENV, APP_URL, etc.
- **Supabase Configuration** - URL and keys with explanation of where to get them
- **Redis Configuration** - Connection string with explanation
- **Security** - Session secrets with generation instructions

Each variable should have an inline comment explaining what it controls and what happens if configured incorrectly.

### .gitignore

Create a comprehensive gitignore for Next.js, Docker, and common development files.

Add a header comment explaining:
- Why we ignore certain files
- Never commit secrets or credentials
- Build artifacts don't belong in version control

## Docker Configuration Files

### docker-compose.yml

Create the main orchestration file defining all four services.

**Add clear section comments for:**

1. **Volumes Section**
   - Explain volumes are storage that survives container restarts
   - Compare to having a storage unit that persists even when you renovate the building
   - List what each volume stores and why

2. **Networks Section**
   - Explain this creates a private communication channel
   - Like an internal phone system - containers can call each other but outsiders cannot
   - Only nginx exposes ports to the outside world

3. **Nginx Service**
   - Explain this is the "front door" or "bouncer"
   - Handles all incoming traffic
   - Terminates SSL (the padlock in browsers)
   - Routes requests to the app
   - Explain port mappings (80 and 443)

4. **App Service**
   - Explain this is your actual application (the kitchen and dining room)
   - Runs Next.js which handles both frontend and backend
   - Lists all environment variables it receives
   - Depends on redis being ready first

5. **Redis Service**
   - Explain this is fast temporary storage (the whiteboard or order ticket rail)
   - Why we use alpine (smaller and faster)
   - The appendonly flag and what it means for data persistence
   - Why we don't expose ports (only internal access)

### docker-compose.dev.yml

Create development-specific overrides.

**Add section comment explaining:**
- This file adds extra features for local development
- Gets merged with base docker-compose.yml
- Key differences: hot-reload, exposed ports for debugging, mounted volumes

**Add subsection comments for:**
- Volume mounts (explain instant code updates without rebuilding)
- Port exposure (explain direct access for debugging)
- Different Dockerfile usage

### docker-compose.prod.yml

Create production-specific overrides.

**Add section comment explaining:**
- This file optimizes for production deployment
- Security and performance over convenience
- Gets merged with base docker-compose.yml

**Add subsection comments for:**
- Resource limits (prevent one service from using all memory/CPU)
- Restart policies (automatic recovery from crashes)
- No volume mounts (code baked into image)

## Nginx Configuration

### nginx/nginx.conf

Create a production-ready reverse proxy configuration.

**Add major section comments for:**

1. **Worker Configuration**
   - Explain worker_processes auto adjusts to CPU cores
   - Think of workers as staff members - more cores = more staff
   - Worker_connections is how many customers each worker handles simultaneously

2. **HTTP Block - Basic Settings**
   - MIME types and file handling
   - Logging setup (access logs = customer orders, error logs = problems)
   - Performance optimizations with plain language explanations

3. **GZIP Compression**
   - Explain this makes pages smaller for faster loading
   - Like vacuum-sealing food for transport
   - List which file types get compressed and why

4. **Upstream Block**
   - Explain this points to the Next.js application
   - Like having the kitchen's phone number
   - Why it's "app:3000" (Docker internal networking)

5. **HTTP Server (Port 80)**
   - Explain this redirects everyone to HTTPS
   - Security best practice - always use the secure door
   - Simple redirect, no actual content served here

6. **HTTPS Server (Port 443)**
   - Main entrance for all traffic
   - SSL certificate configuration
   - Explain each proxy_set_header and why it matters
   - WebSocket support section
   - Static file handling with caching explanation
   - Health check endpoint

### nginx/Dockerfile

Create a simple Dockerfile for Nginx.

**Add header comment explaining:**
- Why we use nginx:alpine (small and efficient)
- What gets copied into the container
- Why the configuration is read-only (:ro flag)

## Next.js Application

### app/Dockerfile

Create a multi-stage production Dockerfile.

**Add section comments for each stage:**

1. **Base Stage**
   - Explain we're starting with Node.js 20
   - Alpine variant is smaller (faster downloads, less disk space)
   - This becomes the foundation for other stages

2. **Dependencies Stage**
   - Explain this stage gathers all the tools we need
   - Like collecting ingredients before cooking
   - Why we use npm ci with frozen-lockfile (exact versions, no surprises)

3. **Builder Stage**
   - Explain this stage compiles and optimizes the code
   - Like actually cooking the meal
   - Takes source code and produces production-ready files
   - Why we copy dependencies from previous stage (efficiency)

4. **Runner Stage**
   - Explain this is the final deliverable
   - Only includes what's needed to run (not source code or dev tools)
   - Why we create a non-root user (security best practice)
   - What gets copied and what gets left behind

### app/Dockerfile.dev

Create a development-focused Dockerfile.

**Add header comment explaining:**
- Development version includes hot-reload and debugging tools
- Prioritizes fast iteration over small image size
- Installs ALL dependencies including devDependencies
- Mounts source code as volume so changes appear instantly

### app/package.json

Create with all necessary dependencies for a modern Next.js app.

**Add a comment block at the top explaining:**
- This is your project's shopping list of tools and libraries
- Scripts section defines commands you can run
- Dependencies are needed in production, devDependencies only during development
- Versions are locked to ensure consistency

Include these key dependencies:
- next, react, react-dom
- @supabase/supabase-js
- redis
- iron-session (for secure session management)

Include these devDependencies:
- typescript, @types packages
- tailwindcss, autoprefixer, postcss
- eslint, eslint-config-next

### app/next.config.js

Create Next.js configuration.

**Add section comments for:**
- Standalone output (explain: packages everything needed into one folder, makes Docker images smaller)
- Image optimization settings
- Any other non-default settings and their purpose

### app/tsconfig.json

Create TypeScript configuration for Next.js.

**Add a header comment explaining:**
- TypeScript catches errors before they reach production
- This config is optimized for Next.js App Router
- Path aliases make imports cleaner

### app/tailwind.config.js

Create Tailwind CSS configuration.

**Add header comment explaining:**
- Tailwind is a utility-first CSS framework
- This config tells Tailwind where to look for classes
- Content array must include all files that use Tailwind

### app/postcss.config.js

Create PostCSS configuration.

**Add header comment explaining:**
- PostCSS processes CSS before it reaches the browser
- Tailwind and Autoprefixer run through PostCSS
- Autoprefixer adds browser-specific CSS prefixes automatically

### app/.eslintrc.json

Create ESLint configuration extending Next.js defaults.

**Add a header comment explaining:**
- ESLint catches code quality issues and bugs
- This extends Next.js recommended rules
- Catches common mistakes early

### app/app/globals.css

Create global styles with Tailwind directives.

**Add section comments for:**
- Tailwind base, components, utilities imports
- Any custom global styles
- CSS variables if used

### app/app/layout.tsx

Create root layout component.

**Add a comprehensive header comment explaining:**
- This wraps around every page in the application
- Good place for navigation, footer, anything that appears everywhere
- Think of it as the restaurant's walls, floor, and ceiling
- Metadata defined here applies to all pages by default

**Add a subsection comment for:**
- The metadata export (SEO and browser tab information)

### app/app/page.tsx

Create a simple homepage component.

**Add header comment explaining:**
- This is the landing page (what people see at your root URL)
- Starting point for building your actual application
- Replace this content with your project-specific UI

### app/app/api/health/route.ts

Create a health check API endpoint.

**Add comprehensive header comment explaining:**
- What health checks are (like a doorbell to verify someone's home)
- Why monitoring tools need this (to know if the app is working)
- What this endpoint checks (Redis and Supabase connectivity)
- Returns 200 if healthy, 500 if something is broken

**Add subsection comments for:**
- Redis ping check
- Supabase connection check
- Error handling and response structure

### app/lib/supabase.ts

Create Supabase client connection helper.

**Add comprehensive header comment explaining:**
- This is how we talk to the "filing cabinet" (permanent storage)
- We create the connection once and reuse it everywhere
- Two clients: regular (for normal operations) and admin (for elevated permissions)

**Add subsection comments for:**
- Regular client creation and what it's used for
- Admin client creation and when you need it (bypasses Row Level Security)
- Why environment variables are used

### app/lib/redis.ts

Create Redis client connection helper.

**Add comprehensive header comment explaining:**
- This is how we talk to the "whiteboard" (fast temporary storage)
- Connection is established when the app starts
- Error handling so the app doesn't crash if Redis is down

**Add subsection comments for:**
- Client creation
- Error event handler
- Connection call

## Setup Scripts

### scripts/setup.sh

Create a bash script that initializes new projects from this template.

**Add a comprehensive header comment block explaining:**
- What this script does (fills out the form for you)
- When to run it (once when starting a new project)
- What it will ask for (project name, domain)
- What it will generate (environment file with secrets)

**Add section comments for:**
- User input collection (explain what each question is for)
- Environment file creation (explain copying from example)
- Secret generation (explain security importance)
- Final instructions (explain manual steps still needed)

Make the script interactive and friendly. Use echo statements to guide the user through the process.

### scripts/deploy.sh

Create a bash script for deploying to production.

**Add a comprehensive header comment block explaining:**
- What this script does (deploys to a VPS)
- Prerequisites (Docker on VPS, SSH access, DNS configured)
- What it will ask for (VPS IP, SSH user)
- What it will do (copy files, start containers remotely)

**Add section comments for:**
- Validation checks (make sure prerequisites are met)
- File synchronization (explain rsync and what gets copied)
- Remote Docker commands (explain starting containers on VPS)
- Success message with next steps

## Additional Guidelines

### Error Messages

When generating validation or error handling code, make error messages helpful:
- Explain what went wrong
- Suggest how to fix it
- Point to relevant documentation or files

### Security Considerations

When relevant, add comments about:
- Why we don't expose certain ports
- Why we run as non-root user
- Why we use secrets and how to generate them
- Why certain files must never be committed to Git

### Performance Notes

When relevant, add comments about:
- Why we use multi-stage builds (smaller images)
- Why we cache dependencies (faster rebuilds)
- Why we use alpine variants (smaller, faster)
- Why we enable GZIP compression (faster page loads)

### Success Criteria

After generating all files, a developer should be able to:

1. **Understand the entire stack** by reading comments alone
2. **Clone and run locally** in under 5 minutes (after getting Supabase credentials)
3. **Deploy to production** using the provided script
4. **Customize for their project** knowing exactly what to change
5. **Debug issues** using helpful comments and clear structure

## Tone and Style

Throughout all generated files:
- Be encouraging and supportive
- Use "we" and "you" (not "one must")
- Assume the reader is smart but may be new to some concepts
- Use concrete examples over abstract explanations
- Prefer simple words over technical terms
- When technical terms are necessary, briefly explain them

## Final Note

This template represents production-ready infrastructure as code. Every decision has been made deliberately. Comments should help readers understand not just HOW it works, but WHY it's built this way and WHEN they might want to change it.

The goal is to create a template that becomes a trusted starting point for any web application project, reducing setup time from days to minutes while teaching best practices along the way.