icclab/kubernetes-on-openstack-demo
=========

This repo guides you through the process of setting up your own Kubernetes Cluster on your existing OpenStack Cloud using its Orchestration Service Heat. This Kubernetes Cluster should only be used as a Proof of Concept.

Big thanks @ kelseyhightower and brendanburns for their tutorials/examples!

###Technology involved:

* Kubernetes: https://github.com/GoogleCloudPlatform/kubernetes
* CoreOS: https://coreos.com/
* etcd: https://github.com/coreos/etcd
* fleet: https://github.com/coreos/fleet
* flannel: https://github.com/coreos/flannel
* kube-register: https://github.com/kelseyhightower/kube-register

###Architecture

The provisioned Cluster consists of 5 VMs. The first one, discovery, is a dedicated etcd host. This allows easy etcd discovery thanks to a static IP-Address.

A Kubernetes Master host is setup with the Kubernetes components apiserver, scheduler, kube-register, controller-manager as well as proxy. This machine also gets a floating IP assined and acts as a access point to your Kubernetes cluster.

Three Kubernetes Minion hosts are setup with the Kubernetes components kubelet and proxy.

##Start the Cluster

This Template was written for and tested on OpenStack Icehouse.

###Upload the CoreOS Image

Import the CoreOS Image from the alpha channel following these Instructions: 

https://coreos.com/docs/running-coreos/platforms/openstack/#import-the-image

###Clone the Git Repo:
```
$ git clone icclab/kubernetes-on-openstack-demo
```
### Parameters
To start the stack, you need a few parameters. These can be retrieved directly from OpenStack:


```
image_id: glance image-list (choose the CoreOS image you uploaded)
key-pair: nova keypair-list
public_net_id: neutron net-list (choose the external network)
```
###Start the template
Start the stack either using Horizon or the heat-cli:
```
$ heat stack-create -f <path/to/kubernetes.yaml> \
-P image_id=<image_id> \
-P key-pair=<key-pair> \
-P public_net_id=<public_net_id> \
kubernetes
```
You can check the progress using:
```
$ heat stack-show kubernetes
```
###Get Floating IP
After a few seconds the stack should be up and running. Retrieve its floating IP via Horizon or the heat-cli:

```
$ heat output-show kubernetes floating_ip
```

###(Optional) Get Kubecfg binary and setup ssh tunnel
If you would like to control your kubernetes cluster from your local machine, get the kubecfg binary for your OS:
####Linux
```
$ wget http://storage.googleapis.com/kubernetes/kubecfg -O /usr/local/bin/kubecfg
```
####Darwin
```
$ wget http://storage.googleapis.com/kubernetes/darwin/kubecfg -O /usr/local/bin/kubecfg
```
Now setup a SSH tunnel to your kubernetes cluster
```
$ ssh -f -nNT -L 8080:127.0.0.1:8080 core@${floating_ip}
```
##Test it
To verify that the Cluster is up, list all minions:
```
$ kubecfg list minions
```
It should show you 3 minions:
```
Minion identifier
----------
192.168.12.20
192.168.12.22
192.168.12.23
```
If it works, follow one of the examples provided.

Version
----

1.0



License
----
```
# Copyright 2014 Zuercher Hochschule fuer Angewandte Wissenschaften
# All Rights Reserved.
#
#    Licensed under the Apache License, Version 2.0 (the "License"); you may
#    not use this file except in compliance with the License. You may obtain
#    a copy of the License at
#
#         http://www.apache.org/licenses/LICENSE-2.0
#
#    Unless required by applicable law or agreed to in writing, software
#    distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
#    WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
#    License for the specific language governing permissions and limitations
#    under the License.
```
