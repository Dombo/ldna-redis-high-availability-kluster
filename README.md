#### Dependencies

* helm (inspired by redis-ha)
* minikube
* kubectl

#### Pre-requisites

```bash
kubectl create ns rhak
```

#### Deploying

```bash
helm install ./src --namespace rhak --set replicas=3
```

#### RW into the cluster

```bash
# Find the master, it will be 0 unless it's been killed, that's the guarantee of StatefulSets
kubectl exec --namespace=rhak redis-high-availability-kluster-server-${N} -ti -- /bin/sh
redis set food good
```

```bash
# Connected to any node
redis get food
```

#### Scaling

```bash
helm upgrade ${RELEASE_NAME} ./src --namespace rhak --set replicas=${N}
```

#### Teardown

```bash
helm delete ${RELEASE_NAME} --purge
```

If you want to remove the data you'll need to remove the Persistent Volumes

### TODO

* checklist of the optional extras included

### Ideas

* A bash or kubernetes ecosystem test harness asserting you can kill master or slaves and quorum will reassemble
* Continuous Integration of this solution

### Evaluation

##### Shortcomings