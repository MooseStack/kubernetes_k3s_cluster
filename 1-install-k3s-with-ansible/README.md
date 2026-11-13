# Kubernetes (K3s)

This directory contains a local Ansible setup for installing, upgrading, or
removing a K3s cluster using the upstream collection: [k3s-ansible](https://github.com/k3s-io/k3s-ansible)

- The upstream k3s-ansible playbook will configure host-level requirements such as firewall rules,
  SELinux policies, and port forwarding as required k3s.
- The kubeconfig is written to the remote host at `~/.kube/config` for the
  configured `ansible_user`, and the local ansible execution user also receives a copy
  saved to `~/.kube/config.new`.
- Additionally, I created my own playbooks to work with firewalld, install ArgoCD and helm, and configure/bootstrap Argo for GitOps

## Prerequisites
 
1. Ensure you have ansible installed: `pip3 install --user ansible` with passwordless ssh to your targets.

2. Set the server and agent hosts in [inventory.yaml](inventory.yaml), then set
   the default cluster options in [vars/k3s_cluster.yaml](vars/k3s_cluster.yaml).
   [ansible.cfg](ansible.cfg) loads both files as inventory sources. The
   variables apply to all server and agent hosts.

   The main playbook installs, upgrades, or removes K3s according to the state
   variables below. It runs the local cluster role only when installing K3s.

   - Hosts must be members of `k3s_cluster` in either `server` or `agent`.

   | Variable | Current value | Purpose |
   | --- | --- | --- |
   | `k3s_install_state` | `install` | Selects `install`, `upgrade`, or `remove`. Only `install` also configures OS packages, firewall, Helm, and Argo CD. |
   | `k3s_version` | `v1.36.4+k3s1` | Default K3s install version. The upgrade variables file overrides it. |
   | `argocd_install_state` | `install` | Argo CD Helm action: `install` or `remove`. Will also install helm. Leave blank otherwise.|
   | `argo_bootstrap` | `true` | Apply the manifests in `2-argo_bootstrap` after the Argo CD installation. |
   | `argocd_chart_version` | `10.9.1` | Argo CD Helm chart version to install or upgrade to. |
   | `helm_version` | `v3.22.0` | Helm CLI version to install on the cluster server before the Argo CD playbook runs. |
   | `controller_kubeconfig` | `/etc/rancher/k3s/k3s.yaml` | Target Kubeconfig used to install ArgoCD |
   | `os_packages` | `nfs-utils` | List of operating system packages to install on all cluster hosts. Leave empty or omit to skip package installation. |
   | `firewalld_public_zone_ports_to_open` | `6443/tcp`, `443/tcp` | Open ports to public zone if using firewalld |
   | `server.hosts` | `172.16.0.20` | The K3s server node for this cluster. |
   | `agent.hosts` | empty (`{}`) | Single-node cluster, so no agent hosts are configured. |
   | `ansible_connection` | `ssh` | Connection method for Ansible hosts. |
   | `ansible_user` | `ansible` | SSH user used by Ansible, needs to have passwordless root access. |
   | `ansible_ssh_private_key_file` | `~/.ssh/ansible` | Private key used to authenticate to the hosts. |

3. Install or update the Ansible collections

The [requirements.yaml](requirements.yaml) file pins the `k3s-ansible`
collection and the other collections used by this project. To install/update them
into the local `collections/` directory for the first time, run:

To update `k3s-ansible`, change its `version: x` entry in
[requirements.yaml](requirements.yaml) to the release tag you want, then run:

```sh
cd 1-install-k3s-with-ansible
ansible-galaxy collection install -r requirements.yaml --force-with-deps
ansible-galaxy collection list k3s.orchestration
```

The collection version selects the Ansible playbook code. The K3s release
installed on your hosts is set separately by `k3s_version` in
[vars/k3s_cluster.yaml](vars/k3s_cluster.yaml) or
[vars/upgrade.yaml](vars/upgrade.yaml).

## Install / Upgrade

Run playbooks from this directory so Ansible picks up the local `ansible.cfg`
and both inventory sources:

### Install K3s, Helm, and Argo CD

```sh
cd 1-install-k3s-with-ansible
ansible-playbook playbooks/main.yaml
```

The local tasks are implemented by the `role/k3s_cluster` role and run only
when `k3s_install_state` is `install`, after the upstream K3s install playbook.

### Upgrade K3s only

Set the target `k3s_version` in [vars/upgrade.yaml](vars/upgrade.yaml), then run:

```sh
cd 1-install-k3s-with-ansible
ansible-playbook playbooks/main.yaml -e @vars/upgrade.yaml
```

The upstream upgrade playbook runs on servers one at a time, then on agents.
The local package, firewall, Helm, Argo CD, and bootstrap tasks are skipped.
The upgrade file overrides the default install state and version without
editing the shared cluster variables. Ansible still loads the shared SSH and
API endpoint settings from `vars/k3s_cluster.yaml`.
For `k3s_install_state: remove`, the main playbook runs the upstream K3s reset
without the local cluster role.
