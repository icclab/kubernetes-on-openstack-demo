examples/guestbook
=========

The Guestbook example from the kubernetes repo: https://github.com/GoogleCloudPlatform/kubernetes/blob/master/examples/guestbook/README.md

##HowTo:
###Step 0 - setup Kubernetes cluster
see README.md
###Step 1 - create redis-master pod
```
$ kubecfg -c examples/guestbook/redis-master.json create pods
```
###Step 2 - create redis-master service
```
$ kubecfg -c examples/guestbook/redis-master-service.json create services
```
###Step 3 - create redis-slave replication controller
```
$ kubecfg -c examples/guestbook/redis-slave-controller.json create replicationControllers
```
###Step 4 - create redis-slave service
```
$ kubecfg -c examples/guestbook/redis-slave-service.json create services
```
###Step 5 - create frontend replication controller
```
$ kubecfg -c examples/guestbook/frontend-controller.json create replicationControllers
```
###Step 6 - create frontend service
```
$ kubecfg -c examples/guestbook/frontend-service.json create services
```
###Step 7 - verify
```
$ for kind in pods replicationControllers services; do kubecfg list ${kind}; done
```
Do note that the first time a pod gets scheduled to a minion, the docker images has to be pulled from the Docker Hub. This may take some time.

The Output should look similar to this:
```
ID                                     Image(s)                  Host                Labels                                                       Status
----------                             ----------                ----------          ----------                                                   ----------
3b8cb8cd-5852-11e4-b2d0-fa163edd4b96   michaelerne/php-redis     192.168.12.20/      name=frontend,replicationController=frontendController       Waiting
3b8cdd81-5852-11e4-b2d0-fa163edd4b96   michaelerne/php-redis     192.168.12.23/      name=frontend,replicationController=frontendController       Running
redis-master-2                         dockerfile/redis          192.168.12.22/      name=redis-master                                            Waiting
354dd321-5852-11e4-b2d0-fa163edd4b96   michaelerne/redis-slave   192.168.12.22/      name=redisslave,replicationController=redisSlaveController   Waiting
354e040c-5852-11e4-b2d0-fa163edd4b96   michaelerne/redis-slave   192.168.12.20/      name=redisslave,replicationController=redisSlaveController   Waiting
3b8c9754-5852-11e4-b2d0-fa163edd4b96   michaelerne/php-redis     192.168.12.22/      name=frontend,replicationController=frontendController       Waiting

ID                     Image(s)                  Selector            Replicas
----------             ----------                ----------          ----------
redisSlaveController   michaelerne/redis-slave   name=redisslave     2
frontendController     michaelerne/php-redis     name=frontend       3

ID                  Labels              Selector            Port
----------          ----------          ----------          ----------
redismaster         name=redis-master   name=redis-master   10000
redisslave          name=redisslave     name=redisslave     10001
frontend                                name=frontend       80
```
### Remove
```
$ kubecfg delete services/frontend
$ kubecfg delete services/redisslave
$ kubecfg delete services/redismaster
$ kubecfg stop frontendController
$ kubecfg rm frontendController
$ kubecfg stop redisSlaveController
$ kubecfg rm redisSlaveController
$ kubecfg delete pods/redis-master-2
```

Version
----

1.0



License
----

see LICENSE

