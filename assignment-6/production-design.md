# Production Design

## Target State
Three Kubernetes clusters distributed across availability zones or regions, with HTTPS-only traffic, separate external and internal APIs, and a 99.9%+ availability target.

## Gateway Strategy
Envoy Gateway is the edge control plane. It keeps the design Kubernetes-native, supports Gateway API resources directly, and provides the traffic controls required for retries, timeouts, and rate limiting without introducing a full service mesh.

## Topology
- Primary and secondary clusters serve live traffic.
- A third cluster remains available for failover.
- External traffic terminates at the gateway layer.
- Internal traffic stays on the cluster network and is protected with policy.

## Security
- TLS terminates at Envoy Gateway.
- cert-manager owns certificate issuance and renewal.
- Secrets remain in-cluster and are not exported to local workstations.
- RBAC remains scoped to the namespace and workload level.

## Operations
- Prometheus scrapes gateway and workload metrics.
- Grafana provides traffic and error-rate visibility.
- Centralized logging captures gateway and application events.
- Alerts focus on gateway availability, 5xx rates, latency, and certificate expiry.

## Resilience
- Multiple replicas per service.
- Readiness probes gate traffic to healthy pods.
- Retry and timeout policies are applied at the route or backend policy layer.
- Services remain independently addressable so a failure in one backend does not take down the full edge.

## Cost Control
- Keep the gateway footprint small.
- Right-size CPU and memory requests.
- Use short log retention for non-audited environments.
- Avoid overlapping traffic policies that do not change behavior.
