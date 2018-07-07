# Kubernetes on Digital Ocean via Ansible

## Provides

Deploy kubernetes on a 3 node digital-ocean cluster using Ansible.

These manifests are based on the following guide:

https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-1-10-cluster-using-kubeadm-on-ubuntu-16-04

## Pre-requisites

- Create 3 droplets on digital-ocean (ubuntu / 1gig ram / any region)
- Create an ssh-keypair. By default, `initial.yml` assumes the public key on your local machine is stored at `~/.ssh/id_rsa.pub` 
- Install ansible (`ansible-playbook`) on your local machine

### Configure hosts

Update the `hosts` file and change the following with the external IPs of your new droplets

- `<master-ip>`
- `<worker-ip1>`
- `<worker-ip2>`

### Run playbooks

The ordering is important!

Wait for each stage to complete before progressing to the next.


```
ansible-playbook -i hosts initial.yml
```

```
ansible-playbook -i hosts kube-deps.yml
```

```
ansible-playbook -i hosts master.yml
```

```
ansible-playbook -i hosts  workers.yml
```

## Validate cluster

```
ssh ubuntu@<master-ip>
```

```
ububuntu@master:~$ kubectl get nodes
NAME       STATUS    ROLES     AGE       VERSION
master     Ready     master    15m       v1.11.0
worker01   Ready     <none>    12m       v1.11.0
worker02   Ready     <none>    12m       v1.11.0
```

# Gotchas / concerns

- Ansible will configure hosts over ssh. Be sure to answer yes/no when it asks you to add the host to the list of known-hosts (i.e. don't skip it)
- Not sure how secure this installation of the cluster is, so assume zero security
