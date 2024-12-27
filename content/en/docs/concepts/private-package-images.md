---
title: Private Package Images
draft: false
images: []
weight: 750
toc: false
---

Package Operator can be configured to authenticate package image
pull operations with [ImagePullSecrets](https://kubernetes.io/docs/concepts/containers/images/#creating-a-secret-with-a-docker-config).

## Using the package-operator ServiceAccount

To give cluster-administrators control over the pull secrets for package images
on the global cluster-level, Package Operator automatically fetches
`imagePullSecrets` of its own ServiceAccount and authenticates pull operations
for all package images in the cluster with these secrets.

This can be very helpful if all package-images are stored
within the same namespace of a private registry server.

To enable this feature, [create and attach an ImagePullSecret](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account)
to package-operator's service-accounts.

```bash
kubectl create secret -n package-operator-system \
  docker-registry myregistrykey \
  --docker-server=registry.example.com \
  --docker-username=DUMMY_USERNAME \
  --docker-password=DUMMY_DOCKER_PASSWORD \
  --docker-email=DUMMY_DOCKER_EMAIL

kubectl patch serviceaccount \
  -n package-operator-system \
  package-operator \
  -p '{"imagePullSecrets": [{"name": "myregistrykey"}]}'

# Package Operator will now authenticate requests to
# `registry.example.com` with the contents of Secret/myregistrykey.
```
