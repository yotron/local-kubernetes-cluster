#  [![yotron](logo-yotron.png)](http://www.yotron.de)

[YOTRON](http://www.yotron.de) is a consultancy company which is focused on DevOps, Cloudmanagement and 
Data Management with NOSQL and SQL-Databases. Visit us on [ www.yotron.de ](http://www.yotron.de)

# Local Kubernetes Cluster
With this project you can deploy a simple Kubernetes cluster with one master and two nodes in a base configuration. 
But you can deploy as much nodes you (or your host machine) wants.

The deployment based on Vagrant with Virtualbox and Ansible. 

The base setup contains only a base configuration for a cluster. 
Pods are only available for Calico and the Kubernetes Dashboard.

The base configuration is in file `ansible/kubernets/files/kubeconfig` defined. 
Feel free to change the configuration.

```
apiVersion: v1
kind: Config
clusters:
- name: local
  cluster:
    server: http://172.18.18.101:8080
users:
- name: kubelet
contexts:
- context:
    cluster: local
    user: kubelet
  name: kubelet-context
current-context: kubelet-context
```

You have the full control of all of the versions you want to use:
- SetUp is independent from a Package Management like yum, apt-get
- Save for restart of complete Kubernetes

The installation process contains 
- etcd service as a clustered key/value store (https://etcd.io/)
- docker-service (https://www.docker.com/)
- kubernetes services (https://kubernetes.io/)
  - kubelet
  - kube-apiserver
  - kube-controller-manager
  - kube-proxy
  - kube-scheduler
  
Additional Controller which will be installed are 
- Calico for the network and policy controlling (https://www.projectcalico.org/)
- [Kubernetes Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

### Requirements 
- Ansible Version > 2.0 (https://www.ansible.com/), tested with Version 5.2.34
- Oracle Virtual Box (https://www.virtualbox.org/), tested with Version 5.2.34

### Startup
To start the Cluster an install all necessary components:

`whoiami@mymachine:~$ vagrant up`

The following node will be created:

- k8s-master

- k8s-node-01

- k8s-node-02

The start process begins with the Nodes and afterwards the master will be created.

Please be aware that ist tooks some time after the start of the vagrant host to get to the Kubernetes Dashboard. 

Here are somme calls for checking the proper setup. 

### Checks

Please check the startup of the nodes and pods.

Login to vagrant:

`whoami@mymachine:~$ vagrant ssh k8s-master`

```
vagrant@k8s-master:~$ kubectl get nodes
NAME            STATUS                     ROLES    AGE     VERSION
172.18.18.101   Ready,SchedulingDisabled   <none>   6m17s   v1.17.0
172.18.18.102   Ready                      <none>   6m16s   v1.17.0
172.18.18.103   Ready                      <none>   6m19s   v1.17.0
```
```
vagrant@k8s-master:~$ kubectl get pods --all-namespaces
NAMESPACE              NAME                                         READY   STATUS    RESTARTS   AGE
kube-system            calico-kube-controllers-648f4868b8-62tcc     1/1     Running   0          3m33s
kube-system            calico-node-clncb                            1/1     Running   0          3m33s
kube-system            calico-node-z4m26                            1/1     Running   0          3m33s
kube-system            calico-node-z4pnn                            1/1     Running   0          3m33s
kubernetes-dashboard   dashboard-metrics-scraper-76585494d8-lbvmq   1/1     Running   0          3m33s
kubernetes-dashboard   kubernetes-dashboard-5996555fd8-l6hbj        1/1     Running   0          3m33s
```

Dashboard URL

`http://172.18.18.101:8080/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login`

### own credentials
created by Joern Kleinbub, YOTRON, 02.01.2020

info@yotron.de, www.yotron.de