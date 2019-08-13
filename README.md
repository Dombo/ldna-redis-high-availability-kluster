#### Dependencies

* helm (inspired by stable/redis-ha)
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

a) The announce service pattern is used for intra-cluster networking but scaling the StatefulSet does not create a Service, only the Pod/s. This leads to the initialisation failing as the container cannot resolve it's own address within the cluster.

The most immediate "workaround" is to issue updates (scale events) via helm which will in turn use it's templating engine and ensure you have the Service necessary for initialisation to succeed. This is pretty lackluster though, as that creates problems for some of the value propositions kubernetes offers like autoscaling.

If you modified the template to have a "max-replicas" value then you'd have another piece of the infra to worry about it's capacity limit, seems a bit counter intuitive to me.

I'm sure I could modify the approach to address this, but time.

b) Security of this implementation is pretty poor.