# Various playbooks for maintaining my RPis

- General
  1. playbook-update-pis.yaml : update the distribution (apt dist-upgrade etc)
  1. shutdown-all.yaml : shutdown hosts
- [microk8s](https://microk8s.io/docs)
  1. install-microk8s.yaml : install microk8s and set up the cluster; enable the basics
  1. remove-microk8s.yaml : remove microk8s
  1. enable-microk8s-services.yaml : enable services on a microk8s installation (although multus is not available on 11/09/2021)
- [vanilla kubernetes](https://kubernetes.io/)
  1. reset-k8.yaml : Reset the kubernetes installation
  1. remove-k8-standard.yaml : Remove the installation (from ubuntu)


# Run a playbook against all of the rpis

```
ansible-playbook <playbook> -i rpis
```
