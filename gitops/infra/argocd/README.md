# Argo CD infrastructure chart

This Helm chart takes over the install from Ansible of ArgoCD, and manages the following resources:

1. The `gitops-infra` namespace, project, repository connection, and infrastructure
   `ApplicationSet` previously installed from `2-argo_bootstrap`
2. ArgoCD Projects, using `projects` key in [values.yaml](values.yaml)
3. ArgoCD Git Repository connections, using `repositories` key in [values.yaml](values.yaml)
4. An `ApplicationSet` that creates new apps for each folder in [gitops/apps](../../apps)
   - Example: [../../apps/tailscale/app.yaml](../../apps/tailscale/app.yaml)
      - This will create a "tailscale" namespace since thats the folder name
      - if `project` is not defined in `app.yaml` then it will fallback to project name `gitops-apps`
5. Upstream Argo CD chart settings are maintained under the `argocd` key in
[values.yaml](values.yaml). The upstream dependency version and repository are pinned in
[Chart.yaml](Chart.yaml).

The generated Argo CD application is intentionally named `argocd` so its Helm
release and resource names remain compatible with the initial Ansible install.

## Initial login credentials

The initial Argo CD username is `admin`. Retrieve the generated initial
password with:

```sh
echo "Username: admin"
echo -n "Password: "
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath='{.data.password}' | base64 --decode
echo
```
