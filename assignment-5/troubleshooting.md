Overview

This section documents common issues that may occur when working with Envoy Gateway, Gateway API resources, cert-manager, and Kubernetes Services. It also provides validation commands that can be used to identify and resolve problems during deployment and operation.

1. HTTP 503 Service Unavailable
Description

A 503 Service Unavailable response indicates that Envoy Gateway is unable to successfully forward traffic to a healthy backend service.

Possible Causes
Application pods are not running.
Pod readiness probes are failing.
Service selectors do not match pod labels.
Service endpoints are not populated.
HTTPRoute is not attached to the Gateway.
Backend references are incorrect.
Verification

Check application health:

kubectl get pods -A

Verify services:

kubectl get svc -A

Verify endpoints:

kubectl get endpoints -A

Verify Gateway and Routes:

kubectl get gateway -A
kubectl get httproute -A

Review Envoy Gateway logs:

kubectl logs -n envoy-gateway-system deploy/envoy-gateway
Resolution
Ensure application pods are in a Running state.
Confirm Service selectors match pod labels.
Verify endpoints are populated.
Validate HTTPRoute backend references.
Confirm Gateway resources are accepted and programmed.
2. Request Timeout
Description

A timeout occurs when the backend application does not respond within the timeout period configured in Envoy Gateway policies.

Possible Causes
Slow application response.
Backend processing delays.
Incorrect timeout policy configuration.
Route policy attached to the wrong resource.
Verification

Inspect HTTPRoute configuration:

kubectl get httproute -A -o yaml

Verify pod status:

kubectl get pods -A

Check Envoy Gateway logs:

kubectl logs -n envoy-gateway-system deploy/envoy-gateway
Resolution
Increase timeout values if necessary.
Optimize backend application performance.
Verify the timeout policy is attached to the intended route.
Confirm route configuration has been accepted by Envoy Gateway.
3. Certificate Validation Failure
Description

Certificate-related failures occur when TLS certificates cannot be issued, validated, or presented correctly during HTTPS connections.

Possible Causes
Certificate resource is not ready.
TLS secret is missing.
ClusterIssuer or Issuer is not healthy.
cert-manager controller is not functioning.
Certificate chain is incomplete.
Verification

Check certificate status:

kubectl get certificate -A

Review certificate details:

kubectl describe certificate -A

Verify TLS secrets:

kubectl get secret -A | grep imesh

Check cert-manager logs:

kubectl logs -n cert-manager deploy/cert-manager

Validate TLS handshake:

openssl s_client \
-connect 18.117.135.42:443 \
-servername api.company.local
Resolution
Verify Issuer and ClusterIssuer readiness.
Ensure TLS secrets are generated successfully.
Confirm certificate resources report READY=True.
Review cert-manager logs for issuance errors.
4. Route Matching Issues
Description

Requests may not reach the intended backend if hostname or path matching rules are incorrectly configured.

Possible Causes
Hostname mismatch.
Incorrect path configuration.
Invalid backend reference.
Missing route attachment.
Listener hostname restrictions.
Verification

Inspect HTTPRoutes:

kubectl get httproute -A

Review route configuration:

kubectl describe httproute -A

Validate hostname routing:

curl -H "Host: web.company.local" \
http://18.117.135.42

Validate API routing:

curl -H "Host: api.company.local" \
http://18.117.135.42/get
Resolution
Verify hostnames match Gateway listener configuration.
Ensure paths match HTTPRoute definitions.
Validate backend service references.
Confirm routes are attached to the correct Gateway.
5. Policy Configuration Issues
Description

Traffic management features such as retries, timeouts, URL rewrites, and rate limiting may not function as expected if policies are not correctly attached.

Possible Causes
Incorrect target references.
Unsupported policy configuration.
Policy attached to the wrong resource.
Validation errors during reconciliation.
Verification

Inspect Gateway resources:

kubectl get gateway -A

Inspect routes:

kubectl get httproute -A

Verify certificates:

kubectl get certificate -A

Confirm supporting controllers are healthy:

kubectl get pods -n cert-manager
kubectl get pods -n envoy-gateway-system
Resolution
Verify policy target references.
Ensure policy resources are accepted by Envoy Gateway.
Review controller logs for reconciliation errors.
Validate policy behavior using test requests before updating manifests.
Conclusion

Most Gateway API and Envoy Gateway issues can be identified by systematically validating application health, service discovery, route configuration, policy attachment, and certificate status. Following the verification and resolution steps above provides a structured approach for diagnosing and resolving common deployment and traffic management problems.