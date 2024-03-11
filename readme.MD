vault password for ansible is: password

this ansible automatization creates whole ecosystem around k8s.

first manually create wildcard self-signed cert for some test domain using provided script.

just run ./ssl.sh "desired domain name" - import rootCA.crt into browser and domain.crt(WITHOUT rootCA.crt) and domain.key into ssl-vault.

rest is done with ansible - create kubeadm HA cluster with kube-vip as LB for k8s api, metallb, nginx-controller, tools like helm, argocd, rancher, longhorn for storage.

then in separate repo https://github.com/tinhutins/argo-k8s-python.git  setup automatic app deployment into this k8s using argo.

push into that repo is being done with GitlabCI pipeline sitting inside this repo https://github.com/tinhutins/ci-cd-without-jenkins where actual app is created, and whole ci/cd process.
