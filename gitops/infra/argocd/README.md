# Argo CD infrastructure chart

This directory is a Helm chart rendered by the `gitops-infra-applicationset`
ApplicationSet. Add or update Argo CD projects and repository connections in
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
