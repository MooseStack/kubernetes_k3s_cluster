# Infrastructure components

Cluster infrastructure components belong in this directory. Components may be
managed with Helm, Kustomize, or plain Kubernetes manifests.

- [cert-manager](cert-manager): certificate management from the upstream
  [cert-manager Helm chart](https://cert-manager.io/docs/installation/helm/).
- [headlamp](headlamp): Kubernetes web UI from the upstream
  [Headlamp Helm chart](https://headlamp.dev/).
- [cloudnative-pg](cloudnative-pg): PostgreSQL operator from the upstream
  [CloudNativePG Helm chart](https://cloudnative-pg.io/).
