# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Essential Commands

```bash
# Container Operations
docker compose up -d                    # Start Netdata
docker compose restart                   # Restart after config changes
docker compose down                      # Stop and remove container
docker logs netdata --tail 50          # Check recent logs
docker logs netdata 2>&1 | grep -i claim  # Check claiming status

# Verification & Health
curl -s http://localhost:19999/api/v3/info | jq '.agents[0].cloud'  # Cloud connection status
curl -s http://localhost:19999/api/v1/info                          # Basic health check
docker ps | grep netdata                                             # Container status

# Troubleshooting
docker exec netdata netdatacli reload-claiming-state                 # Reload claim state
curl -s http://localhost:19999/api/v1/alarms?all                    # List all alerts
curl -s http://localhost:19999/api/v1/charts | jq '.charts | keys[]' | grep -i <service>  # Find metrics
```

## Common Issues & Solutions

**Token expired/invalid error**
- Get fresh token: Netdata Cloud → your space → "Add Nodes" → copy Docker Compose config
- Update `.env` file with new token and room ID
- Run `docker compose restart`

**Cloud not connecting**
- MUST have all 3 env vars: NETDATA_CLAIM_TOKEN, NETDATA_CLAIM_URL, NETDATA_CLAIM_ROOMS
- Room ID is mandatory for this setup (format: UUID)
- Check logs: `docker logs netdata 2>&1 | grep -i claim`

**Service not auto-discovered**
- PostgreSQL, Redis, ClickHouse: Auto-discovered via port detection
- MinIO, Neo4j: NOT supported - need Prometheus/StatsD endpoints if available
- Verify docker.sock mount: `-v /var/run/docker.sock:/var/run/docker.sock:ro`

## Architecture Constraints

- **OrbStack Limitation**: Monitors Linux VM metrics only, NOT macOS host
- **Free Tier**: Max 5 nodes, 1 custom dashboard, local data retention
- **Token Format**: Changed in 2024 - no "ndc." prefix, get from Cloud dashboard
- **Required Mounts**: docker.sock (containers), /:/host/root (system), /proc, /sys

## Key API Endpoints

```bash
/api/v3/info                 # Full agent info including cloud status
/api/v1/info                 # Basic info (health check)
/api/v1/alarms?all          # All configured alerts
/api/v1/charts              # List all available metrics
/api/v1/data?chart=<name>   # Get specific metric data
```

## Environment Setup

1. Copy `.env.example` to `.env`
2. Fill in credentials from Netdata Cloud
3. NEVER commit `.env` (gitignored)
4. Source main env if needed: `source ~/.env`

## Critical Files

- `docker-compose.yml` - Container configuration with all mounts
- `.env` - Credentials (NEVER commit)
- `.env.example` - Template for others

## Workflow for Updates

1. Check current status first: `curl -s http://localhost:19999/api/v3/info | jq '.agents[0].cloud'`
2. Make changes to configuration
3. Restart container: `docker compose restart`
4. Verify changes took effect
5. Check logs if issues: `docker logs netdata --tail 50`