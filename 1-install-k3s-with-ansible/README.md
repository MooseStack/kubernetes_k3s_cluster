# Kubernetes (K3s)

This directory contains a local Ansible setup for installing, upgrading, or
removing a K3s cluster using the upstream collection: [k3s-ansible](https://github.com/k3s-io/k3s-ansible)

- The playbook will configure host-level requirements such as firewall rules,
  SELinux policies, and port forwarding as required k3s.
- The kubeconfig is written to the remote host at `~/.kube/config` for the
  configured `ansible_user`, and the local ansible execution user also receives a copy
  saved to `~/.kube/config.new`.

## Prerequisites

1. Update the inventory (variables and hosts info): [1-install-k3s-with-ansible/inventory.yaml](inventory.yaml)

   - Hosts must be members of `k3s_cluster` in either `server` or `agent`.

   | Variable | Value in inventory | Purpose |
   | --- | --- | --- |
   | `k3s_install_state` | `install` | Options: `install`, `upgrade`, or `remove`. |
   | `k3s_version` | `v1.36.4+k3s1` | K3s release to install / upgrade to. |
   | `firewalld_public_zone_ports_to_open` | `6443/tcp`, `443/tcp` | Open ports to public zone if using firewalld |
   | `server.hosts` | `172.16.0.20` | The K3s server node for this cluster. |
   | `agent.hosts` | empty (`{}`) | Single-node cluster, so no agent hosts are configured. |
   | `ansible_connection` | `ssh` | Connection method for Ansible hosts. |
   | `ansible_user` | `ansible` | SSH user used by Ansible, needs to have passwordless root access. |
   | `ansible_ssh_private_key_file` | `~/.ssh/ansible` | Private key used to authenticate to the hosts. |

2. (optional) - already installed in this repo, but if you want to update and reinstall the `k3s-ansible` collection:

```sh
ansible-galaxy collection install -r 1-install-k3s-with-ansible/requirements.yaml
```

## Run

Run the playbook:

```sh
ansible-playbook 1-install-k3s-with-ansible/playbook.yaml
```
