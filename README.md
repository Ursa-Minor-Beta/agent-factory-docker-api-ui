# Agent Factory Deploy

Docker deployment for Agent Factory ([agent-factory](https://github.com/Ursa-Minor-Beta/agent-factory) + [agent-factory-ui](https://github.com/Ursa-Minor-Beta/agent-factory-ui)).

## Quick Start

1. Copy environment file:
   ```bash
   cp .env.example .env
   ```

2. Edit `.env` with your configuration (at minimum set `ADMIN_EMAIL` and `ADMIN_PASSWORD`)

3. Run with local MongoDB:
   ```bash
   docker-compose --profile with-db up --build
   ```

   Or with external MongoDB (set `MONGODB_URI` in `.env`):
   ```bash
   docker-compose up --build
   ```

4. Access:
   - UI: http://localhost:8080
   - API: http://localhost:3000
## Deploy Specific Version

```bash
API_VERSION=v1.2.0 UI_VERSION=v1.0.0 docker-compose up --build
```

## Update After Branch Changes

To pull latest code and rebuild:
```bash
docker-compose --profile with-db up --build
```

For a complete fresh build (no cache):
```bash
docker-compose --profile with-db build --no-cache && docker-compose --profile with-db up
```

## Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `API_VERSION` | API branch/tag to deploy | `master` |
| `UI_VERSION` | UI branch/tag to deploy | `master` |
| `API_PORT` | API exposed port | `3000` |
| `UI_PORT` | UI exposed port | `8080` |
| `MONGO_PORT` | MongoDB exposed port | `27017` |
| `MONGO_USER` | MongoDB root username | - |
| `MONGO_PASSWORD` | MongoDB root password | - |
| `JWT_SECRET` | JWT signing secret | - |
| `ENCRYPTION_KEY` | Encryption key (32 bytes) | - |
| `MONGODB_URI` | MongoDB connection string | `mongodb://mongo:27017/agent-factory` |
| `VITE_API_URL` | API URL for UI | `http://localhost:3000` |

## Production Deployment

For production, ensure you:
1. Set strong `JWT_SECRET`, `ENCRYPTION_KEY`, `ADMIN_PASSWORD`
2. Configure MongoDB with authentication (e.g., `mongodb://user:password@host:27017/agent-factory`)
3. Set `VITE_API_URL` to your production API URL
4. Consider using a reverse proxy (nginx/traefik) for SSL
