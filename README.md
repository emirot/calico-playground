# calico-playground
Playing with Calico

### Install

- https://docs.projectcalico.org/maintenance/kubernetes-upgrade#upgrading-if-you-have-application-layer-policy-enabled

`export CALICO_DATASTORE_TYPE=kubernetes`
`export KUBECONFIG=~/.kube/config`

Calicoctl version
```
Client Version:    v3.12.0
Git commit:        84a21b3b
Cluster Version:   v3.12.0
Cluster Type:      k8s,kdd,bgp
```

### Playground

3 apps:

nginx1, nginx2, 

#### No Rule applied
- No rule applied, everything can talk to each other

#### Only nginx rule applied

```
root@nginx-deployment-1-b676f78df-66jqt:/# curl my-service-2.default.svc.cluster.local
```
Only pod 1 can talk to my-service-2, busybox can not.

```
kubectl run -i --tty --rm debug --image=busybox --restart=Never -- sh
```

Busybox can talk to ` wget my-service-1.default.svc.cluster.local`


#### nginx + global rule 


Only pod 1 can talk to my-service-2, busybox can not.
Other communication timeouts.

