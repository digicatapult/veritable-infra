# Repository structure

This repository is structured as follows:

```
clusters/
    stage-cluster/ #cluster specific configuration
        app/
            env-services/ # Environment specific Application installations

            shared-config/ # Environment specific values and kustomizations for shared applications
            shared-sync.yaml # kustomization for loading all shared applications.
        base/
            flux-system/ # flux-system components
                gotk-components.yaml
                gotk-sync.yaml
                kustomization.yaml
            namespaces.yaml # creates namespaces used across the applications where applicable
            secrets-sync.yaml # kustomization for loading application secrets
            app-sync.yaml # kustomization pointing to this clusters env-services dir
            infra-sync.yaml # kustomization pointing to this clusters infrastructure specific installations
        infra/ # Cluster specific infrastructure installations where applicable
            cert-manager/ controller for managing certificates in K8S
            aws-ebs-csi-driver/ controller for managing ebs volumes as storage classes in AWS K8S
        secrets/
            ghcr_token.yml # SOPs encrypted secrets
shared/ # contains shared application deployment configuration for all clusters
    ...
certs/ # contains public keys for each environment
    local/
        github.asc
        user1.asc
        user2.asc
    production/
        sops.asc
docs/ # contains documentation references
```

## clusters

Contains per cluster configuration details and deployment `Kustomizations`. These are first broken down by environment (for example `kind-cluster`) and then into the `base` install and the application `secrets`.  For more environment specific information see [here](./cluster-information.md)

### app
The `app` section contains cluster specific application installations/configuration and references the shared application deployment
### base

The `base` section describes the flux-system components and allows for the loading of secrets and cluster applications.

### infra
The `infra` section describes cluster specific infrastructure to be installed, this usually allows for interaction with the PaaS providers specific resources.

### secrets

Contains SOPs encrypted secrets which will be loaded into the cluster and decrypted from within the cluster. See [secrets](./managing-secrets) for how to create new secrets for an environment.

## shared

Contains the application deployment specification that is shared amongst all cluster environments.

## certs

Contains public PGP certificates (keys) used to encrypt new secrets organised by the environments in which the corresponding private keys are deployed.

## docs

Contains documentation for the repository
