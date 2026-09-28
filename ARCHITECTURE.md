# Quant Terminal Architecture

## Overview

Quant Terminal is a professional financial intelligence SaaS platform built as a modular monolith. The system is organized into three main layers:

1. **Frontend** - React + TypeScript + Vite
2. **Backend** - Python + FastAPI
3. **Data** - PostgreSQL with SQLAlchemy ORM

## Directory Structure

```
quant-terminal/
├── backend/
│   ├── app/
│   │   ├── __init__.py
│   │   ├── main.py                 # FastAPI app factory
│   │   ├── config.py               # Environment configuration
│   │   ├── models.py               # SQLAlchemy ORM models
│   │   ├── schemas.py              # Pydantic schemas (request/response)
│   │   ├── middleware.py           # FastAPI middleware
│   │   │
│   │   ├── api/
│   │   │   ├── __init__.py
│   │   │   ├── v1/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── router.py       # Main API router
│   │   │   │   ├── health.py       # Health check endpoints
│   │   │   │   ├── instruments.py  # Instrument endpoints
│   │   │   │   └── ...
│   │   │
│   │   ├── database/
│   │   │   ├── __init__.py
│   │   │   ├── engine.py           # Database connection
│   │   │   ├── session.py          # SQLAlchemy session management
│   │   │   └── base.py             # Base model
│   │   │
│   │   ├── services/               # Business logic
│   │   │   ├── __init__.py
│   │   │   └── health.py
│   │   │
│   │   └── utils/
│   │       ├── __init__.py
│   │       └── logger.py
│   │
│   ├── alembic/                    # Database migrations
│   │   ├── env.py
│   │   ├── script.py.mako
│   │   └── versions/
│   │
│   ├── tests/
│   │   ├── __init__.py
│   │   ├── conftest.py             # Pytest fixtures
│   │   ├── test_health.py
│   │   └── ...
│   │
│   ├── requirements.txt            # Python dependencies
│   ├── Dockerfile                  # Backend container
│   ├── alembic.ini                 # Migration configuration
│   └── pytest.ini                  # Test configuration
│
├── frontend/
│   ├── src/
│   │   ├── main.tsx                # React entry point
│   │   ├── App.tsx                 # Root component
│   │   ├── index.css               # Global styles
│   │   │
│   │   ├── components/             # Reusable components
│   │   │   ├── Layout.tsx
│   │   │   ├── Navigation.tsx
│   │   │   ├── ...ui components
│   │   │
│   │   ├── pages/                  # Page components
│   │   │   ├── Dashboard.tsx
│   │   │   ├── Markets.tsx
│   │   │   └── ...
│   │   │
│   │   ├── hooks/                  # Custom React hooks
│   │   │   ├── useApi.ts
│   │   │   └── ...
│   │   │
│   │   ├── services/               # API client services
│   │   │   ├── api.ts              # API configuration
│   │   │   ├── health.ts
│   │   │   └── ...
│   │   │
│   │   ├── types/                  # TypeScript interfaces
│   │   │   ├── common.ts
│   │   │   ├── api.ts
│   │   │   └── ...
│   │   │
│   │   └── utils/                  # Utility functions
│   │       ├── format.ts
│   │       └── ...
│   │
│   ├── tests/
│   │   ├── unit/
│   │   ├── integration/
│   │   └── ...
│   │
│   ├── public/
│   ├── index.html
│   ├── package.json
│   ├── tsconfig.json
│   ├── vite.config.ts
│   ├── vitest.config.ts
│   ├── tailwind.config.js
│   ├── postcss.config.js
│   └── Dockerfile                  # Frontend container
│
├── docker-compose.yml              # Service orchestration
├── .env.example                    # Environment template
├── .gitignore
└── README.md
```

## Backend Architecture

### Core Components

#### 1. FastAPI Application (`app/main.py`)
- Entry point for the REST API
- Route registration
- Middleware configuration
- Exception handling

#### 2. Configuration (`app/config.py`)
- Environment variable management
- Settings validation with Pydantic
- Different config for dev/prod

#### 3. Database Layer
- SQLAlchemy ORM models
- Alembic migrations for schema versioning
- Connection pooling and session management

#### 4. API Routes (`app/api/v1/`)
- Versioned REST endpoints
- Request validation with Pydantic
- Response serialization
- Error handling

#### 5. Services (`app/services/`)
- Business logic and data processing
- Database queries and operations
- External API calls

### Request Flow

```
Client HTTP Request
        ↓
FastAPI Router (app/api/v1/)
        ↓
Request Validation (Pydantic schemas)
        ↓
Service Layer (Business logic)
        ↓
Database Layer (SQLAlchemy models)
        ↓
PostgreSQL Database
        ↓
Response Serialization (Pydantic schemas)
        ↓
HTTP Response
```

## Frontend Architecture

### Core Components

#### 1. React Application (`src/main.tsx`)
- Entry point for React app
- React Router setup
- Global providers (context, theme, etc.)

#### 2. Components (`src/components/`)
- Reusable UI components
- Layout components
- Feature-specific components

#### 3. Pages (`src/pages/`)
- Route-level components
- Page-specific logic

#### 4. Services (`src/services/`)
- API client initialization
- HTTP request helpers
- Authentication service

#### 5. Hooks (`src/hooks/`)
- Custom React hooks for API calls
- State management hooks
- DOM manipulation hooks

### Component Structure

```
App.tsx (Root)
    ↓
<Layout>
    ├── <Navigation/>  (Sidebar)
    ├── <MainContent>
    │   ├── <Page Component>
    │   └── <Feature Components>
    └── <StatusBar/>   (Footer)
```

## Data Flow

### Typical User Action

```
1. User Action (click, input, etc.)
   ↓
2. React Component Event Handler
   ↓
3. Custom Hook (useApi, useData, etc.)
   ↓
4. API Service (api.ts)
   ↓
5. HTTP Request → Backend
   ↓
6. FastAPI Route Handler
   ↓
7. Validation → Service → Database
   ↓
8. Response → Frontend
   ↓
9. Update Component State
   ↓
10. Re-render UI
```

## Database Schema

### Phase 1 (Foundation)
- `alembic_version` - Migration tracking
- (Additional tables added in Phase 2+)

### Future Phases
- Users, Organizations, Subscriptions
- Instruments, Exchanges
- Price data, Corporate actions
- Portfolios, Watchlists
- Screeners, Alerts

## Development Workflow

### Local Development
1. Frontend runs on `http://localhost:5173` with hot reload
2. Backend runs in Docker on `http://localhost:8000`
3. Database runs in Docker on `localhost:5432`
4. Changes to frontend source trigger rebuild
5. Backend changes require container restart

### Making Database Changes
1. Create migration: `alembic revision --autogenerate -m "Description"`
2. Review generated migration file
3. Apply: `alembic upgrade head`
4. Commit migration to version control

### Adding New API Endpoints
1. Create Pydantic schema in `app/schemas.py`
2. Create SQLAlchemy model in `app/models.py` (if needed)
3. Create service in `app/services/`
4. Create route in `app/api/v1/`
5. Write tests in `tests/`
6. Update API documentation

## API Versioning

API endpoints are versioned under `/api/v1/`.

Future versions (`/api/v2/`, etc.) can coexist with v1 during transition periods.

## Error Handling

### Backend
- Structured error responses with error codes
- Appropriate HTTP status codes
- Detailed logging
- User-friendly error messages

### Frontend
- API error handling in service layer
- User-visible error notifications
- Graceful degradation
- Error boundaries for React

## Security Considerations

### Phase 1
- Environment-based configuration
- No hardcoded secrets

### Future Phases
- Authentication and authorization
- Rate limiting
- Input validation and sanitization
- CORS configuration
- Database encryption

## Performance Considerations

### Database
- Connection pooling
- Indexes on frequently queried columns
- Query optimization

### Backend
- Response caching
- Async operations for long-running tasks
- Pagination for large datasets

### Frontend
- Code splitting and lazy loading
- Component memoization
- Efficient re-renders

## Deployment Architecture

Once ready for deployment:

```
Load Balancer
    ↓
[Frontend Container] [Backend Container]
    ↓                      ↓
  Nginx              FastAPI/Gunicorn
    ↓                      ↓
                    [PostgreSQL Container]
                    [Redis Container]
```

## Testing Strategy

### Backend
- Unit tests for services and utilities
- Integration tests for API endpoints
- Database tests with fixtures
- Test coverage target: 80%+

### Frontend
- Unit tests for utilities and hooks
- Component tests with React Testing Library
- Integration tests for user flows
- Test coverage target: 70%+

## Monitoring and Logging

### Phase 1
- Structured logging with Python logging
- Application logs to stdout/stderr

### Future Phases
- Centralized logging
- Performance monitoring
- Error tracking
- User analytics
