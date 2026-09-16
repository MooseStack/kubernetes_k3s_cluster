# Argo CD infrastructure chart

This directory is a Helm chart rendered by the `gitops-infra-applicationset`
ApplicationSet. The repository uses two ApplicationSets: one for infrastructure
resources and one for namespace-scoped applications under `gitops/apps`.

Add or update Argo CD projects and repository connections in
[`values.yaml`](./values.yaml) instead of creating one manifest per resource.

Projects use the following fields:

- `name`
- `description`
- `sourceRepos`
- `destinations`
- optional `clusterResourceWhitelist`
- optional `namespaceResourceWhitelist`

Repositories use `name`, `project`, and `url`. The chart creates the repository
Secret as `argo-gitrepo-<name>`.

The `apps` project is used by the `apps` ApplicationSet in
[`templates/apps-applicationSet.yaml`](./templates/apps-applicationSet.yaml).
Add applications under `gitops/apps/<name>/app.yaml`. The `sources` list is
passed directly to the generated Argo CD Application, so it can describe a
local Git Helm chart or Kustomize directory, an external Git repository, or an
OCI/registry Helm artifact. The application directory name becomes its
namespace. Multiple sources can keep Helm values in this repository while the
chart comes from another repository.

For example, add another project and repository with:

```yaml
projects:
  - name: example
    description: Argo CD project for example
    sourceRepos:
      - https://github.com/example/example.git
    destinations:
      - namespace: example
        server: https://kubernetes.default.svc

repositories:
  - name: example
    project: example
    url: https://github.com/example/example.git
```
