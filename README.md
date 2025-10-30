# Full-Stack Template

A production-ready, containerized web application template that gets you from zero to production in minutes. Everything runs in Docker, works identically on your laptop and in the cloud, and includes best practices baked in.

> **For Template Developers:** If you're building or modifying this template, see [CLAUDE.md](./CLAUDE.md) for build instructions and documentation standards.

## What This Template Does

This template connects four essential pieces that work together seamlessly: **Nginx** (your traffic manager), **Next.js** (your application), **Redis** (your fast cache), and **Supabase** (your database). Clone this, fill in a few settings, and you have a complete, scalable application ready to customize. No more "how do I set up a production server?" — it's done. You focus on building features.

## The Four Pieces

Think of your application like a restaurant:

**Nginx (the front door & host stand)** — Every customer enters through the front door. Nginx directs traffic, manages security (HTTPS/SSL), and routes requests to the right place. It's also smart about caching static content like menus and decorations, so they load fast.

**Next.js (the kitchen & dining room)** — This is where the magic happens. Next.js handles both the backend (API routes that process orders) and the frontend (the dining room experience). It's fast, handles user sessions, and makes building features straightforward.

**Redis (the order ticket rail)** — Fast, temporary storage. When the kitchen needs to quickly check what's been ordered or remember a customer's preferences, Redis is there. It's not permanent storage — it's for things you need *right now, fast*. Think of it as the whiteboard in the kitchen where you write current orders.

**Supabase (the filing cabinet)** — This is permanent storage. Every order, customer, transaction, and piece of data that matters lives here. It's secure, searchable, and never forgotten.

## Quick Start

### Prerequisites
- **Docker Desktop** installed ([download here](https://www.docker.com/products/docker-desktop))
- **Git** for version control
- A **Supabase account** ([free tier at supabase.com](https://supabase.com))
- About **5 minutes** of setup time

### Step 1: Clone and Configure

```bash
# Clone this template
git clone <this-repo> my-awesome-app
cd my-awesome-app

# Create your local environment file
cp .env.example .env.local
```

Now open `.env.local` and fill in your Supabase credentials:
- Get your Supabase URL and keys from [app.supabase.com](https://app.supabase.com)
- Go to Project Settings → API → copy the Project URL and anon key
- Paste them into `.env.local`

### Step 2: Start Everything

```bash
# Start all containers (Nginx, Next.js, Redis, and your database connection)
docker-compose up

# On first run, this downloads images. Subsequent runs are much faster.
# Wait for "ready - started server on" in the Next.js logs
```

That's it! Open [http://localhost](http://localhost) in your browser.

### Step 3: Develop

The template includes "hot reload" — change a file and see the changes instantly without restarting anything. Just edit files normally and refresh your browser.

```bash
# To see logs from all services
docker-compose logs -f

# To stop everything
docker-compose down

# To stop and wipe data (fresh start)
docker-compose down -v
```

## Project Structure

```
fullstack-template/
├── README.md                    # You are here
├── .env.example                 # Template for environment variables
├── .gitignore                   # Don't commit secrets or build files
│
├── docker-compose.yml           # Main orchestration (all 4 services)
├── docker-compose.dev.yml       # Development mode (hot reload, debugging)
├── docker-compose.prod.yml      # Production mode (resource limits, security)
│
├── nginx/                       # Web server & reverse proxy
│   ├── nginx.conf              # Configuration
│   └── Dockerfile              # Build instructions
│
├── app/                         # Next.js application
│   ├── package.json            # Dependencies
│   ├── Dockerfile              # Production build
│   ├── Dockerfile.dev          # Development build
│   ├── next.config.js          # Next.js settings
│   ├── tsconfig.json           # TypeScript settings
│   ├── tailwind.config.js       # Styling framework
│   ├── .eslintrc.json          # Code quality rules
│   │
│   ├── app/                    # Application code (Next.js App Router)
│   │   ├── layout.tsx          # Root layout (wraps all pages)
│   │   ├── page.tsx            # Home page
│   │   ├── globals.css         # Global styles
│   │   └── api/                # Backend API routes
│   │       └── health/
│   │           └── route.ts    # Health check endpoint
│   │
│   ├── lib/                    # Helper functions
│   │   ├── supabase.ts         # Database connection
│   │   └── redis.ts            # Cache connection
│   │
│   └── public/                 # Static files (images, etc)
│
└── scripts/                     # Automation scripts
    ├── setup.sh                # Initialize new projects
    └── deploy.sh               # Deploy to production
```

## Development Workflow

### Local Development

```bash
# Terminal 1: Start all services
docker-compose -f docker-compose.yml -f docker-compose.dev.yml up

# Terminal 2: Check logs as you work
docker-compose logs -f app

# Your code is hot-reloaded automatically. Just save and refresh!
```

### Making Database Changes

Edit your Supabase schema directly in [app.supabase.com](https://app.supabase.com), then update your app's queries. The app will automatically use the new schema on next refresh.

### Building Features

1. **API Routes** — Create endpoints in `app/app/api/[feature]/route.ts`
2. **Database Queries** — Use Supabase client in `app/lib/supabase.ts`
3. **Caching** — Use Redis client in `app/lib/redis.ts` for temporary data
4. **Frontend** — Create components and pages in `app/app/`

All hot-reloaded. No restarts needed.

### Debugging

```bash
# See all container logs
docker-compose logs -f

# See just the app
docker-compose logs -f app

# Restart a specific service
docker-compose restart app

# SSH into the app container
docker-compose exec app sh
```

## Deployment

### Prerequisites for Production
- A VPS or cloud server (DigitalOcean, Linode, AWS, etc) running Ubuntu/Debian
- Docker and Docker Compose installed on the server
- A domain name pointed to your server's IP
- SSH access to the server

### Deploy Script

The template includes an automated deploy script that handles everything:

```bash
# Make the script executable
chmod +x scripts/deploy.sh

# Run the deployment
./scripts/deploy.sh

# Follow the prompts:
# - Enter your VPS IP address
# - Enter your SSH username
# - Enter your domain name
```

The script will:
1. Copy all files to your server
2. Install Docker if needed
3. Start containers with production settings
4. Set up HTTPS certificates (free!)
5. Configure Nginx for your domain

### Manual Deployment

If you prefer to deploy manually:

```bash
# SSH into your server
ssh user@your-server-ip

# Clone the repository
git clone <your-repo> /app
cd /app

# Copy and configure environment
cp .env.example .env.prod
# Edit .env.prod with production values

# Use production Docker settings
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up -d

# Check everything is running
docker-compose ps
```

## Customization Guide

### For a New Project

Use the setup script to initialize a new project from this template:

```bash
bash scripts/setup.sh
```

This will ask you for your project name and domain, then set up a customized copy.

### Changing the App Name

1. Edit `.env.local` and change `APP_NAME=`
2. Update `app/app/layout.tsx` to change the browser title
3. Update `README.md` with your project details

### Adding Dependencies

```bash
# Add a npm package
docker-compose exec app npm install package-name

# Remove a package
docker-compose exec app npm uninstall package-name

# It takes effect immediately (hot reload)
```

### Connecting to External APIs

1. Add API keys to `.env.local`
2. Use them in server routes only (never in browser code)
3. Create wrapper functions in `app/lib/` for API calls

### Customizing Styles

The template uses **Tailwind CSS**. Customize by:
- Editing `app/tailwind.config.js` for theme colors and fonts
- Using Tailwind classes in components: `<div className="bg-blue-500 p-4">`
- Adding custom CSS in `app/app/globals.css`

### Adding Database Tables

1. Open [app.supabase.com](https://app.supabase.com)
2. Go to the SQL Editor
3. Create tables and define your schema
4. The app automatically connects — no restart needed

## Troubleshooting

### "Connection refused" when accessing localhost

**Problem:** The app hasn't started yet.

**Solution:** Wait for the logs to show "ready - started server on 0.0.0.0:3000", then try again. First startup can take 30 seconds.

### Docker containers keep restarting

**Problem:** Usually a misconfigured environment variable or missing dependency.

**Solution:**
```bash
# Check the logs for the specific error
docker-compose logs app

# Common causes:
# - Wrong Supabase URL or keys in .env.local
# - Missing node_modules (run: docker-compose restart app)
```

### "Cannot connect to Redis"

**Problem:** Redis isn't running or isn't healthy.

**Solution:**
```bash
# Check Redis status
docker-compose logs redis

# Restart Redis
docker-compose restart redis

# If that doesn't work, stop and restart everything
docker-compose down
docker-compose up
```

### Changes aren't showing up

**Problem:** Hot reload might not be working.

**Solution:**
1. Make sure you're using `docker-compose.dev.yml`: check the start command
2. Hard refresh your browser: `Cmd+Shift+R` (Mac) or `Ctrl+Shift+R` (Windows)
3. Restart the app: `docker-compose restart app`

### Out of disk space

**Problem:** Docker images and volumes accumulate over time.

**Solution:**
```bash
# Clean up dangling images and volumes (safe)
docker system prune

# Remove everything Docker-related (be careful!)
docker system prune -a
```

### Performance is slow

**Problem:** Usually means the app is waiting on the database.

**Solution:**
1. Check Supabase query logs in [app.supabase.com](https://app.supabase.com) → Logs
2. Add indexes to frequently queried columns
3. Use Redis caching for repeated queries
4. Check docker resource usage: `docker stats`

## Getting Help

- **Docker issues:** [Docker documentation](https://docs.docker.com/)
- **Next.js questions:** [Next.js docs](https://nextjs.org/docs)
- **Supabase help:** [Supabase docs](https://supabase.com/docs)
- **Something's broken:** Check the logs with `docker-compose logs -f`

## What's Included

✅ Multi-stage Docker builds (optimized images)
✅ Hot module reloading (instant feedback)
✅ TypeScript (catch errors early)
✅ Tailwind CSS (modern styling)
✅ ESLint (code quality)
✅ Health check endpoint (monitoring ready)
✅ Session management (keep users logged in)
✅ Redis caching (fast temporary storage)
✅ Supabase integration (secure database)
✅ HTTPS ready (secure by default)
✅ Production-optimized (fast and safe)

## License

This template is open source and available under the MIT License. Use it for any project, commercial or personal.

---

**Happy building!** 🚀 You've got everything you need. Start with the Quick Start section and let me know if you get stuck.
