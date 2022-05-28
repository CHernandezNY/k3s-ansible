# Build a Kubernetes cluster using k3s via Ansible

## K3s Ansible Playbook

Build a Kubernetes cluster using Ansible with k3s. The goal is easily install a Kubernetes cluster on machines running:

- [ ] Debian
- [X] Ubuntu
- [X] Raspbian
- [ ] CentOS

on processor architecture:

- [X] x64
- [X] arm64
- [X] armhf

## System requirements

Deployment environment must have Ansible 2.4.0+
controllers and workers must have passwordless SSH access

## Usage

First create a new directory based on the `sample` directory within the `inventory` directory:

```bash
cp -R inventory/sample inventory/
```

Second, edit `inventory/hosts.ini` to match the system information gathered above. For example:

```bash
[controller]
192.168.1.10

[worker]
192.168.1.11
192.168.1.12

[balancer]
192.168.1.13

[k3s_cluster:children]
controller
worker
```

If multiple hosts are in the controller group, the playbook will automatically set up k3s in [HA mode with etcd](https://rancher.com/docs/k3s/latest/en/installation/ha-embedded/).

This cluster was designed to use an external NGINX load balancer for the Control Plane API

This requires at least k3s version `1.19.1` however the version is configurable by using the `k3s_version` variable.

If needed, you can also edit `inventory/group_vars/all.yml` to match your environment.

### Create Cluster

Start provisioning of the cluster using the following command:

```bash
ansible-playbook site.yml -i inventory/hosts.ini
```

After deployment control plane will be accessible via virtual ip-address which is defined in inventory/group_vars/all.yml as `apiserver_endpoint`

### Remove k3s cluster

```bash
ansible-playbook reset.yml -i inventory/hosts.ini
```

>You should also reboot these nodes 

## Kube Config

To get access to your **Kubernetes** cluster just

```bash
scp debian@controller_ip:~/.kube/config ~/.kube/config
```

## Thanks  

I need to echo: This repo is really standing on the shoulders of giants.  To all those who have contributed.

Thanks to these repos for code and ideas:

* [k3s-io/k3s-ansible](https://github.com/k3s-io/k3s-ansible)
* [techno-tim/k3s-ansible](https://github.com/techno-tim/k3s-ansible)
* [geerlingguy/turing-pi-cluster](https://github.com/geerlingguy/turing-pi-cluster)
* [212850a/k3s-ansible](https://github.com/212850a/k3s-ansible) 
