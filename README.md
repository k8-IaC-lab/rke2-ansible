# rke2-ansible (Custom Fork)

This repository is a lightly modified fork of [rancherfederal/rke2-ansible](https://github.com/rancherfederal/rke2-ansible).  
Key changes in this fork include:

- **Post-provision support** for bootstrapping a specific cluster:  
  See [`k8-IaC-lab/argocd-vault`](https://github.com/k8-IaC-lab/argocd-vault).
- **Basic `bashrc` updates** for cluster nodes.
- **High Availability (HA) focus**:  
  HA is configured using a load balancer, handled via `tls_san` and `server` variables.

---

## Bootstrap Instructions

To fully bootstrap your RKE2 cluster:

### 1. SSH Access

Ensure you have SSH access to **all** nodes you plan to provision as part of the cluster.

---

### 2. Create an Ansible Hosts Inventory

Create a `hosts` file (see `hosts.example.yaml` for reference) similar to this:

```yaml
rke2_cluster:
  children:
    rke2_servers:
      hosts:
        test-control-1:
          ansible_host: xxx.xxx.x.xx
          host_rke2_config:
        test-control-2:
          ansible_host: xxx.xxx.x.xx
          host_rke2_config:
            node-label:
              - "role=controlplane"
        test-control-3:
          ansible_host: xxx.xxx.x.xx
          host_rke2_config:
            node-label:
              - "role=controlplane"
    rke2_agents:
      hosts:
        test-agent-1:
          ansible_host: xxx.xxx.x.xx
          host_rke2_config:
            node-label:
              - "role=agent"
```
Replace the hostnames and IP addresses with those of your actual nodes.

---

### 3. Run the Ansible Playbook

Run the playbook using your HA entrypoint (adjusting values as needed):

```bash
ansible-playbook -i hosts.yml site.yml \
  -e tls_san=test.test.com \
  -e server=test.test.com
```

- `tls_san` should match the DNS name of your load balancer.
- `server` should also point to your load balancer's address.

---

### 4. Post-provision Steps

Once provisioning completes, follow the post-installation instructions in:  
[https://github.com/k8-IaC-lab/argocd-vault](https://github.com/k8-IaC-lab/argocd-vault)
