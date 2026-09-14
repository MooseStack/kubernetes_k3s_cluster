# Kubernetes (K3s)

This directory contains a local Ansible setup for installing, upgrading, or
removing a K3s cluster using the upstream collection: [k3s-ansible](https://github.com/k3s-io/k3s-ansible)

- The playbook will configure host-level requirements such as firewall rules,
  SELinux policies, and port forwarding as required by the collection.
- The kubeconfig is written to the remote host at `~/.kube/config` for the
  configured `ansible_user`, and the local ansible execution user also receives a copy
  saved to `~/.kube/config.new`.

## Prerequisites

1. Update the inventory (variables and hosts info): [1-install-k3s-with-ansible/inventory.yaml](inventory.yaml)

   - Set `k3s_install_state` variable in the inventory to `install`, `upgrade`, or `remove`.
   - Hosts must be members of `k3s_cluster` in either
`server` or `agent`.

2. (optional) - already in this repo, but if you want to update and reinstall the `k3s-ansible` collection:

```sh
ansible-galaxy collection install -r 1-install-k3s-with-ansible/requirements.yaml
```



The cluster variables are defined directly in `inventory.yaml` rather than in a
separate `playbooks/kubernetes/vars` directory.

## Run

Run the playbook against the whole cluster or limit it to a group or host:

```sh
ansible-playbook 1-install-k3s-with-ansible/playbook.yaml
```
