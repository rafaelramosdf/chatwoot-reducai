# Chatwoot - Local Development Setup

This guide will help you set up Chatwoot for local development using Docker Compose.

## Prerequisites

Before you begin, ensure you have the following installed on your system:

- **Docker Desktop** (latest version)
  - Windows: [Download Docker Desktop for Windows](https://docs.docker.com/desktop/windows/install/)
  - macOS: [Download Docker Desktop for Mac](https://docs.docker.com/desktop/mac/install/)
  - Linux: [Install Docker Engine](https://docs.docker.com/engine/install/)
- **Git** for version control
- **A code editor** (VS Code, Sublime Text, etc.)

## Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/rafaelramosdf/chatwoot-reducai.git
cd chatwoot-reducai
```

### 2. Environment Configuration

Copy the example environment file and configure it:

```bash
# Copy the example environment file
cp .env.example .env
```

Edit the `.env` file and set the following required variables:

```bash
# Generate a secure secret key (you can use the command below)
SECRET_KEY_BASE=your_secure_secret_key_here

# Database configuration
POSTGRES_DATABASE=chatwoot
POSTGRES_HOST=postgres
POSTGRES_USERNAME=postgres
POSTGRES_PASSWORD=chatwoot_postgres_pass

# Redis configuration
REDIS_URL=redis://redis:6379
REDIS_PASSWORD=chatwoot_redis_pass

# Rails environment
RAILS_ENV=development
RAILS_MAX_THREADS=5

# Frontend URL
FRONTEND_URL=http://localhost:3000

# Email configuration (using MailHog for development)
SMTP_ADDRESS=mailhog
SMTP_PORT=1025
MAILER_SENDER_EMAIL=Chatwoot <accounts@chatwoot.com>

# Enable account signup for development
ENABLE_ACCOUNT_SIGNUP=true
```

**Generate a secure SECRET_KEY_BASE:**

```bash
# Using Docker with Ruby
docker run --rm ruby:3.2 ruby -e "require 'securerandom'; puts SecureRandom.hex(64)"

# Or using OpenSSL (if available)
openssl rand -hex 64
```

### 3. Build and Start the Application

Build the Docker images and start all services:

```bash
# Build the base image first
docker compose build base

# Start all services
docker compose up -d
```

**Wait for initialization:** The first startup may take several minutes as it downloads dependencies and sets up the database.

### 4. Verify the Setup

Check that all containers are running:

```bash
docker compose ps
```

You should see all services running:
- `chatwoot-reducai-rails-1` (Main application)
- `chatwoot-reducai-vite-1` (Frontend development server)
- `chatwoot-reducai-sidekiq-1` (Background jobs)
- `chatwoot-reducai-postgres-1` (Database)
- `chatwoot-reducai-redis-1` (Cache and sessions)
- `chatwoot-reducai-mailhog-1` (Email testing)

### 5. Access the Application

- **Main Application**: http://localhost:3000
- **Vite Dev Server**: http://localhost:3036 (for frontend assets)
- **MailHog Interface**: http://localhost:8025 (for email testing)

## Default Login Credentials

After the initial setup, you can log in with:

- **Email**: `john@acme.inc`
- **Password**: `Password1!`

## Development Workflow

### Starting and Stopping

```bash
# Start all services
docker compose up -d

# Stop all services
docker compose down

# View logs
docker compose logs -f

# View logs for specific service
docker compose logs -f rails
docker compose logs -f vite
```

### Database Operations

```bash
# Create and setup database
docker compose exec rails bundle exec rails db:create db:migrate db:seed

# Reset database (if needed)
docker compose exec rails bundle exec rails db:drop db:create db:migrate db:seed

# Run database migrations
docker compose exec rails bundle exec rails db:migrate

# Access Rails console
docker compose exec rails bundle exec rails console

# Access database console
docker compose exec postgres psql -U postgres -d chatwoot
```

### Running Tests

```bash
# Run all tests
docker compose exec rails bundle exec rspec

# Run specific test file
docker compose exec rails bundle exec rspec spec/models/account_spec.rb

# Run tests with coverage
docker compose exec rails bundle exec rspec --format documentation
```

### Frontend Development

The Vite development server provides hot reloading for frontend assets:

- CSS/SCSS changes are automatically reloaded
- JavaScript/Vue changes trigger page refresh
- Access frontend directly via http://localhost:3036 if needed

### Background Jobs

Sidekiq handles background jobs and can be monitored via:

```bash
# View Sidekiq logs
docker compose logs -f sidekiq

# Access Sidekiq web interface (if enabled)
# Add to config/routes.rb in development:
# require 'sidekiq/web'
# mount Sidekiq::Web => '/sidekiq'
```

## Project Structure

```
chatwoot-reducai/
├── app/                     # Rails application code
│   ├── controllers/         # Application controllers
│   ├── models/             # Data models
│   ├── views/              # View templates
│   ├── javascript/         # Frontend JavaScript/Vue
│   └── assets/             # Static assets
├── config/                 # Configuration files
├── db/                     # Database migrations and seeds
├── docker/                 # Docker configuration
│   ├── dockerfiles/        # Custom Dockerfiles
│   └── entrypoints/        # Container entry scripts
├── spec/                   # Test files (RSpec)
├── docker-compose.yaml     # Development services
├── .env                    # Environment variables
└── README.md              # Project documentation
```

## Common Issues and Solutions

### Container Issues

**Containers not starting:**
```bash
# Check container status
docker compose ps

# View detailed logs
docker compose logs

# Rebuild images
docker compose build --no-cache
docker compose up -d
```

**Port conflicts:**
```bash
# Check what's using the ports
# Windows:
netstat -ano | findstr :3000
# Linux/Mac:
lsof -i :3000

# Change ports in docker-compose.yaml if needed
```

### Database Issues

**Database connection errors:**
```bash
# Ensure PostgreSQL container is running
docker compose ps postgres

# Check database logs
docker compose logs postgres

# Restart database container
docker compose restart postgres
```

**Migration errors:**
```bash
# Reset database
docker compose exec rails bundle exec rails db:drop db:create db:migrate db:seed
```

### Performance Issues

**Slow startup times:**
- First run always takes longer (downloading dependencies)
- Ensure Docker has enough resources allocated
- On Windows, use WSL2 backend for better performance

**Frontend compilation issues:**
```bash
# Clear Vite cache
docker compose exec vite rm -rf node_modules/.vite

# Restart Vite container
docker compose restart vite
```

## Environment Variables Reference

### Required Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `SECRET_KEY_BASE` | Rails secret key | Generated 128-char string |
| `POSTGRES_DATABASE` | Database name | `chatwoot` |
| `POSTGRES_PASSWORD` | Database password | `chatwoot_postgres_pass` |
| `REDIS_PASSWORD` | Redis password | `chatwoot_redis_pass` |

### Optional Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `FRONTEND_URL` | Application URL | `http://localhost:3000` |
| `ENABLE_ACCOUNT_SIGNUP` | Allow new registrations | `false` |
| `LOG_LEVEL` | Rails log level | `info` |
| `RAILS_MAX_THREADS` | Puma thread count | `5` |

## Troubleshooting

### Getting Help

1. **Check logs**: Always start by checking container logs
2. **Restart services**: Try restarting problematic containers
3. **Clean rebuild**: Rebuild images from scratch if issues persist
4. **Resource allocation**: Ensure Docker has sufficient CPU/memory

### Reset Everything

If you encounter persistent issues, you can reset the entire environment:

```bash
# Stop and remove all containers, networks, and volumes
docker compose down -v

# Remove images (optional)
docker compose down --rmi all

# Start fresh
docker compose build --no-cache
docker compose up -d
```

### Common Commands

```bash
# View all containers
docker ps -a

# Clean up unused Docker resources
docker system prune -f

# Monitor resource usage
docker stats

# Execute commands in running containers
docker compose exec rails bash
docker compose exec postgres psql -U postgres
```

## Contributing

When contributing to the project:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly in the development environment
5. Submit a pull request

For more detailed contributing guidelines, see [CONTRIBUTING.md](./CONTRIBUTING.md).

## Additional Resources

- [Chatwoot Documentation](https://www.chatwoot.com/docs)
- [Rails Guides](https://guides.rubyonrails.org/)
- [Vue.js Documentation](https://vuejs.org/)
- [Docker Documentation](https://docs.docker.com/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
