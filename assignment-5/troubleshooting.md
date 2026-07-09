# Troubleshooting

## 503 Service Unavailable
Check pod readiness, service selectors, endpoint population, and route attachment.
```bash
kubectl get pods -A
kubectl get svc -A
kubectl get endpoints -A
kubectl get gateway -A
kubectl get httproute -A
kubectl logs -n envoy-gateway deploy/envoy-gateway
```

## Request Timeout
Confirm the backend is responding within the configured limit and that the timeout policy is attached to the intended route.
```bash
kubectl get httproute -A -o yaml
kubectl get pods -A
kubectl logs -n envoy-gateway deploy/envoy-gateway
```

## Certificate Failure
Verify the certificate resource, issued secret, and cert-manager controller status.
```bash
kubectl get certificate -A
kubectl describe certificate -A
kubectl get secret -A | grep imesh
kubectl logs -n cert-manager deploy/cert-manager
openssl s_client -connect 18.117.135.42:443 -servername api.company.local
```

## Route Mismatch
Check hostname, path match, and backend reference alignment.
```bash
kubectl get httproute -A
kubectl describe httproute -A
curl -H "Host: web.company.local" http://18.117.135.42
curl -H "Host: api.company.local" http://18.117.135.42/get
```

## Policy Issues
Validate the attached Envoy Gateway policies before changing the manifests.
```bash
kubectl get gateway -A
kubectl get httproute -A
kubectl get certificate -A
kubectl get pods -n cert-manager
```
