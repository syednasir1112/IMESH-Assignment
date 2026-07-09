# Kubernetes Gateway API Implementation

Production-ready Kubernetes implementation using Gateway API, Envoy Gateway, and cert-manager.

## Architecture

- **Gateway API**: v1.1.0
- **Envoy Gateway**: v1.2.0
- **cert-manager**: v1.15.1
- **Runtime**: k3d (Kubernetes v1.30+)

## Prerequisites

- Docker Desktop
- kubectl
- Helm
- k3d

## Quick Start

```powershell
# Add to C:\Windows\System32\drivers\etc\hosts:
# 127.0.0.1 web.company.local
# 127.0.0.1 api.company.local
# 127.0.0.1 test.company.local

.\scripts\create-cluster.ps1
.\scripts\install-envoy.ps1
.\scripts\install-cert-manager.ps1
.\scripts\deploy-all.ps1
```

## Verification

```powershell
kubectl get pods -A
kubectl get gateway
kubectl get httproute
kubectl get certificate
```

## Testing

```powershell
curl http://web.company.local/
curl http://api.company.local/
curl http://test.company.local/
curl -vk https://api.company.local/
```

## Project Structure

```
.
├── assignment-1/          # Application deployments
├── assignment-2/          # Gateway API configuration
├── assignment-3/          # TLS certificates
├── assignment-4/          # Networking documentation
├── assignment-5/          # Troubleshooting guide
├── assignment-6/          # Production architecture
├── bonus/                 # JWT authentication
└── scripts/               # Installation scripts
```

## Cleanup

```powershell
kubectl delete -f bonus/
kubectl delete -f assignment-3/
kubectl delete -f assignment-2/
kubectl delete -f assignment-1/
k3d cluster delete imesh-lab
```

## Domains

- web.company.local → nginx
- api.company.local → httpbin
- test.company.local → echoserver
