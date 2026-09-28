# Development Setup Guide

## Prerequisites

Before starting, ensure you have installed:

### System Requirements
- Docker Desktop (including Docker Compose)
- Git
- (Optional) Local development:
  - Node.js 18+ and npm/yarn
  - Python 3.11+
  - PostgreSQL client tools

## Quick Start (Docker Compose)

This is the recommended approach for development.

### 1. Clone and Configure

```bash
git clone https://github.com/Suhaib-Rahman/quant-terminal.git
cd quant-terminal
cp .env.example .env
```

### 2. Start Services

```bash
# Start database and backend
docker-compose up -d

# Verify services are running
docker-compose ps
```

### 3. Run Database Migrations

```bash
# Enter the backend container
docker-compose exec backend bash

# Run migrations
alembic upgrade head

# Verify tables were created
psql -U quant_user -h postgres -d quant_terminal -c "\dt"

# Exit container
exit
```

### 4. Start Frontend (Local)

```bash
cd frontend
npm install
npm run dev
```

The frontend will start at http://localhost:5173

### 5. Verify Installation

```bash
# Check backend health
curl http://localhost:8000/health

# Expected response:
# {"status":"healthy","timestamp":"2026-09-28T...","version":"0.1.0"}

# Check database connectivity
curl http://localhost:8000/health/database

# Expected response:
# {"status":"connected","database":"quant_terminal","timestamp":"..."}
```

## Local Development (Without Docker)

If you prefer to run services locally:

### Backend Setup

```bash
cd backend

# Create virtual environment
python3.11 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Create .env file (copy from .env.example)
cp ../.env.example .env

# Run migrations
alembic upgrade head

# Start development server
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Backend will be available at http://localhost:8000

### Frontend Setup

```bash
cd frontend

# Install dependencies
npm install

# Create .env file
cp ../.env.example .env.local

# Start development server
npm run dev
```

Frontend will be available at http://localhost:5173

## Testing

### Backend Tests

```bash
cd backend

# Run all tests with coverage
pytest --cov=app --cov-report=html tests/

# Run specific test file
pytest tests/test_health.py -v

# Run with live output
pytest -s tests/
```

### Frontend Tests

```bash
cd frontend

# Run tests
npm run test

# Run with coverage
npm run test:coverage

# Watch mode
npm run test:watch
```

## Database Management

### Connect to Database

```bash
# Using docker-compose
docker-compose exec postgres psql -U quant_user -d quant_terminal

# Or with psql installed locally
psql -U quant_user -h localhost -d quant_terminal -p 5432
```

### View Tables

```sql
\dt                          -- List all tables
\d table_name               -- Describe specific table
SELECT * FROM alembic_version; -- View migration history
```

### Reset Database (Development Only)

```bash
# Stop services
docker-compose down -v

# Remove volume (WARNING: deletes all data)
rm -rf postgres_data/

# Restart
docker-compose up -d
docker-compose exec backend alembic upgrade head
```

## Building for Production

### Frontend Build

```bash
cd frontend
npm run build
# Output: dist/
```

### Backend

The Dockerfile handles the build automatically.

## Useful Commands

```bash
# View logs
docker-compose logs -f backend
docker-compose logs -f postgres

# Stop services
docker-compose down

# Remove all containers and volumes
docker-compose down -v

# Rebuild images
docker-compose build --no-cache

# Run specific service
docker-compose up backend
```

## Environment Variables

Configuration is managed via `.env` file (copy from `.env.example`).

Key variables:
- `FASTAPI_ENV`: Set to `development` or `production`
- `DATABASE_URL`: PostgreSQL connection string
- `REDIS_URL`: Redis connection string
- `CORS_ORIGINS`: Comma-separated list of allowed origins
- `VITE_API_BASE_URL`: Frontend API endpoint

## Troubleshooting

### Backend won't start

```bash
# Check logs
docker-compose logs backend

# Verify database is running
docker-compose ps postgres

# Check migrations
docker-compose exec backend alembic current
```

### Database connection errors

```bash
# Ensure database service is running
docker-compose up -d postgres

# Wait for database to be ready (usually ~5 seconds)
sleep 10

# Run migrations
docker-compose exec backend alembic upgrade head
```

### Frontend build fails

```bash
cd frontend

# Clear node modules and reinstall
rm -rf node_modules package-lock.json
npm install

# Check TypeScript errors
npm run type-check
```

### Port already in use

If ports 5173, 8000, or 5432 are in use, modify `docker-compose.yml` and `frontend/vite.config.ts`.

## API Documentation

Once backend is running, visit http://localhost:8000/docs for interactive Swagger UI documentation.

## Support

For issues, check:
1. [ARCHITECTURE.md](./ARCHITECTURE.md) for system design
2. Backend and frontend logs
3. GitHub Issues
