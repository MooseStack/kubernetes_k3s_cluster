## rbac.yaml

- Using a token to login to the headlamp dashboard
- Retrieve the long lived token: 

```sh
kubectl get secret headlamp-admin-token-secret -n headlamp -o jsonpath='{.data.token}' | base64 --decode
```