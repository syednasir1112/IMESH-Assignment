# IMESH Kubernetes Technical Assessment

## Overview
This repository contains the Kubernetes manifests and written responses for the IMESH technical assessment. The deployment target is a k3s cluster on AWS EC2.

## Architecture
The solution uses k3s as the Kubernetes runtime, Envoy Gateway for north-south traffic, cert-manager for certificate issuance, and three sample services: nginx, httpbin, and echoserver.

## Prerequisites
- AWS EC2 instance running Ubuntu 22.04
- Inbound access for SSH, HTTP, and HTTPS
- `kubectl`
- `helm`
- `curl`

## EC2 Setup
```bash
git clone <repository-url>
cd imesh-assignment
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```

## k3s Installation
```bash
curl -sfL https://get.k3s.io | sh -
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown "$USER":"$USER" ~/.kube/config
kubectl get nodes
```

## Gateway API Installation
```bash
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.1.0/standard-install.yaml
kubectl get crd | grep gateway
```

## Envoy Gateway Installation
```bash
helm repo add envoy-gateway https://gateway.envoyproxy.io
helm repo update
helm install eg envoy-gateway/envoy-gateway -n envoy-gateway-system --create-namespace --version v1.2.0
kubectl get pods -n envoy-gateway-system
kubectl get gatewayclass
```

## cert-manager Installation
```bash
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager jetstack/cert-manager -n cert-manager --create-namespace --version v1.15.1 --set crds.enabled=true
kubectl get pods -n cert-manager
kubectl get crd | grep cert-manager
```

## Deployment
```bash
kubectl apply -f assignment-1/
kubectl apply -f assignment-2/
kubectl apply -f assignment-3/
kubectl apply -f bonus/
```

## Verification
```bash
kubectl get nodes
kubectl get pods -A
kubectl get gateway -A
kubectl get httproute -A
kubectl get certificate -A
curl -H "Host: web.company.local" http://18.117.135.42
curl -H "Host: api.company.local" http://18.117.135.42/get
curl -H "Host: test.company.local" http://18.117.135.42
curl -vk https://18.117.135.42
```

## Project Structure
```text
imesh-assignment/
├── README.md
├── assignment-1/
│   ├── nginx-deployment.yaml
│   ├── nginx-service.yaml
│   ├── httpbin-deployment.yaml
│   ├── httpbin-service.yaml
│   ├── echoserver-deployment.yaml
│   └── echoserver-service.yaml
├── assignment-2/
│   ├── gateway.yaml
│   ├── httproutes.yaml
│   ├── rate-limit.yaml
│   ├── retry-policy.yaml
│   ├── timeout-policy.yaml
│   ├── rewrite-policy.yaml
│   └── header-injection.yaml
├── assignment-3/
│   ├── issuer.yaml
│   ├── clusterissuer.yaml
│   ├── certificate.yaml
│   └── gateway-tls.yaml
├── assignment-4/
│   └── networking-fundamentals.md
├── assignment-5/
│   └── troubleshooting.md
├── assignment-6/
│   └── production-design.md
├── bonus/
│   └── jwt-authentication.yaml
└── screen-recording-checklist.md
```

## Cleanup
```bash
kubectl delete -f bonus/
kubectl delete -f assignment-3/
kubectl delete -f assignment-2/
kubectl delete -f assignment-1/
helm uninstall cert-manager -n cert-manager
helm uninstall eg -n envoy-gateway-system
kubectl delete -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.1.0/standard-install.yaml
sudo /usr/local/bin/k3s-uninstall.sh
```
