examples/nginx
=========

A simple example of a replication controller and a service to cerate a load balanced nginx.

##HowTo:
###Step 0 - setup Kubernetes cluster
see README.md 
###Step 1 - create replication controller
```
$ kubecfg -c examples/nginx/nginx-controller.json create replicationControllers
```
###Step 2 - create service
```
$ kubecfg -c examples/nginx/nginx-service.json create services
```
###Step 3 - verify
```
$ for kind in pods replicationControllers services; do kubecfg list ${kind}; done
```
Do note that the first time a pod gets scheduled to a minion, the docker images has to be pulled from the Docker Hub. This may take some time.

The Output should look similar to this:
```
ID                                     Image(s)            Host                Labels                                             Status
----------                             ----------          ----------          ----------                                         ----------
680c2cca-5609-11e4-9743-fa163ee0b804   dockerfile/nginx    192.168.12.23/      name=nginx,replicationController=nginxController   Running
680cc301-5609-11e4-9743-fa163ee0b804   dockerfile/nginx    192.168.12.22/      name=nginx,replicationController=nginxController   Running
680e10a0-5609-11e4-9743-fa163ee0b804   dockerfile/nginx    192.168.12.20/      name=nginx,replicationController=nginxController   Running

ID                  Image(s)            Selector            Replicas
----------          ----------          ----------          ----------
nginxController     dockerfile/nginx    name=nginx          3

ID                  Labels              Selector            Port
----------          ----------          ----------          ----------
nginx                                   name=nginx          80

```
###Step 4 - play with it

Now nginx should be served on each host on port ```80``` and port ```8000```.

Using port ```80```, the kubernetes proxy will route the request to any pod with the label ```nginx```. 
Using port ```8000```, you directly connect to the pod running on this machine.

###remove it

```
$ kubecfg stop nginxController
$ kubecfg rm nginxController
$ kubecfg delete services/nginx
```

Version
----

1.0



License
----

see LICENSE



