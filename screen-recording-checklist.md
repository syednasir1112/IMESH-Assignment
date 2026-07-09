# IMESH Assessment - Screen Recording Checklist

## Environment
- [ ] Confirm the EC2 host is reachable at `18.117.135.42`.
- [ ] Confirm `kubectl`, `helm`, and `curl` are available.
- [ ] Confirm the security group allows ports `22`, `80`, and `443`.
- [ ] Open the repository on the EC2 host.
- [ ] Start screen recording with audio enabled.

## Demo Flow
- [ ] Install k3s.
  ```bash
  curl -sfL https://get.k3s.io | sh -
  ```
- [ ] Install Gateway API.
  ```bash
  kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.1.0/standard-install.yaml
  ```
- [ ] Install Envoy Gateway.
  ```bash
  helm repo add envoy-gateway https://gateway.envoyproxy.io
  helm repo update
  helm install eg envoy-gateway/envoy-gateway -n envoy-gateway-system --create-namespace --version v1.2.0
  ```
- [ ] Install cert-manager.
  ```bash
  helm repo add jetstack https://charts.jetstack.io
  helm repo update
  helm install cert-manager jetstack/cert-manager -n cert-manager --create-namespace --version v1.15.1 --set crds.enabled=true
  ```
- [ ] Apply the assessment manifests.
  ```bash
  kubectl apply -f assignment-1/
  kubectl apply -f assignment-2/
  kubectl apply -f assignment-3/
  kubectl apply -f bonus/
  ```

## Verification
- [ ] Confirm cluster readiness.
  ```bash
  kubectl get nodes
  kubectl get pods -A
  ```
- [ ] Confirm Gateway API resources.
  ```bash
  kubectl get gateway -A
  kubectl get httproute -A
  kubectl get certificate -A
  ```
- [ ] Verify application routing.
  ```bash
  curl -H "Host: web.company.local" http://18.117.135.42
  curl -H "Host: api.company.local" http://18.117.135.42/get
  curl -H "Host: test.company.local" http://18.117.135.42
  ```
- [ ] Verify TLS.
  ```bash
  curl -vk https://18.117.135.42
  ```

## Cleanup
- [ ] Remove Kubernetes resources.
  ```bash
  kubectl delete -f bonus/
  kubectl delete -f assignment-3/
  kubectl delete -f assignment-2/
  kubectl delete -f assignment-1/
  ```
- [ ] Remove cluster components if needed.
  ```bash
  helm uninstall cert-manager -n cert-manager
  helm uninstall eg -n envoy-gateway-system
  sudo /usr/local/bin/k3s-uninstall.sh
  ```
