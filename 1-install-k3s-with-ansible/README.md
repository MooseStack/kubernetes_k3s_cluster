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

2. Update the inventory (variables and hosts info): [1-install-k3s-with-ansible/inventory.yaml](inventory.yaml)

   Run `playbooks/helm.yaml` first to install the pinned Helm CLI on the target
   server before running the Argo CD playbook.

   - Hosts must be members of `k3s_cluster` in either `server` or `agent`.

   | Variable | Value in inventory | Purpose |
   | --- | --- | --- |
   | `k3s_install_state` | `install` | Options: `install`, `upgrade`, or `remove`. |
   | `k3s_version` | `v1.36.4+k3s1` | K3s release to install / upgrade to. |
   | `argocd_install_state` | `install` | Argo CD Helm action: `install` or `remove`. Will also install helm. Leave blank otherwise.|
   | `argo_bootstrap` | `false` | Apply the manifests in `2-argo_bootstrap` after the Argo CD installation. |
   | `argocd_chart_version` | `10.9.1` | Argo CD Helm chart version to install or upgrade to. |
   | `helm_version` | `v3.17.3` | Helm CLI version to install on the cluster server before the Argo CD playbook runs. |
   | `controller_kubeconfig` | `/etc/rancher/k3s/k3s.yaml` | Target Kubeconfig used to install ArgoCD |
   | `firewalld_public_zone_ports_to_open` | `6443/tcp`, `443/tcp` | Open ports to public zone if using firewalld |
   | `server.hosts` | `172.16.0.20` | The K3s server node for this cluster. |
   | `agent.hosts` | empty (`{}`) | Single-node cluster, so no agent hosts are configured. |
   | `ansible_connection` | `ssh` | Connection method for Ansible hosts. |
   | `ansible_user` | `ansible` | SSH user used by Ansible, needs to have passwordless root access. |
   | `ansible_ssh_private_key_file` | `~/.ssh/ansible` | Private key used to authenticate to the hosts. |

3. (optional) - already installed in this repo, but if you want to update and reinstall the `k3s-ansible` collection:

```sh
cd 1-install-k3s-with-ansible
ansible-galaxy collection install -r requirements.yaml
```

## Run

Run the playbook from this directory so Ansible automatically picks up the local `ansible.cfg`:

## Install k3s, helm, and argocd:
```sh
cd 1-install-k3s-with-ansible
ansible-playbook playbooks/main.yaml
```

## Or individually:
Install the Helm CLI:

```sh
ansible-playbook playbooks/helm.yaml
```

Install ArgoCD. Theres a check I put that will ensure it doesnt reinstall if its already installed. To avoid GitOps overwrite in future Ansible reruns.

```sh
ansible-playbook playbooks/argocd.yaml
```


Apply the Argo CD bootstrap manifests, this enabled the GitOps approach to maintain ArgoCD upgrades and all future Kubernetes manifests via the [gitops](../gitops) folder:

```sh
ansible-playbook playbooks/argo-bootstrap.yaml -e argo_bootstrap=true
```
