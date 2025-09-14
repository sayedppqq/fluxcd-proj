# FluxCD WordPress Deployment

GitOps deployment of WordPress with MySQL using FluxCD on Kubernetes.

## What's Deployed

- **WordPress** with MySQL database
- **MetalLB** load balancer
- **Ingress-NGINX** with custom Lua plugin
- **Monitoring** stack (Prometheus, Loki, Grafana, Promtail)
- **Encrypted secrets** using SOPS + Age

## Quick Start

1. **Bootstrap FluxCD**
```bash
flux bootstrap github \
  --owner=your-username \
  --repository=fluxcd-proj \
  --branch=main \
  --path=./clusters/local \
  --personal
```

2. **Add to hosts file**
```bash
echo "172.22.226.254 app.local" >> /etc/hosts
echo "172.22.226.254 app-monitor.local" >> /etc/hosts
```

3. **Wait for deployment**
```bash
flux get kustomizations
kubectl get pods -A
```

## Access

- WordPress: http://app.local
- Grafana: http://app-monitor.local (admin/admin123)

## Components

### Infrastructure
- **MetalLB**: Load balancer (IP: 172.22.226.254)
- **Ingress-NGINX**: HTTP ingress with 404 redirect to Google
- **Monitoring**: Prometheus, Loki, Grafana, Promtail

### Applications  
- **MySQL**: StatefulSet with encrypted secrets
- **WordPress**: Connected to MySQL via ClusterIP service

### Security
- **SOPS encryption**: Age key in `age.agekey`
- **GitOps**: All changes via Git commits

## Files Structure

```
├── clusters/local/           # FluxCD entry points
├── infrastructure/           # Infrastructure components
│   ├── metallb/             # Load balancer
│   ├── ingress-nginx/       # Ingress + Lua plugin
│   └── monitoring/          # Observability stack
├── applications/            # Application deployments
│   ├── mysql/              # Database with secrets
│   └── wordpress/          # WordPress app
├── .sops.yaml              # SOPS configuration
└── age.agekey              # Encryption key
```

## Test

```bash
# WordPress
curl http://app.local

# 404 redirect to Google  
curl http://app.local/notfound

# Grafana
curl http://app-monitor.local
```

## Status

✅ Infrastructure deployed  
✅ WordPress + MySQL working  
✅ SOPS encryption configured  
🚧 Monitoring stack (partial)  
🚧 Custom Grafana dashboard  