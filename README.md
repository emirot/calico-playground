# calico-playground
Playing with Calico

## Environment

I decided to use GKE, version 1.16.8-gke.8 because I can get a kubernete cluster after one click.

## Install

- https://docs.projectcalico.org/maintenance/kubernetes-upgrade#upgrading-if-you-have-application-layer-policy-enabled
- `export CALICO_DATASTORE_TYPE=kubernetes`
- `export KUBECONFIG=~/.kube/config`

**Check install**
`calicoctl version`

```
Client Version:    v3.12.0
Git commit:        84a21b3b
Cluster Version:   v3.12.0
Cluster Type:      k8s,kdd,bgp
```

```
calicoctl get node -o wide
$ gke-cluster-1-default-pool-6ca3b7a6-nthv
$ ...
```

Then before applying calico rules, check if network policies are working: https://github.com/ahmetb/kubernetes-network-policy-recipes/blob/master/01-deny-all-traffic-to-an-application.md


## Playground

### 3 apps:

- nginx1 
- nginx2 
- debian1

Deploy nginx 1,2 + debian1:
```
kubeclt apply -f resources/
```


### No Rule applied

- No rule applied, everything can talk to each other.

**_Examples_**:

Nginx1 to Nginx2:

```
kubectl exec -it $(kubectl get pod -o name  | grep nginx-deployment-1) bash
$apt-get update && apt-get install -y curl
$curl my-service-2.default.svc.cluster.local
# Get html response form nginx
```


### Only nginx rule applied

```
calicoctl apply -f calico-playground/network_policies/nginx-2-allow-ingress.yaml
```

```
root@nginx-deployment-1-b676f78df-66jqt:/# curl my-service-2.default.svc.cluster.local
```
Only pod 1 can talk to my-service-2, debian1 can not.

```
curl my-service-2.default.svc.cluster.local -> timeout
```

Debian1 can talk to ` wget my-service-1.default.svc.cluster.local`


#### Global network rule only

Let's apply: deny-all-global-network-policy.yaml

```
calicoctl apply -f deny-all-global-network-policy.yaml
```

##### Pods in default namespace

No ingress traffic, egress traffic is impacted because I can't access kube-dns to resolve dns.

##### Pods in kube namespace

No ingress traffic, egress traffic is impacted because I can't access kube-dns to resolve dns.



### Application layer policy

-  https://docs.projectcalico.org/v3.9/getting-started/kubernetes/installation/app-layer-policy

### References

- https://cloudblogs.microsoft.com/opensource/2019/10/17/tutorial-calico-network-policies-with-azure-kubernetes-service/
- https://github.com/ahmetb/kubernetes-network-policy-recipes/blob/master/03-deny-all-non-whitelisted-traffic-in-the-namespace.md
