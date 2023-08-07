# Scripts

This document contains an overview of the scripts in this repo, their function and when they should be used.

## add-kind-cluster.sh
This script can be used to provision a local `kind-cluster` on your local machine.  It has no arguments or additional variables that can be supplied to it.

It does the following:
1. checks the environment is sane and that you have the necessary dependencies.
2. create a local docker registry in a continer bound to port 5000.
3. creates the kind cluster with a load of locally forwarded ports to what will be the database services and the docker registry.
4. binds the docker registry network and the kind network together.
5. creates a configmap referring to the local docker registry.

## install-flux.sh
This script provisions flux onto a local `kind-cluster`.  It is designed to do the following:

1. check your environment for `GITHUB_TOKEN` environment variable.
2. check to see if you are using a version of bash > 4.2
3. check to see if your cluster is setup correctly and the context is accessible.
4. install flux (depending on the version you have locally installed) onto your kind cluster
5. install your github token as a secret in the cluster so it can pull repos.
6. setup a git source (based on the supplied repo value or default repo/branch).
7. add a kustomization which will give flux the path to start building deps at.

Usage for this script is as follows:
```console
$ ./scripts/install-flux.sh -h
Installs flux onto a cluster and sets up the secrets, git sources and kustomizations necessary to get started

Usage:
  ./scripts/install-flux.sh [ -h ] [ -g <git_repository> ] [ -b <base_branch> ] [ -c <kind_context_name> ] [ -p <base_path> ]

Options:
  -g        Specify an alternative git repository. Note the default assumes http authentication
            (default: https://github.com/CDECatapult/wasp-k8s-infra/)
  -b        Specify an alternative base branch to use.
            (default: main)
  -c        Specify the context name of your cluster
            (default: kind-wasp-k8s-infra)
  -p        Specify an alternative base path
            (default: ./clusters/kind-cluster/base)

Flags: 
  -h        Prints this message
  ```
  
## add-kind-user.sh

This script can be used when a developer is setting up a local `kind-cluster` in order to get access to the shared encrypted secrets for that cluster. The script in essence does the following:

1. checks that the environment is sane; that you have the necessary dependencies, that you have a running local kind cluster, and that you have access to this repository
2. creates a new PGP key for your local cluster
3. pushes the public key to a new branch (named like `update-user-key-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`)
4. installs the PGP private key in your local Kind cluster

Note that a new keyring is used to generate the key and this is intentionally removed from your local system after this process has completed. The private key should only be accessible by your cluster to maintain the security of the shared secrets.

Usage for this script is as follows:

```console
$ ./scripts/add-kind-user.sh -h
Adds a new user key for the kind-cluster deployment and pushes the key to a new branch

Usage:
  ./scripts/add-kind-user.sh [ -h ] [ -g <git_repository> ] [ -b <base_branch> ] [ -c <kind_context_name> ]

Options:
  -g        Specify an alternative git repository. Note the default assumes ssh auth!
            (default: git@github.com:CDECatapult/wasp-k8s-infra.git)
  -b        Specify an alternative base branch to clone
            (default: main)
  -c        Specify the context name of your local kind cluster
            (default: kind-wasp-k8s-infra)

Flags:
  -h        Prints this message
```

## encrypt-secrets.sh

This script can be used for encrypting freshly created secrets with the correct keys for the desired cluster. This script will check for presence in your `$PATH` of the `gpg` and `sops` executables. The script is invoked with the following usage:

```console
$ ./scripts/encrypt-secrets.sh -h
Encrypts secrets for a specified cluster

Usage:
  ./scripts/encrypt-secrets.sh [ -h ] [ -a ] <cluster_name>

Flags:
  -a        Re-encrypts all secrets. You must have a valid PGP secret key in your GPG keyring for the environment for this operation to succeed
  -h        Prints this message
```
Note that the option `-a` is intended for use only by a privileged user for a given cluster environment and is mainly used by a github action to authorise new users to the `kind-cluster` environment. In normal usage secrets should be replaced and not re-encrypted!
