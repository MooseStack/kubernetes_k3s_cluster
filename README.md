# kubernetes_k3s_cluster

Installation and configuration of k3s, Helm, and ArgoCD using Ansible and GitOps.

## Requirements
- `x86-64` or `ARM`/`aarch64` architectures
- Tested Operating Systems: Fedora/RHEL family distros (AlmaLinux, Rocky, Oracle Linux, CentOS Stream, etc.). Although, should work on Debian/Ubuntu family distros as well, just havent tested there yet.
- `ansible`, can install using `pip3 install --user ansible`

## Install and configuration using Ansible

- [1-install-k3s-with-ansible/README.md](1-install-k3s-with-ansible/README.md)
  - Inventory and variables: [inventory.yaml](1-install-k3s-with-ansible/inventory.yaml)
    - if `argo_bootstrap: true`, it will:
       - Create the gitops-infra related manifests and applicationSet defined in: [2-argo_bootstrap](2-argo_bootstrap)
       - GitOps will be enabled, with ArgoCD syncing with the [gitops](gitops) folder
       - ArgoCD will manage itself via GitOps by taking ownership of the initial boostrap via [Chart.yaml](gitops/infra/argocd/Chart.yaml) & [values.yaml](gitops/infra/argocd/values.yaml). Reference: [README.md](gitops/infra/argocd/README.md)