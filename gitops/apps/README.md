# Applications

Each subdirectory represents one namespace-scoped Argo CD Application. Add an
`app.yaml` file to the directory; the directory name becomes both the
Application name and its namespace.

The `sources` list is passed to Argo CD. Use `path` for a Helm chart or
Kustomize directory in Git, or use `chart` for a Helm chart in a chart or OCI
registry:

```yaml
sources:
  - repoURL: https://github.com/example/example.git
    targetRevision: main
    path: deploy
```

```yaml
sources:
  - repoURL: oci://registry.example.com/helm
    targetRevision: 1.2.3
    chart: example
```

To keep values in this repository while deploying a chart from elsewhere, add a
second source with `ref: values` and reference the file from the chart source:

```yaml
sources:
  - repoURL: https://charts.example.com
    chart: example
    targetRevision: 1.2.3
    helm:
      valueFiles:
        - $values/gitops/apps/example/values.yaml
  - repoURL: https://github.com/example/example.git
    targetRevision: main
    ref: values
```

Optional `ignoreDifferences` is applied to the generated Application. The
`apps` ApplicationSet is defined in
`gitops/infra/argocd/templates/apps-applicationSet.yaml`.

This repository uses two ApplicationSets: the infrastructure ApplicationSet and
this `apps` ApplicationSet for namespace-scoped workloads.
