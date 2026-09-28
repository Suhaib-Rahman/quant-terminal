# Quant Terminal

Professional institutional-grade global financial intelligence SaaS platform inspired by Bloomberg Terminal.

## Quick Start

See [SETUP.md](./SETUP.md) for detailed setup and development instructions.

## Project Structure

```
quant-terminal/
├── backend/              # Python + FastAPI backend
├── frontend/             # React + TypeScript + Vite frontend
├── docker-compose.yml    # Database and service orchestration
├── .env.example          # Environment variable template
├── SETUP.md              # Development setup guide
├── ARCHITECTURE.md       # System architecture documentation
└── README.md             # This file
```

## Technology Stack

### Frontend
- React with TypeScript
- Vite build tool
- Tailwind CSS for styling
- Vitest for testing

### Backend
- Python 3.11+
- FastAPI for REST API
- SQLAlchemy for ORM
- Alembic for database migrations
- PostgreSQL for persistent storage

### Infrastructure
- Docker and Docker Compose
- Redis for caching

## Development

### Prerequisites
- Docker and Docker Compose
- Node.js 18+
- Python 3.11+

### Starting the Application

```bash
# Start all services (database, backend, frontend)
docker-compose up -d

# Run database migrations
cd backend && alembic upgrade head && cd ..

# Start frontend development server
cd frontend && npm run dev

# Backend runs automatically via docker-compose
```

The application will be available at:
- Frontend: http://localhost:5173
- Backend API: http://localhost:8000
- API Documentation: http://localhost:8000/docs

## Testing

```bash
# Backend tests
cd backend
pytest tests/

# Frontend tests
cd ../frontend
npm run test
```

## Health Checks

```bash
# Backend health endpoint
curl http://localhost:8000/health

# Database connectivity
curl http://localhost:8000/health/database
```

## Documentation

- [SETUP.md](./SETUP.md) - Detailed setup instructions
- [ARCHITECTURE.md](./ARCHITECTURE.md) - System design and module organization
- API documentation available at http://localhost:8000/docs (Swagger UI)

## License

MIT License - See LICENSE file for details.
