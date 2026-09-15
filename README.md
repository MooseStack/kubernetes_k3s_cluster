# kubernetes_k3s_cluster

## Install k3s using Ansible

- [1-install-k3s-with-ansible/README.md](1-install-k3s-with-ansible/README.md)
  - Supports installation of k3s, Helm, and Argo CD on the destination.
  - `pip3 install --user ansible`

## Manage the cluster with GitOps (Argo CD)

After Argo CD is installed, bootstrap the GitOps resources from this repository
with the Ansible playbook by setting `argo_bootstrap: true` in
`1-install-k3s-with-ansible/inventory.yaml`:

```sh
cd 1-install-k3s-with-ansible
ansible-playbook playbooks/main.yaml
```

Alternatively, apply the manifests directly:

```sh
kubectl --kubeconfig <your-KUBECONFIG> apply -f 2-argo_bootstrap
```

This creates:

- The `gitops-infra` Argo CD project.
- The `gitops-infra` namespace and repository connection.
- An ApplicationSet that manages the kubernetes compatible Argo CD resources in
  `gitops/infra/argocd` and infrastructure components in
  `gitops/infra/components`.
- An app-of-apps Application that manages definitions in
  `gitops/app-of-apps`.

Argo CD watches the active k3s resources and applies changes automatically with
prune and self-heal enabled. Add Argo CD projects, repository connections, and
Applications under `gitops/infra/argocd`; add application definitions under
`gitops/app-of-apps`.

Infrastructure components live under `gitops/infra/components`. Each component
is discovered as an Argo CD Application and may use Helm, Kustomize, or plain
Kubernetes manifests.
