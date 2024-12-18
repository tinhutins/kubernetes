## Kubeadm kubernetes Ecosystem Automation with Ansible

This project automates the creation of a complete Kubernetes ecosystem using Ansible. 
The process includes setting up a Kubeadm Kubernetes High Availability (HA) cluster with various essential services and tools for managing and deploying applications.

## Prerequisites

Virtual Machines: At least 4 virtual machines running Ubuntu 24.04.

Ansible Vault Password: The vault password for Ansible is password.

Self-Signed Certificates: Generate wildcard self-signed certificates for a test domain using the provided script (ssl.sh).

## Features

Kubernetes HA Cluster: Provisioned with kubeadm and kube-vip for API load balancing.

MetalLB: Provides load balancing for services.

Ingress-NGINX: Manages HTTP and HTTPS traffic.

Storage: Longhorn is used for persistent storage.

Tools:

Helm

ArgoCD

Rancher

## CI/CD Pipelines: 
Integrates with separate repositories for automated application deployment:

[CI/CD Without Jenkins](https://github.com/tinhutins/ci-cd-without-jenkins) - entire CI/CD process using a demo python application  using Github Actions.

[voting-app-lfs261](https://github.com/tinhutins/voting-app-lfs261) - entire CI/CD process for Example Voting App.

## Application Deployment:
Integrates with separate repositories, for deploying example applications, such as the Demo Python application and Example Voting App, with ArgoCD inside k8s:

[argo-k8s-python](https://github.com/tinhutins/argo-k8s-python) - Deploy Demo Python application using kustomize and ArgoCD in k8s.

[argocd-vote-deploy](https://github.com/tinhutins/argocd-vote-deploy) -  Deploy Example Voting App using kustomize and ArgoCD in k8s.


## Quick start
1. Generate Wildcard Certificates

Run the provided script to create wildcard self-signed certificates for your desired domain:
```bash
  create_self_signed_wildcard_cert/ssl-wildcard.sh "your-desired-domain"
```

Import rootCA.crt into your browser.

Add domain.crt and domain.key into the Ansible vault located at group_vars/kubeadm/ssl-vault.yml

2. Create and activate a Python virtual environment:
```bash
    python3 -m venv venv-kubeadm
    source venv-kubeadm/bin/activate
    pip install -r requirements.txt
    ansible --version
```

3. Provision Nodes

Run the playbook to set up hostname, timezone etc:
 ```bash
    ansible-playbook -i inventory/inventory_vmware_workstation_local_lab.ini --tags provision -kK --ask-vault-pass
```

4. Provision Kubernetes Cluster

Run the playbook to set up the Kubernetes HA cluster:
 ```bash
    ansible-playbook -i inventory/inventory_vmware_workstation_local_lab.ini --tags install_services_and_form_cluster -kK --ask-vault-pass
```
