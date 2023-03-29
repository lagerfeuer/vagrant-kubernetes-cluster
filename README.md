# Kubernetes cluster on Vagrant

## Prerequisites
1. A few tools are needed, make sure they are installed:
  - vagrant
  - ansible

2. Make sure to add the following block to `/etc/vbox/networks.conf`, otherwise Vagrant will run into an error. We need this because our nodes are in the `10.0.0.0/16` network and our pods are using the `192.168.0.0/16` network.
```
* 10.0.0.0/8 192.168.0.0/16
* 2001::/64
```

3. Install required collections from Ansible Galaxy:
```shell
ansible-galaxy collection install -r requirements.yaml
```

## Let's go

To bring up the cluster, run
```shell
vagrant up --provision
```

Cleanup after you're done:
```shell
vagrant destroy -f
```

### kubectl
Since `kubectl` needs to be configured to connect to our cluster, we need the appropriate config file on our local machine.
During the setup of the master, this config is copied to the current working directory.
```shell
export KUBECONFIG=kubeconfig
kubectl config view
kubectl get nodes -o wide
```

Now we can finally deploy something. If you don't know what, then take a look at the [socks-shop]().
```shell
kubectl create namespace sock-shop
kubectl apply -f https://raw.githubusercontent.com/microservices-demo/microservices-demo/master/deploy/kubernetes/complete-demo.yaml
```

### ssh
You can connect to a machine by running `vagrant ssh master|worker-1|worker-2|worker-3`.
There is one master and N worker nodes, by default we create 3 workers.
You can change this variable in the `Vagrantfile`.



## TODO
- [ ] Add [dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 
- [ ] Add ingress controller (e.g., [nginx](https://kubernetes.github.io/ingress-nginx/deploy/) in `roles/kubernetes/tasks/addons/ingress.yml`)
- [ ] Allow different OS
  - [ ] Rocky Linux
  - [ ] OpenSUSE
- [ ] Ansible Lint
