# Managing secrets

Secrets for the application are stored in repo as SOPs encrypted Kubernetes secrets. The keys for decrypting these secrets will be loaded into a cluster on creation and should never be retained on any other device. Public certificates (keys) corresponding to the decryption key are then stored in this repository under [certs](./repository-structure.md#certs) and can be used to encrypt new secrets for use by the deployed application.

## Local development secrets

To prevent each developer form having to setup and maintain their own cluster infrastructure there is a special environment called `kind-cluster` which can be used by multiple developers who will share a set of encrypted secrets. Each secret is essentially encrypted by multiple PGP keys (one per developer) so that all registered developers can decrypt the secrets without sharing keys.

As a consequence of this design choice secrets used for local development must be considered shared resources amongst application developers. Secrets that could expose personal details or harm to an individual should not be stored in the `kind-cluster`.

## Creating new secrets

To create new secrets you will need to have [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) and [Mozilla SOPs](https://github.com/mozilla/sops). On MacOS these can both be installed using [Homebrew](https://brew.sh/).

To create a new secret you first need to generate the secret locally using `kubectl` then you will encrypt it with `SOPs`. As an example we will create an example of a `kubernetes.io/dockerconfigjson` secret for connecting to a docker registry and encrypting that for the `kind-cluster` deployment.

We can create a secret using:

```sh
kubectl create secret docker-registry <secret-name> \
--namespace=<secret-namespace> \
--docker-server=<your-registry-server> \
--docker-username=<your-name> \
--docker-password=<your-pword> \
--docker-email=<your-email> \
--dry-run=client \
--output=yaml > ./clusters/kind-cluster/secrets/<secret-name>.unc.yaml
```

Replacing tags with appropriate values:

* `<secret-name>` is the name of the secret to create
* `<secret-namespace>` is the namespace in Kubernetes to create the secret in
* `<your-registry-server>` is your Private Docker Registry FQDN. (https://index.docker.io/v1/ for DockerHub)
* `<your-name>` is your Docker username.
* `<your-pword>` is your Docker password.
* `<your-email>` is your Docker email.

This will generate the secret at the path `./clusters/kind-cluster/secrets/<secret-name>.unc.yaml` which should look something like

```yaml
apiVersion: v1
data:
  .dockerconfigjson: eyJhdXRocyI6eyJodHRwczovL2luZGV4LmRvY2tlci5pby92MS8iOnsidXNlcm5hbWUiOiJ0ZXN0LXVzZXIiLCJwYXNzd29yZCI6InN1cGVyX3NlY3JldCIsImVtYWlsIjoidGVzdC11c2VyQGV4YW1wbGUuY29tIiwiYXV0aCI6ImRHVnpkQzExYzJWeU9uTjFjR1Z5WDNObFkzSmxkQT09In19fQ==
kind: Secret
metadata:
  creationTimestamp: null
  name: test-secret
  namespace: wasp
type: kubernetes.io/dockerconfigjson
```

Next we will need to encrypt the secret with SOPs. This can be done using the script `encrypt-secrets.sh` with the cluster to update as follows:

```sh
./scripts/encrypt-secrets.sh kind-cluster
```

This will ensure any unencrypted secrets in the cluster specific `secrets` directory are encrypted with all public keys configured for that cluster.
