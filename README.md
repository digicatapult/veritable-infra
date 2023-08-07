# veritable-infra

This repository contains Kubernetes deployment descriptions for [fluxcd](https://toolkit.fluxcd.io/) to use when deploying an application. If you wish to setup the application stack for development purposes, or add a new service to the application this is the place you will register those services.

## Getting started

If you would like to deploy a local version of the application for development using [Kind](https://kind.sigs.k8s.io/) instructions for getting started can be found [here](./docs/getting-started.md).
## Repository structure

For an overview of the structure of this repository see [here](./docs/repository-structure.md).

## Managing Secrets

For managing secret values within a cluster see the documentation [here](./docs/managing-secrets.md).

## Deploying a new cluster

To deploy a new cluster please see documentation [here](./docs/deploying-new-clusters.md).

## Existing cluster information

For information on the current clusters managed by this repository and their specific configurations please see the documentation [here](./docs/cluster-information.md)
