# Audible Luxe - Docker Deployment

A modern audiobook streaming application that provides a beautiful interface for browsing and listening to audiobooks through the V3 API.

## Features

- 📚 Browse thousands of audiobooks
- 🎧 Stream audiobooks directly in your browser
- 🌙 Dark mode support
- 📱 Fully responsive design
- ⭐ Manage your favorites
- 🔍 Advanced search and filtering
- 🎨 Beautiful, modern UI

## Prerequisites

- Docker and Docker Compose installed on your server
- V3 API credentials - Get your license key from [https://store.datahorders.org/](https://store.datahorders.org/)
- A server with at least 2GB RAM
- Port 80 available (or modify docker-compose.yml for a different port)

## Quick Start

### One-line installation

```bash
curl -fsSL https://raw.githubusercontent.com/zenjabba/audible-luxe-deploy/main/setup.sh | bash
```

### Manual installation

### 1. Clone this repository

```bash
git clone https://github.com/zenjabba/audible-luxe-deploy.git
cd audible-luxe-deploy
```

### 2. Configure environment

Copy the example environment file and edit it:

```bash
cp .env.example .env
```

Edit `.env` and add your V3 API credentials:

```env
# REQUIRED: V3 API Configuration
V3_API_BASE_URL=your_v3_api_base_url_here
V3_API_LICENSE_KEY=your_actual_license_key_here  # Get from https://store.datahorders.org/

# REQUIRED: Security (generate a random string)
SESSION_SECRET=your-random-session-secret-here
```

To generate a secure session secret:
```bash
openssl rand -base64 32
```

### 3. Create data directories

```bash
mkdir -p data logs
```

### 4. Start the application

```bash
docker-compose up -d
```

### 5. Access the application

Open your browser and navigate to:
- `http://your-server-ip` (or `http://localhost` if running locally)

## Architecture

The application consists of four services:

1. **Nginx** - Reverse proxy (port 80)
2. **Frontend** - Next.js application
3. **Backend** - Fastify API server
4. **Redis** - Caching layer

All traffic flows through Nginx, which routes:
- `/` → Frontend
- `/api` → Backend

## Managing the Application

### View logs

```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f backend
```

### Stop the application

```bash
docker-compose down
```

### Update to latest version

```bash
docker-compose pull
docker-compose up -d
```

### Backup database

```bash
cp data/production.db data/backup-$(date +%Y%m%d-%H%M%S).db
```

## Configuration

### Change port

To run on a different port, edit `docker-compose.yml`:

```yaml
services:
  nginx:
    ports:
      - "8080:80"  # Change 8080 to your desired port
```

### Memory limits

Redis is configured with a 256MB memory limit. To adjust:

```yaml
services:
  redis:
    command: redis-server --appendonly yes --maxmemory 512mb --maxmemory-policy allkeys-lru
```

## Troubleshooting

### Application not accessible

1. Check if services are running:
   ```bash
   docker-compose ps
   ```

2. Check logs for errors:
   ```bash
   docker-compose logs
   ```

3. Verify port 80 is not in use:
   ```bash
   sudo netstat -tlnp | grep :80
   ```

### Redis connection errors

If you see Redis connection errors, ensure the backend started after Redis:

```bash
docker-compose restart backend
```

### Database issues

If you encounter database errors:

```bash
# Backup existing database
mv data/production.db data/production.db.backup

# Restart to create fresh database
docker-compose restart backend
```

### Clear Redis cache

```bash
docker-compose exec redis redis-cli FLUSHALL
```

## Security Considerations

1. **Change the SESSION_SECRET** - Use a strong, random value
2. **Firewall** - Only expose necessary ports
3. **Updates** - Regularly update Docker images
4. **HTTPS** - For production use, consider placing this behind a reverse proxy with SSL

## Support

For issues or questions:
- Check the [Issues](https://github.com/zenjabba/audible-luxe-deploy/issues) page
- Review logs with `docker-compose logs`
- Get your V3 API license key from [https://store.datahorders.org/](https://store.datahorders.org/)

## License

This deployment configuration is provided as-is for use with the Audible Luxe application.