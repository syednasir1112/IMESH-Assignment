Overview

This section explains how a client request reaches an application deployed inside the Kubernetes cluster and how the different networking components interact throughout the request lifecycle.

Request Flow

When a user accesses the endpoint:

https://api.company.local/orders

the request follows the sequence below:

The client performs a DNS lookup for api.company.local.
DNS resolves the hostname to the public IP address of the Kubernetes node.
A TCP connection is established on port 443.
The TLS handshake is completed and the certificate is validated.
Envoy Gateway receives the HTTPS request and terminates TLS.
The Gateway listener matches the request based on the configured hostname.
The associated HTTPRoute evaluates the request path and routing rules.
Gateway API policies such as URL rewriting, retries, timeouts, and rate limiting are applied.
The request is forwarded to the Kubernetes Service associated with the backend application.
Kubernetes Service load-balances traffic to one of the healthy backend pods.
The application processes the request and returns the response through the same path.
Key Networking Components
DNS Resolution

DNS is responsible for resolving the hostname to the external IP address of the cluster entry point.

Example

api.company.local → 18.117.135.42
Envoy Gateway

Envoy Gateway acts as the ingress layer for external traffic and performs:

Host-based routing
Path-based routing
TLS termination
Traffic management
Policy enforcement
Gateway API Resources

Gateway API resources define how traffic enters and moves through the cluster.

Components used in this assignment include:

GatewayClass
Gateway
HTTPRoute
ReferenceGrant

These resources determine which requests are accepted and where they should be forwarded.

Kubernetes Services

Kubernetes Services provide stable networking endpoints for applications.

Benefits include:

Service discovery
Load balancing
Stable virtual IP addresses
Backend abstraction

Example:

httpbin.apps.svc.cluster.local
kube-proxy

kube-proxy maintains networking rules on cluster nodes and forwards traffic from Services to healthy pod endpoints.

Responsibilities include:

Service routing
Endpoint selection
Load distribution
Container Network Interface (CNI)

The CNI plugin provides networking between pods running in the cluster.

Functions include:

Pod-to-pod communication
Network address allocation
Routing between nodes
Cluster network management
End-to-End Traffic Path
Client
  │
  ▼
DNS Resolution
  │
  ▼
EC2 Public IP
  │
  ▼
Envoy Gateway
  │
  ▼
Gateway API Routing
  │
  ▼
Kubernetes Service
  │
  ▼
Backend Pod
Validation Commands

The following commands can be used to verify the networking configuration and routing behavior.

Verify Gateway Resources
kubectl get gateway -A
kubectl get httproute -A
Verify Service Discovery
kubectl get svc httpbin -n apps
kubectl get endpoints httpbin -n apps
Verify External Connectivity
curl -vk https://18.117.135.42
Conclusion

The networking architecture uses Envoy Gateway, Gateway API resources, Kubernetes Services, and cluster networking components to securely route external traffic to backend applications. Traffic is processed through DNS resolution, TLS termination, routing policies, service discovery, and pod-level load balancing before reaching the target application.