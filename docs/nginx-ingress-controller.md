# Nginx-ingress Controller

## Preface
We use the [nginx-ingress](https://kubernetes.github.io/ingress-nginx/) controller for a number of things inside our infrastructure.  It acts as an endpoint for all our APIs and in some cases our frontend clients, it terminates SSL, it handles authentication for certain ingress paths and also sets up websocket connections to some services.

## Future ingress-controller issues
In `v1.22.` onwards we will need to adjust all services to using `ingressClass` on all `ingress` objects. See [this document](https://kubernetes.io/blog/2021/07/26/update-with-ingress-nginx/) for reference.

We currently use the [Bitnami chart](https://github.com/bitnami/charts/tree/master/bitnami/nginx-ingress-controller) version `7.4.0`

* `v1.21.1` in `kind-cluster`

## Nginx as an API Gateway
We effectively use nginx-ingress as an API gateway for all of our APIs.  The ingress resources of the helm charts themselves define a path which we then over-ride to give a more API compliant prefix's, please refer to the [wasp-thing-service](../shared/wasp-thing-service/values.yaml) `ingress.paths` and `ingress.annotations` as an example.

## TLS Termination
We typically terminate SSL on the ingress controller itself.  This is done on a cluster by cluster basis so you'll find the individual configurations in [each clusters values-nginx.yaml](../clusters/stage-cluster/app/shared-config/values-nginx.yaml) We setup the SSL certificate in the `certificates.yaml` in the same directory, this uses [cert-managers](https://cert-manager.io) to create an ACME certificate and store it in a secret.  We set this using `extraArgs.default-ssl-certificate` and pointing it at the relevant secret.

## Authentication
We set authentication in [shared nginx configuration](../shared/nginx/values.yaml) using `config.global-auth-url` and pointing it to the FQDN of of an authentication service.  We can also at this point add additional headers and responses from the authenication-service should we wish.

Certain ingresses have to be unauthenticated (such as the authentication-service itself) and we do this by setting an annotation in their `ingress.annotations` with a value of `nginx.ingress.kubernetes.io/enable-global-auth: "false"`.

## Upgrading Websocket connections
Nginx handles this by default but you will need to upgrade the timeout.  Again this is achieved by setting an annotation in the ingress resource that requires a websocket.  By default all services that require a websocket set the timeout annotations in their own helm charts.
