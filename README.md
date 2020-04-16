# calico-playground
Playing with Calico

### Install

- https://docs.projectcalico.org/maintenance/kubernetes-upgrade#upgrading-if-you-have-application-layer-policy-enabled
- `export CALICO_DATASTORE_TYPE=kubernetes`
- `export KUBECONFIG=~/.kube/config`

Calicoctl version
```
Client Version:    v3.12.0
Git commit:        84a21b3b
Cluster Version:   v3.12.0
Cluster Type:      k8s,kdd,bgp
```

### Playground

#### 3 apps:

- nginx1 
- nginx2 
- busybox

Deploy nginx 1,2 + busybox:
```
kubeclt apply -f resources/
```


#### No Rule applied

- No rule applied, everything can talk to each other.

**_Examples_**:

Nginx1 to Nginx2:

```
kubectl exec -it $(kubectl get pod -o name  | grep nginx-deployment-1) bash
$apt-get update && apt-get install -y curl
$curl my-service-2.default.svc.cluster.local
# Get html response form nginx
```


#### Only nginx rule applied

```
root@nginx-deployment-1-b676f78df-66jqt:/# curl my-service-2.default.svc.cluster.local
```
Only pod 1 can talk to my-service-2, busybox can not.

```
kubectl run -i --tty --rm debug --image=busybox --restart=Never -- sh
```

Busybox can talk to ` wget my-service-1.default.svc.cluster.local`


#### Global network rule only

##### Pods in default namespace

##### Pods in kube namespace


#### nginx + global rule 


Only pod 1 can talk to my-service-2, busybox can not.
Other communication timeouts.

#### Application layer policy

-  https://docs.projectcalico.org/v3.9/getting-started/kubernetes/installation/app-layer-policy

#### References

- https://cloudblogs.microsoft.com/opensource/2019/10/17/tutorial-calico-network-policies-with-azure-kubernetes-service/
- https://github.com/ahmetb/kubernetes-network-policy-recipes/blob/master/03-deny-all-non-whitelisted-traffic-in-the-namespace.md
