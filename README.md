# Ansible role: KubeRustFS

Deploys RustFS in a Kubernetes cluster using its Helm chart and installs the
RustFS `rc` command-line client on the managed host.

## Requirements

- Ansible Core 2.11 or newer.
- `helm` and `kubectl` available on the managed host.
- A working Kubernetes configuration file on the managed host.
- Permissions to create namespaces and deploy Helm releases in the cluster.
- Privileges to write the `rc` binary to `/usr/local/bin` (normally `become`).

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `rustfs_accesskey` | `rustfs` | Access key for object storage and web access. Override it in production. |
| `rustfs_secretkey` | `rustfs123` | Secret key for object storage and web access. Store it with Ansible Vault and override it in production. |
| `master_deploy` | `false` | Allow the workload to run on a Kubernetes control-plane node. |
| `kubeconfig_path` | `/etc/kubernetes/admin.conf` | Kubernetes configuration file used by Helm and kubectl. |
| `enable_ingress` | `false` | Enable the chart ingress. |
| `enable_httproute` | `false` | Enable a Gateway API HTTPRoute. |
| `gateway.class` | `traefik` | Gateway class used by the chart. |
| `gateway.name` | `traefik-gateway` | Name of the existing Gateway. |
| `gateway.namespace` | `traefik` | Namespace of the existing Gateway. |
| `public_hostname` | `rustfs.local` | Public hostname used by ingress or HTTPRoute. |
| `rustfs_cli_version` | `0.1.32` | Version of the RustFS CLI to install. |

## Example playbook

```yaml
---
- name: Deploy RustFS
  hosts: kubernetes_control_plane
  become: true
  roles:
    - role: ansible-role-kuberustfs
      vars:
        rustfs_accesskey: "{{ vault_rustfs_accesskey }}"
        rustfs_secretkey: "{{ vault_rustfs_secretkey }}"
        public_hostname: rustfs.example.com
        enable_ingress: true
```

Keep credentials in Ansible Vault or another secret store; do not commit them
to the inventory in plain text.

## License

Apache-2.0
