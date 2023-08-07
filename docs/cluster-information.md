# Cluster Information

We currently manage several clusters using this repository:

- [kind-cluster](#kind-cluster)


## kind-cluster

This is a general purpose development cluster, running on kind on the local machine of a developer.  Notable specicifities are as follows:

- Infra - There are no additional PaaS helm modules or etc required to run in this cluster hence the kustomization is omitted
- TLS - we do not have TLS terminating on the `nginx-ingress-controller` in this cluster, everything is plain HTTP
- Helm packages - we do not specify helm versions for shared components so helm will always upgrade to the most recent tag
- TCP passthrough - we allow access to the databases via the `nginx-ingress-controller` see [here](../clusters/kind-cluster/app/shared-config/values-nginx.yaml) for more details

