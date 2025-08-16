# Netdata Monitoring for OrbStack

A comprehensive monitoring solution for OrbStack containers and services using Netdata with auto-discovery and cloud integration.

## Features

- 🔍 **Auto-Discovery**: Automatically detects and monitors all Docker containers
- 📊 **Real-time Metrics**: 1-second granularity for all metrics
- ☁️ **Cloud Integration**: Connected to Netdata Cloud for centralized monitoring
- 🐳 **Docker Native**: Full container monitoring with health checks
- 📈 **Database Monitoring**: Auto-discovers PostgreSQL, Redis, ClickHouse
- 🚨 **Smart Alerts**: Pre-configured alerts for CPU, memory, and disk usage

## Quick Start

### Prerequisites

- Docker and Docker Compose installed (via OrbStack)
- Netdata Cloud account (free tier available)

### Setup

1. Clone this repository:
   ```bash
   git clone <repository-url>
   cd netdata-monitoring
   ```

2. Create a `.env` file with your Netdata Cloud credentials:
   ```bash
   NETDATA_CLAIM_TOKEN=<your-claim-token>
   NETDATA_CLAIM_URL=https://app.netdata.cloud
   NETDATA_CLAIM_ROOMS=<your-room-id>
   ```

   To get these values:
   - Login to [Netdata Cloud](https://app.netdata.cloud)
   - Navigate to your space
   - Click "Add Nodes" and copy the Docker Compose configuration

3. Start Netdata:
   ```bash
   docker compose up -d
   ```

4. Access the dashboards:
   - **Local**: http://localhost:19999
   - **Cloud**: https://app.netdata.cloud

## Configuration

### Docker Compose

The `docker-compose.yml` includes:
- Full system monitoring with root filesystem access
- Docker socket mounting for container discovery
- Persistent volumes for configuration and data
- Required capabilities for comprehensive monitoring

### Auto-Discovered Services

Netdata automatically monitors:
- All Docker containers (CPU, memory, network, disk I/O)
- PostgreSQL databases (100+ metrics)
- Redis instances (connections, operations, memory)
- ClickHouse (queries, resources, replication)
- System metrics from OrbStack Linux VM

### Security Considerations

- The `.env` file containing tokens is excluded from version control
- Docker socket is mounted read-only for security
- Consider using a socket proxy for production environments

## Monitoring Capabilities

### Container Metrics
- CPU utilization and limits
- Memory usage and limits
- Network I/O
- Disk I/O
- Container health status

### System Metrics
- CPU usage per core
- Memory and swap usage
- Disk usage and I/O
- Network interfaces
- System load and processes

### Database Metrics
- Query performance
- Connection pools
- Cache hit rates
- Replication status
- Resource utilization

## Troubleshooting

### Cloud Connection Issues

If you see "token is expired, not found, or invalid":
1. Get a fresh token from Netdata Cloud
2. Update the `.env` file
3. Restart the container: `docker compose restart`

### Checking Status

```bash
# Check container status
docker ps | grep netdata

# View logs
docker logs netdata

# Check cloud connection
curl -s http://localhost:19999/api/v3/info | jq '.agents[0].cloud'
```

## Netdata Cloud Free Tier

- Maximum 5 active nodes
- 1 custom dashboard per room
- Local data retention (limited by disk space)
- Email and Discord notifications

## Resources

- [Netdata Documentation](https://learn.netdata.cloud)
- [Netdata Cloud](https://app.netdata.cloud)
- [Docker Installation Guide](https://learn.netdata.cloud/docs/netdata-agent/installation/docker)

## License

This configuration is provided as-is for monitoring OrbStack environments.