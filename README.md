# Ansible Role: kubeadm
[![License](https://img.shields.io/badge/license-MIT%20License-brightgreen.svg)](https://opensource.org/licenses/MIT)

## Description

Deploy [Kubernetes](https://kubernetes.io/) , also known as K8s.

Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications.


## Requirements

- Ansible >= 2.16 (It might work on previous versions, but we cannot guarantee it)
- Debian 12 (bookworm) and python on deployer machine.

## Role Variables

All variables which can be overridden are stored in [defaults/main.yml](defaults/main.yml) file as well as in table below.

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| `kubeadm_tools.kubectl` | true | Install kubectl on master host (bool) |
| `kubeadm_tools.helm` | false | Install Helm on master host (bool) |
| `kubeadm_kube_settings.pod_cidr`| "10.244.0.0/16" | Specify range of IP addresses for the pod network. If set, the control plane will automatically allocate CIDRs for every node. (string) |
| `kubeadm_kube_settings.fqdn`| "" | Optional extra Subject Alternative Names (SANs) to use for the API Server serving certificate. Can be both IP addresses and DNS names. (string) |
| `kubeadm_kube_settings.endpoint`| "" | Specify a stable IP address or DNS name for the control plane. (string) |
| `kubeadm_cni.flunnel`| true | Install CNI Flunnel (bool) |
| `kubeadm_cni.calico`| false | Install CNI Calico (bool) |


## Example

### inventory
```
[bastions]
bastion ansible_host=158.160.97.73   ansible_user=infernofeniks

[kube_control_plane]
master1   ansible_host=10.1.1.25    ansible_user=infernofeniks

[kube_node]
worker1   ansible_host=10.1.1.34    ansible_user=infernofeniks

[ansible:children]
kube_control_plane
kube_node

[ansible:vars]
ansible_ssh_common_args='-o "StrictHostKeyChecking=false" -o "UserKnownHostsFile=/dev/null" -o ProxyCommand="ssh -W %h:%p infernofeniks@158.160.97.73"'
```

### Playbook

```yaml
---
- name: KUBERNETES | Install and Config
  hosts: all # or all,!bastions
  roles:
    - role: kubeadm
      vars:
        kubeadm_kube_settings:
          kubectl: true
          pod_cidr: "10.247.0.0/16"
          fqdn: kuber.infernofeniks.tech
          endpoint: ""
        # How CNI usage? (flunnel or calico)
        kubeadm_cni:
          flunnel: true
          calico: false
        # Add tools
        kubeadm_tools:
          helm: true
      tags: kubeadm
```

## License

This project is licensed under MIT License. See [LICENSE](/LICENSE) for more details.


## Author Information

[Kubernetes](https://kubernetes.io/) by [The Linux Foundationl](https://www.linuxfoundation.org/about).

Role by `InfernoFeniks`.