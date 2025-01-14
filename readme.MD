## Kubeadm kubernetes Ecosystem Automation with Ansible

This project automates the creation of a complete Kubernetes ecosystem using Kubeadm and Ansible. 
The process includes setting up a High Availability (HA) Kubeadm Kubernetes cluster with various essential services and tools for managing and deploying applications.

## Prerequisites

Virtual Machines: At least 4 virtual machines running Ubuntu 24.04.

Create your own inventory file guided by examples in inventory folder.

Ansible Vault Password: password.

Self-Signed Certificates: Generate wildcard self-signed certificates for a test domain using the provided script (ssl.sh).


## Features

- **Kubernetes HA Cluster**: Provisioned using Kubeadm and kube-vip for API load balancing.
- **MetalLB**: Provides load balancing for services.
- **Ingress-NGINX**: Manages HTTP and HTTPS traffic.
- **Persistent Storage**: Longhorn is used for storage solutions.

## Prerequisites

- At least 4 virtual machines running Ubuntu 24.04.
- Ansible Vault password: `password`.
- Optional - Self-Signed Certificates: Generate wildcard self-signed certificates for a test domain using the provided script (ssl.sh) located in folder create_self_signed_wildcard_cert.
- Resource Recommendations: Each VM should have at least 2 CPUs, 2GB RAM, and 20GB disk space.


## Quick start
1. Generate Wildcard Certificates

Run the provided script to create wildcard self-signed certificates for your desired domain:
```bash
  create_self_signed_wildcard_cert/ssl-wildcard.sh "your-desired-domain"
```

Import rootCA.crt into your browser.

Add domain.crt and domain.key into the Ansible vault located at group_vars/kubeadm/ssl-vault.yml

2. After that delete all the created certificate files inside this repo or back them up in some other folder, just don't leave them inside this repo!
```bash
    rm -rf cert.conf csr.conf rootCA.*
    rm -rf your-desired-domain.*
```

3. Create and activate a Python virtual environment:
```bash
    python3 -m venv venv-kubeadm
    source venv-kubeadm/bin/activate
    pip install -r requirements.txt
    ansible --version
```

4. Provision Nodes

Run the provision playbook to set up hostnames, timezones, and other system configurations on desired nodes:
 ```bash
    ansible-playbook -i inventory/inventory_vmware_workstation_local_lab.ini  preinstall.yml --tags provision -kK --ask-vault-pass
```

5. Provision Kubeadm Kubernetes Cluster automated using Ansible

Run the playbook to set up the Kubernetes HA cluster:
 ```bash
    ansible-playbook -i inventory/inventory_vmware_workstation_local_lab.ini  postinstall.yml --tags install_services_and_form_cluster --ask-vault-pass
```

For upgrade of the cluster use this playbook:
 ```bash
    ansible-playbook -i inventory/inventory_vmware_workstation_local_lab.ini  postinstall.yml --tags upgrade_control_plane_nodes --ask-vault-pass
    ansible-playbook -i inventory/inventory_vmware_workstation_local_lab.ini  postinstall.yml --tags upgrade_worker_nodes --ask-vault-pass
```

6. Add required tools like Helm to master nodes

 ```bash
    ansible-playbook -i inventory/inventory_vmware_workstation_local_lab.ini  postinstall.yml --tags install_tools --ask-vault-pass
```

7. Add MetalLB load balancing for services.  (update pool of addresses with your own in the group_vars/kubeadm/all.yml)

 ```bash
    ansible-playbook -i inventory/inventory_vmware_workstation_local_lab.ini  postinstall.yml --tags install_metallb --ask-vault-pass
```

8. Add Ingress-NGINX so that we can access our apps from outside of the cluster through metalLB provisioned LB IP
 ```bash
    ansible-playbook -i inventory/inventory_vmware_workstation_local_lab.ini  postinstall.yml --tags install_nginx_controller --ask-vault-pass
```

9. Add Longhorn for persistent storage
```bash
    ansible-playbook -i inventory/inventory_vmware_workstation_local_lab.ini  postinstall.yml --tags install_longhorn --ask-vault-pass
```

10. Add argoCD into cluster check group_vars/kubeadm/argovars.yml for which repo and branches does it track:
    More info about argoCD and how it operates here: [Link Text](#Additional info About ArgoCD).
```bash
    ansible-playbook -i inventory/inventory_vmware_workstation_local_lab.ini  postinstall.yml --tags install_argocd --ask-vault-pass
```

11. Add rancher:
```bash
    ansible-playbook -i inventory/inventory_vmware_workstation_local_lab.ini  postinstall.yml --tags install_rancher --ask-vault-pass
```

12. Add monitoring for whole setup:
```bash
    ansible-playbook -i inventory/inventory_vmware_workstation_local_lab.ini  postinstall.yml --tags install_k8s_prometheus_grafana_loki --ask-vault-pass
```

13. Clean Up - Once installation is complete remove venv.
```bash
   deactivate
   rm -rf venv-kubeadm
```

## Additional info About ArgoCD

## CI/CD Pipelines: 
Integrates with separate repositories for automated application deployment:

[CI/CD Without Jenkins](https://github.com/tinhutins/ci-cd-without-jenkins) - entire CI/CD process using a demo python application  using Github Actions.

[voting-app-lfs261](https://github.com/tinhutins/voting-app-lfs261) - entire CI/CD process for Example Voting App.

## Application Deployment:
Integrates with separate repositories, for deploying example applications, such as the Demo Python application and Example Voting App, with ArgoCD inside k8s:

[argo-k8s-python](https://github.com/tinhutins/argo-k8s-python) - Deploy Demo Python application using kustomize and ArgoCD in k8s.

[argocd-vote-deploy](https://github.com/tinhutins/argocd-vote-deploy) -  Deploy Example Voting App using kustomize and ArgoCD in k8s.
