# Freddy Monorepo

This is the monorepo for the Freddy application, containing all services and infrastructure as code.

## Project Structure

```
.
├── .bitbucket/             # Bitbucket pipeline configurations
├── apps/                   # Application services
│   ├── api/                # Rust backend API
│   ├── autobot/            # Automation service
│   └── web/                # Frontend application
├── infrastructure/         # Infrastructure as code
│   ├── chrome/             # Headless Chrome service
│   ├── proxy/              # Nginx reverse proxy
│   ├── redis/              # Redis configuration
│   └── tunnel/             # SSH tunnel service
├── scripts/                # Utility scripts
├── .env.example            # Example environment variables 
├── bitbucket-pipelines.yml # CI/CD configuration
├── docker-compose.yml      # Main Docker Compose configuration
└── README.md              # This file
```

## Development Setup

### Prerequisites

- Docker and Docker Compose
- Node.js 18.x
- Rust 1.70.0+

### Environment Setup

1. Copy the example environment file:
   ```bash
   cp .env.example .env
   ```

2. Update the `.env` file with your local configuration.

### Running Locally

```bash
# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

## CI/CD Pipeline

The Bitbucket pipeline is configured in `bitbucket-pipelines.yml` and includes:

- Automated builds and tests on every push
- Docker image building and pushing
- Deployment to staging on `develop` branch
- Manual deployment to production from `main` branch
- Parallel job execution for faster builds
- Caching for dependencies to speed up builds

## Development Workflow

1. Create a feature branch from `develop`
2. Make your changes
3. Run tests locally
4. Create a pull request to `develop`
5. After review, merge to `develop` for staging deployment
6. Create a release branch from `develop` for production deployment
7. Merge to `main` to trigger production deployment

## Testing

### Backend (Rust)

```bash
cd apps/api
cargo test
```

### Frontend (Node.js)

```bash
cd apps/web
npm test
```

## SSL Configuration

### Development

For local development, self-signed certificates are automatically generated. To trust these certificates:

1. Add the following entry to your `/etc/hosts` file:
   ```
   127.0.0.1 labb.dev.freddy.app.labbvision.com
   ```

2. Trust the development certificate in your browser/OS.

### Production

For production, Let's Encrypt certificates are automatically managed. The system will:

1. Automatically obtain certificates on first run
2. Renew certificates automatically
3. Fall back to self-signed certificates if Let's Encrypt is unavailable

## Deployment

### Prerequisites

- Docker 20.10.0+
- Docker Compose 1.29.0+
- Ports 80 and 443 available

### Staging

Merging to `develop` will automatically deploy to the staging environment.

### Production

1. Set up your production environment:
   ```bash
   cp .env.example .env
   # Edit .env with production values
   ```

2. Set environment to production:
   ```bash
   echo "APP_ENV=production" >> .env
   ```

3. Start the stack:
   ```bash
   docker-compose up -d
   ```

4. Verify the deployment:
   ```bash
   docker-compose ps
   docker-compose logs -f
   ```

5. Access the application at `https://your-domain.com`

### Monitoring

Health checks are available at `https://your-domain.com/health`

### Updating

To update the application:

1. Pull the latest changes
2. Rebuild and restart the services:
   ```bash
   docker-compose up -d --build
   ```

### Backup and Recovery

Regularly back up:
- Redis data volume
- SSL certificates
- Environment variables
- Database (if applicable)

### Security Considerations

- Always use strong passwords in production
- Keep Docker and system packages updated
- Monitor logs for suspicious activity
- Use a firewall to restrict access to necessary ports
- Regularly rotate secrets and certificates

## Troubleshooting

### SSL Issues

If you encounter SSL errors:

1. Check certificate permissions:
   ```bash
   chmod 600 /path/to/privkey.pem
   chmod 644 /path/to/cert.pem
   ```

2. Verify certificate chain:
   ```bash
   openssl s_client -connect your-domain.com:443 -showcerts
   ```

3. Check Nginx configuration:
   ```bash
   docker-compose exec proxy nginx -t
   ```

### Logs

View logs for all services:
```bash
docker-compose logs -f
```

Or for a specific service:
```bash
docker-compose logs -f service_name
```

### Performance

For performance issues:

1. Check resource usage:
   ```bash
   docker stats
   ```

2. Review Nginx access/error logs
3. Check Redis memory usage

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

Proprietary - All rights reserved
