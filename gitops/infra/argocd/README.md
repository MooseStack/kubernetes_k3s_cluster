# Argo CD infrastructure chart

This Helm chart takes over the install from Ansible of ArgoCD, and manages the following resources:

1. ArgoCD Projects, using `projects` key in  [values.yaml](values.yaml)
2. ArgooCD Git Repository connections, using `repositories` key in  [values.yaml](values.yaml)
3. An `ApplicationSet` that creates new apps for each folder in [gitops/apps](../../apps)
   - Example: [../../apps/tailscale/app.yaml](../../apps/tailscale/app.yaml)
      - This will create a "tailscale" namespace since thats the folder name
      - if `project` is not defined in `app.yaml` then it will fallback to project name `gitops-apps`
4. Upstream Argo CD chart settings are maintained under the `argocd` key in
[values.yaml](values.yaml). The upstream dependency version and repository are pinned in
[Chart.yaml](Chart.yaml).
