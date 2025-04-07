# Lab 6 - Kubernetes
## Clusters

- creating clusters:
```bash
kind create cluster --config kind-config.yaml
```

- info about the current cluster:
```bash
kubectl cluster-info [dump]
```

- listing components from current cluster:
```bash
kubectl get all
```

- listing pods from current cluster:
```bash
kubectl get pods
```

- listing cluster nodes:
```bash
kubectl get nodes
```

- displaying details about a node:
```bash
kubectl describe nodes <node>

kubectl describe nodes kind-control-plane
```

- delete cluster:
```bash
kind delete cluster
```

## Pods
- creating pods (imperative) based on a Docker image - background mode:
```bash
kubectl run my-nginx --image=nginx
kubectl run alpine --image=alpine
```

- creating pods (imperative) based on a Docker image - interactive mode:
```bash
kubectl run -i --tty --rm alpine --image=alpine
```

- creating namespaces, in which pods can run:
```bash
kubectl create namespace <namespace-name>
```

- run a pod in a namespace:
```bash
kubectl run alpine --image=alpine -n <namespace-name>
```

- get pods from a namespace:
```bash
kubectl get pods -n <namespace>
```

- execute command in a runnig pod:
```bash
kubectl exec -it <podname> <command>
kubectl exec -it mypod ls # (mypod is already running)
```

- delete a pod (from a namespace):
```bash
kubectl delete pod <pod> -n <namespace>
```

### Example 1
```bash
kubectl create namespace my-first-namespace # creating namespace

# creating pods in this namespace
kubectl run my-nginx --image=nginx -n=my-first-namespace

kubectl run alpine --image=alpine -n=my-first-namespace

kubectl exec -it my-nginx -n=my-first-namesp
ace -- ls # executing ls in my-nginx pod

# delete pods
kubectl delete pod alpine -n=my-first-namespace

kubectl delete pod my-nginx -n=my-first-name
space

kubectl delete namespace my-first-namespace # delete namespace
```

- create a pod using a configuration file:
```bash
kubectl apply -f nginxpod.yaml
```

### Example 2
```bash
kubectl apply -f testapp-pod.yaml # creates testapp pod

kubectl port-forward testapp 8888:5000 # port forwarding to testapp pod

kubectl logs testapp # gets logs from testapp pod

kubectl exec testapp -- ls # executes ls command in testapp pod

kubectl cp kind-config.yaml testapp:./kind-config.yaml # copy kind-config.yaml file in testapp pod

kubectl delete pod testapp # deleted testapp pod
```

## Generating YAML files
- generate YAML configurations for pods:
```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml > my-nginx-pod.yaml
```

## Labels and selectors
```bash
# create pod and add app:myapp label
kubectl run nginx --image=nginx --dry-run=client -o yaml > my-nginx-pod.yaml

# see pod details
kubectl describe pod nginx

# get pods using label selector
kubectl get pods -l app=myapp

# launch ClusterIP service
kubectl apply -f my-service.yaml
```

## ReplicaSets
- creating a ReplicaSet:
```bash
kubectl apply -f my-nginx-rs.yaml
```

- list ReplicaSets:
```bash
kubectl get replicasets
```

- show details about a ReplicaSet:
```bash
kubectl describe rs <replica-set>
# OR
kubectl describe replicaset <replica-set>
```

- delete ReplicaSet
```bash
kubectl delete rs <replica-set>
# OR
kubectl delete replicaset <replica-set>
```

- scale ReplicaSet:
```bash
kubectl scale replicasets <replicaset> –replicas=4
```

### Example 3
```bash
kubectl apply -f testapp-rs.yaml 

kubectl get pods

kubectl describe rs testapp

kubectl scale rs testapp --replicas=4

kubectl delete rs testapp
```

## Deployments
- create deployment:
```bash
kubectl apply -f my-nginx-deployment.yaml
```

- list deployments:
```bash
kubectl get deployments
```

- create a new deployment:
```bash
kubectl apply -f lab3dep.yaml
```

- let's see the deployments, the ReplicaSets and the pods:
```bash
kubectl get pods

kubectl get rs

kubectl get deploy
```

- let's launch an updated deployment and then let's see the ReplicaSets and the pods:
```bash
kubectl apply -f mynewdep.yaml

kubectl get pods # new pods from a new ReplicaSet are created and then the old pods are killed

kubectl get rs # a new ReplicaSet is created - the old RS will have zero pods after the old pods are deleted (that's logical, right?)
# and it still exists as may go back to the previous deployment
```

### Example
```bash
kubectl scale deployment cc-dep-01 --replicas=2 # updating the number of replicas from deployment

kubectl get pods

kubectl get rs

kubectl scale deployment cc-dep-01 --replicas=5

kubectl get pods

kubectl get rs
```

- let's update the deployment with a wrong image, thus the deployment should fail:
```bash
kubectl set image deployment cc-dep-01 nginx=nginx:failTest
```

- we'll see the pods failed to be created due to invalid image:
```bash
kubectl get pods
```

- let's see the history of the current deployment
```bash
kubectl rollout history deployment cc-dep-01
```

- let's compare the current version with a previous version:
```bash
kubectl rollout history deployment cc-dep-01 --revision=2
```

- let's roll back and check the pods:
```bash
kubectl rollout undo deployment cc-dep-01

kubectl get pods
```

## ConfigMaps and Secrets

- create ConfigMap:
```bash
kubectl apply -f basic-configmap.yaml
```

- list ConfigMaps:
```bash
kubectl get configmaps
```

- describe ConfigMap
```bash
kubectl describe configmap <config-map>
```

## Services and volumes
- let's create the ConfigMap for PostgreSQL DB:
```bash
kubectl apply -f db-config-map.yaml
```

- create the Persistent Volume and Persistent Volume Claim for DB:
```bash
kubectl apply -f db-persistent-volume.yaml -f db-persistent-volume-claim.yaml

kubectl get pv
kubectl get pvc
```

- create the DB pod:
```bash
kubectl apply -f db-pod-config.yaml
```

- let's expose the DB port using ClusterIP service:
```bash
kubectl apply -f db-cluster-ip-config.yaml
```

- let's create API pod and expose it:
```bash
kubectl apply -f api-pod-config.yaml

kubectl expose pod apipod --port 80 --type=NodePort # we will see the port used by the service, for example 80:30714/TCP
# OR
kubectl apply -f api-nodeport-config.yaml

# add port-forwarding
kubectl port-forward services/api 3000:80 # we can send requests to localhost:3000
```

- let's configure the DB in the K8S pod:
```bash
kubectl exec -it db-pod -- bash # entering in the DB pod

psql -h localhost -U admin --password books # connecting to the DB instance

```

- and we will run this SQL query:
```sql
CREATE TABLE IF NOT EXISTS books (
    id serial PRIMARY KEY,
    title varchar NOT NULL,
    author varchar NOT NULL
);
```