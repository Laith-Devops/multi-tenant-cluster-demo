<<<<<<< HEAD
# Multi-Tenant Kubernetes Platform
This demo is local only and is not suitable for production environment and this is also for testing purposes only, but it shows the importantce of helm.
This demo demonstrates the importance of kubernetes for real life world projects, as this demo simulation hosts nginx, redis and postgres together in different namespaces, 1 nginx pod and 1 redis pod and 1 postgres pod in one namespace, 1 nginx pod and 1 redis pod and 1 postgres pod also in another namespace. This demo also showcases the importance of using helm in kubernetes, instead of having a lot of yaml files, helm reduces that a lot, in this demo simulation.



# IMPORTANT:
Postgres has no password set
No POSTGRES_PASSWORD env var in the StatefulSet. Anyone inside the cluster can connect to it without credentials.

Redis has no auth. No requirepass set. Open to anything inside the cluster.

No NetworkPolicies. Tenant-001 can technically talk to tenant-002's Postgres and Redis. The namespaces are logically isolated but not network isolated. A NetworkPolicy would lock that down.

No resource limits

No requests or limits on any container. One misbehaving pod could eat all node resources and starve the others.

No RBAC

No ServiceAccounts or Roles defined. Everything runs with the default ServiceAccount.

HTTP only, no TLS

Traffic between the gateway and clients is unencrypted.

# Why helm?
Without Helm, adding a third tenant means manually creating 5+ new YAML files, copy-pasting everything, and doing a find-and-replace on the tenant name across every file. Miss one and something breaks.
With Helm, adding a third tenant is one line in values.yaml:
tenants:
  - name: tenant-001
    domain: tenant-001.example.com
  - name: tenant-002
    domain: tenant-002.example.com
    add this domain after the structure is completed for better and quick results.
  //- name: tenant-003       
   // domain: tenant-003.example.com
    
Run helm upgrade multi-tenant-demo . and everything from namespaces, Postgres, Redis, Nginx, Hpa, HTTPRoute get created automatically for tenant-003. No new files, no copy-pasting, no risk of typos.
That's what Helm does. It turns repetitive YAML into reusable templates with variables. You define the structure once, and values.yaml drives everything else.
This is done either before creating the helm structure or after it, if adding a tenante to the values.yaml file, after the strcutre is completed, everything will work and traffic will be sent to the tenant-003 namespace, but this demo uses fake domains, so it will not work, refer to the important section in this file

#Features:

Namespaces for multi-tenant isolation
Tenant services like:

NGINX (made public through API Gateway or HTTPRoute)
Postgres (PVC + StatefulSet)
Redis (Deployment and PVC)
NGINX's Horizontal Pod Autoscaling (HPA)
Persistent Volumes (PVCs) for services that need to keep their state
HTTPRoute for gateway API routing




Without helm, this entire demo needs at least 14 yaml files:
# Namespaces:

  tenant-001.yaml
  tenant-002.yaml
  gateway-system.yaml

# Postgres StatefulSets + PVCs:

  postgres-tenant-001.yaml
  postgres-tenant-002.yaml

# Redis Deployments + PVCs:

  redis-tenant-001.yaml
  redis-tenant-002.yaml

# NGINX Deployments:

  nginx-tenant-001.yaml
  nginx-tenant-002.yaml

# Horizontal Pod Autoscalers (HPA):

  nginx-hpa-tenant-001.yaml
  nginx-hpa-tenant-002.yaml

# API Gateway:

  api-gateway.yaml

# HTTPRoutes:

  nginx-route-tenant-001.yaml
  nginx-route-tenant-002.yaml


# Of course you can put all of these yaml files in a single yaml file, but that does not mean the number of the yaml files decreases, that means the yaml files are inside a single yaml file.


# Requirements:
1. Kubernetes Version: v1.21+ but 1.26+ is recommended

2. Then these commands, these are required to allow kubernetes to work properly:
echo 'net.ipv4.ip_forward = 1' >> /etc/sysctl.conf

echo'net.bridge.bridge-nf-call-iptables = 1' >> /etc/sysctl.conf 

sysctl -p

4. Helm v3 — check by using helm --version, if not, install helm

5. Gateway API CRDs — must be installed before applying your Gateway and HTTPRoute resources:

kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.2.0/standard-install.yaml

Verify:
kubectl get crd | grep gateway

6. NGINX Gateway Fabric — the actual controller that programs the gateway:
kubectl apply -f https://raw.githubusercontent.com/nginxinc/nginx-gateway-fabric/v1.4.0/deploy/default/deploy.yaml

7. Metrics Server — required for HPA:
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
To verify if it is installed, run:
kubectl top pods -n tenant-001


=======
# helm-kubernetes-tier-architecture
>>>>>>> 4569c5b6dfd51ab590e5781620243f59aa90c7f9
