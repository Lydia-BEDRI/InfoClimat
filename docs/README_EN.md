# InfoClimat - English Documentation

## Overview

**InfoClimat** is a web application for valorizing and analyzing meteorological data in France. It provides:

- **REST API** (Django backend) for accessing weather data, stations, and seasonal indicators
- **Web Interface** (Vue.js frontend) for visualizing meteorological data and anomalies
- **Observability Stack** (Prometheus + Grafana) for monitoring system metrics
- **Production-ready Infrastructure** with Docker hardened images and multi-stage builds

This is a **pedagogical fork project** developed as part of the "Infrastructure and DevOps" module at ESGI in collaboration with Data For Good (Season 14).

## Quick Start

### Prerequisites

- Docker & Docker Compose
- Python 3.11+ (for local development)
- Node.js 24+ (for local frontend development)

### Installation

**Clone the repository:**

```bash
git clone https://github.com/Lydia-BEDRI/InfoClimat.git
cd InfoClimat
```

**Development build (with simulated data):**

```bash
docker compose -f docker-compose.dev.yml up -d
```

**Production build (requires DHI registry access):**

```bash
docker login dhi.io
docker compose -f docker-compose.prod.yml build backend frontend
docker compose -f docker-compose.prod.yml up -d
```

See [Backend](../backend/README.md) and [Frontend](../frontend/README.md) for detailed instructions.

## Application Access

| Service                     | URL                                  | Credentials   |
| --------------------------- | ------------------------------------ | ------------- |
| Web Frontend                | `http://localhost:8080`              | -             |
| API Base URL                | `http://localhost:8080/api/v1/`      | -             |
| API Documentation (Swagger) | `http://localhost:8080/api/v1/docs/` | -             |
| Prometheus Metrics          | `http://localhost:8080/metrics`      | -             |
| Grafana Dashboard           | `http://localhost:3000`              | admin / admin |

## Key Features

### Weather Data Management

- Historical weather data storage and retrieval
- Multiple data sources integration
- Data validation and quality checks
- Support for various meteorological parameters

### Analytics & Anomaly Detection

- Seasonal anomaly detection
- Trend analysis
- Historical comparisons
- National indicators computation
- Temperature deviation tracking

### REST API

- RESTful endpoints with full CRUD operations
- OpenAPI/Swagger documentation
- Filtering and pagination support
- Real-time data access
- Comprehensive error handling

### Production-Ready Infrastructure

- Docker multi-stage builds (dev & production)
- Hardened base images from `dhi.io`
- Load balancing with Nginx
- Reverse proxy configuration
- Health checks and monitoring

### Observability

- Prometheus metrics collection
- Grafana dashboards
- Django prometheus integration
- Real-time system monitoring
- Custom metrics for API endpoints

### Quality Assurance

- Automated testing (unit & integration tests)
- Pre-commit hooks (Ruff, ESLint, Prettier)
- CI/CD pipeline with GitHub Actions
- Code quality linting and formatting
- Test coverage reporting

### Security

- HTTPS/TLS enforcement
- Security policy and vulnerability reporting process
- Credential scanning
- Dependency vulnerability checks
- Secure authentication mechanisms
- OpenSSF Best Practices compliance

### Documentation

- API documentation (OpenAPI/Swagger)
- Architecture documentation
- Deployment guides
- Contribution guidelines
- Troubleshooting guides

## Architecture

The project is organized into three main components:

### Backend (Python/Django)

- RESTful API implementation
- Database models and ORM
- Business logic for data processing
- Metrics and monitoring

**Key directories:**

- `backend/weather/` - Core application logic
- `backend/weather/services/` - Service layer
- `backend/weather/views.py` - API endpoints
- `backend/config/` - Django configuration

### Frontend (Vue.js/TypeScript)

- Single Page Application (SPA)
- Components and composables
- Stores (state management)
- i18n (internationalization)

**Key directories:**

- `frontend/app/pages/` - Page components
- `frontend/app/components/` - Reusable components
- `frontend/app/stores/` - State management
- `frontend/app/i18n/` - Translations

### Infrastructure

- Docker configuration for local and production
- GitHub Actions workflows for CI/CD
- Prometheus and Grafana setup
- Nginx configuration

## Development Workflow

### Local Development

1. **Setup backend:**

```bash
cd backend
uv sync --extra dev
cp .env.example .env
```

2. **Setup frontend:**

```bash
cd frontend
npm install
```

3. **Start services:**

```bash
docker compose -f docker-compose.dev.yml up -d
```

### Testing

**Backend tests:**

```bash
cd backend
uv run pytest
```

**Frontend tests:**

```bash
cd frontend
npm run test
```

### Code Quality

**Pre-commit hooks:**

```bash
pre-commit install
pre-commit run --all-files
```

**Backend linting:**

```bash
cd backend
uv run pre-commit run --all-files --config=.pre-commit-config.yaml
```

**Frontend linting:**

```bash
cd frontend
npm run lint
```

## API Documentation

For detailed API endpoint documentation, see [API_ENDPOINTS.md](API_ENDPOINTS.md)

Quick reference:

- `GET /api/v1/stations/` - List all weather stations
- `GET /api/v1/stations/{id}/` - Get specific station details
- `GET /api/v1/quotidienne/` - Get daily weather records
- `GET /api/v1/national_indicator/` - Get national indicators
- `GET /api/v1/temperature_deviation/` - Get temperature anomalies

## Monitoring & Observability

### Prometheus

- Access: `http://localhost:9090`
- Metrics endpoint: `http://localhost:8080/metrics`
- Scrape interval: 15 seconds

### Grafana

- Access: `http://localhost:3000`
- Credentials: admin / admin
- Pre-configured dashboard: "InfoClimat Backend Observability"

### Available Metrics

- HTTP request counts and latency
- Database query performance
- Python process metrics (GC, threads, memory)
- Custom application metrics

## Security

### Reporting Vulnerabilities

See [SECURITY.md](../SECURITY.md) for the vulnerability reporting process.

**Key points:**

- Email: [Check SECURITY.md]
- Response time: ≤ 14 days
- Confidential handling of reports

### Best Practices

- Keep images up-to-date (Python 3.11+, Node.js 24+)
- Use hardened base images from `dhi.io`
- Run security audits: `npm audit`, `pip audit`
- Enable pre-commit hooks for all developers
- Monitor dependencies for vulnerabilities

## Contributing

See the main [README](../README.md) for contribution guidelines and workflow.

### Key Points

- Use feature branches: `feat/scope/name`
- Make atomic commits
- Write clear commit messages
- Create PRs for code review
- Ensure tests pass before merging

## Troubleshooting

### API not responding

```bash
# Check backend container
docker compose logs backend

# Verify database connection
docker compose exec backend python manage.py shell
```

### Frontend build errors

```bash
# Clear node_modules and reinstall
cd frontend
rm -rf node_modules
npm install --legacy-peer-deps
```

### Metrics not appearing

```bash
# Check Prometheus targets
curl http://localhost:9090/api/v1/targets

# Check Django metrics endpoint
curl http://localhost:8000/metrics
```

## Support & Contact

For issues and questions:

- GitHub Issues: [Project Issues](https://github.com/Lydia-BEDRI/InfoClimat/issues)
- Email: [Check SECURITY.md for contact info]

## License

See [LICENSE](../LICENSE) for license information.

## Resources

- [Backend README](../backend/README.md)
- [Frontend README](../frontend/README.md)
- [API Documentation](API_ENDPOINTS.md)
- [Security Policy](../SECURITY.md)
- [Main README](../README.md)
