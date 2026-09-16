# Applications

- Each subdirectory represents one namespace-scoped Argo CD Application. Add an
`app.yaml` file to the directory; the **directory** name becomes both the
Application name and its namespace. 

- Set `project` to deploy the application
through a specific Argo CD project. If it is omitted, the application uses the
`gitops-apps` project. Create projects via: [gitops/infra/argocd/values.yaml](../infra/argocd/values.yaml)


