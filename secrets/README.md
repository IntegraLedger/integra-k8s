# Secrets Directory

⚠️ **WARNING: NEVER COMMIT ACTUAL SECRETS TO THIS DIRECTORY** ⚠️

This directory contains only:
- Secret templates
- Documentation about required secrets
- Examples with placeholder values

All actual secret values are stored in Infisical.

## Required Secrets

### Infisical Machine Token
- `CLIENT_ID`: Stored in environment
- `CLIENT_SECRET`: Stored in environment  
- `PROJECT_ID`: acd53ca1-6365-4874-874f-15d62453c34f

### Slack Webhooks (for alerts)
- `SLACK_WEBHOOK_CRITICAL`: For critical alerts
- `SLACK_WEBHOOK_WARNINGS`: For warning alerts
- `SLACK_WEBHOOK_INFO`: For informational alerts

### Database Credentials
- Managed by DigitalOcean
- Connection strings in Infisical

### OAuth2 Secrets
- `HYDRA_SYSTEM_SECRET`: For Hydra
- `JWT_SECRET`: Shared across services

## How to Add Secrets

1. Add to Infisical web UI or CLI
2. Reference in InfisicalSecret CRD
3. Never put actual values in this repo