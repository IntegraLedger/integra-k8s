# Integra K8s Infrastructure

**Production Kubernetes Configuration**  
**Version:** 1.0.0  
**Created:** 2024-08-25  

This repository contains the actual Kubernetes configuration for the Integra platform.

## Directory Structure

```
integra-k8s/
├── infrastructure/          # Core cluster services (deployed first)
│   ├── 01-namespaces/      # Namespace definitions
│   ├── 02-infisical/       # Secret management operator
│   ├── 03-cert-manager/    # TLS certificate management
│   ├── 04-linkerd/         # Service mesh
│   ├── 05-monitoring/      # Prometheus + Loki + Grafana
│   ├── 06-apisix/          # API Gateway
│   └── 07-hydra/           # OAuth2 provider
│
├── applications/           # Application deployments
│   ├── development/       # Dev environment configs
│   ├── staging/          # Staging configs
│   └── production/       # Production configs
│
├── helm-charts/           # Custom Helm charts
│   └── integra-service/  # Universal service chart
│
├── scripts/              # Deployment and management scripts
│   ├── deploy-infrastructure.sh
│   ├── deploy-applications.sh
│   └── verify-cluster.sh
│
├── secrets/              # Secret templates (values in Infisical)
│   └── README.md        # Never commit actual secrets!
│
└── state/               # Deployment state tracking
    ├── infrastructure.yaml
    └── applications.yaml
```

## Quick Start

### Prerequisites
- kubectl configured for your cluster
- Helm 3.x installed
- Infisical machine token configured
- GitHub PAT for npm packages

### Deploy Infrastructure

```bash
# Deploy all infrastructure components in order
./scripts/deploy-infrastructure.sh production
```

### Deploy Applications

```bash
# Deploy all applications
./scripts/deploy-applications.sh production

# Or deploy a single service
./scripts/deploy-service.sh integra-trust-app production
```

## Design Principles

1. **Simple GitOps** - Git + Helm + Scripts (no ArgoCD/Flux complexity)
2. **Clear Separation** - Infrastructure vs Applications
3. **Environment Parity** - Same patterns across dev/staging/prod
4. **AI-Proof** - Clear standards prevent configuration drift
5. **Single Source of Truth** - This repo defines cluster state

## Critical Information

### Infisical Machine Token
```yaml
CLIENT_ID: 652f8634-bae6-4aee-ae03-59904d13326d
CLIENT_SECRET: [Stored in environment]
PROJECT_ID: acd53ca1-6365-4874-874f-15d62453c34f
```

### Namespaces
- `integra-apps` - Frontend applications
- `integra-core` - Core services (bridge, admin)
- `integra-blockchain` - Blockchain services
- `integra-workflow` - Workflow services
- `integra-infrastructure` - Infrastructure components
- `monitoring` - Observability stack

### Critical Services (Never Change Names/Ports)
- `hydra-public:4444` - OAuth2 public API
- `hydra-admin:4445` - OAuth2 admin API
- `apisix-gateway` - External LoadBalancer
- `loki-grafana` - Monitoring dashboard

## Documentation

Detailed design documentation is in `/Users/davidfisher/AAA-LAUNCH/cluster-redesign/`

## Deployment Order

Infrastructure MUST be deployed in this order:
1. Namespaces
2. Infisical Operator
3. Cert-Manager
4. Linkerd
5. Monitoring (Loki + Prometheus)
6. APISIX
7. Hydra

Applications can be deployed in any order after infrastructure is ready.

## State Management

State is tracked in Git:
- `state/infrastructure.yaml` - Infrastructure component versions
- `state/applications.yaml` - Application versions

Rollback is simple:
```bash
# Rollback a service
helm rollback <service-name> -n <namespace>

# Check rollback history
helm history <service-name> -n <namespace>
```

## Monitoring

After deployment, access Grafana:
```bash
kubectl port-forward -n monitoring svc/loki-grafana 3000:80
# Username: admin
# Password: [Retrieved during deployment]
```

## Troubleshooting

### Check infrastructure status
```bash
./scripts/verify-cluster.sh
```

### View logs
```bash
kubectl logs -n <namespace> deployment/<service> --tail=100
```

### Check events
```bash
kubectl get events -n <namespace> --sort-by='.lastTimestamp'
```

## Security

- **Never commit secrets** - All secrets in Infisical
- **Never manual deploy** - Always use scripts
- **Never use latest tag** - Always versioned images
- **Never bypass Helm** - Maintains state consistency

## Support

For issues, check:
1. GitHub Actions logs for build failures
2. Infisical for secret issues
3. `kubectl describe` for pod issues
4. Grafana dashboards for metrics/logs