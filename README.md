# Various playbooks for maintaining my RPis

- General
  1. ``playbook-update-pis.yaml`` :
      * update the distribution; run apt dist-upgrade etc on hosts
  1. ``misc/shutdown.yaml`` :
      * shutdown hosts
  1. ``misc/reboot.yaml`` :
      * reboot hosts
- [microk8s](https://microk8s.io/docs)
  1. ``microk8s/microk8s.00.playbook-install-microk8s-with-metallb.yaml`` (calls out to other playbooks to do its work):
      * install microk8s and set up the cluster
      * enable various services
      * install metallb
  1. ``microk8s/microk8s.remove-microk8s.yaml`` :
      * remove microk8s
- [k3s](https://k3s.io/)
  1. ``k3s/k3s.00.playbook-install-k3s.yaml`` (calls out to other playbooks to do its work):
      * Install clustered k3s with HA Embedded DB on all hosts
      * Install [cilium](https://cilium.io/) as the CNI provider
      * Install [cilium hubble](https://docs.cilium.io/en/stable/intro/#what-is-hubble)
      * Install [kubernetes dashboard](https://github.com/kubernetes/dashboard)
      * Install [metallb](https://metallb.org/) and setup external service definitions
  1. ``k3s/k3s.remove-k3s.yaml`` :
      * Remove k3s from hosts
- [vanilla kubernetes](https://kubernetes.io/)
  1. ``k8/reset-k8.yaml`` :
      * Reset the kubernetes installation
  1. ``k8/remove-k8-standard.yaml`` :
      * Remove the installation (from ubuntu)
- kernel (see [README.md in the directory](kernel/README.md) for further info)
  1. ``kernel/kernel-dev-setup.yaml`` :
      * Set up (ubuntu) kernel development/build dependencies
  1. ``kernel/kernel.01.build-kernel-locally-with-updated-config.yaml`` :
      * Use to build the arm64 kernel locally, along with vxlan and btf requirements
  1. ``kernel/kernel.02.apply-kernel.yaml`` :
      * Apply the kernel built in the above playbook


# Run a playbook against all of the rpis

```
ansible-playbook <playbook> -i rpis.yaml
```
