# Networking Fundamentals

## Request Flow
The request to `https://api.company.local/orders` is resolved by DNS, carried over TCP on port 443, terminated by TLS at Envoy Gateway, and forwarded through the matching `HTTPRoute` to the `httpbin` backend.

## Control Points
- DNS maps the hostname to the EC2 public IP.
- Envoy Gateway selects the listener and route based on host and path.
- Gateway API policies apply request rewriting, retries, timeouts, and rate limits.
- Kubernetes Services provide stable backend discovery.
- `kube-proxy` programs service routing to healthy pod endpoints.
- The CNI layer handles pod-to-pod connectivity.

## Validation
```bash
kubectl get gateway -A
kubectl get httproute -A
kubectl get svc httpbin
kubectl get endpoints httpbin
curl -vk https://18.117.135.42
```
