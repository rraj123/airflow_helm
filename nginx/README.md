### Objective

#### Goal 
1. Kind
2. Volume share
3. Helm based deploy

#### Kind Volume - PVC
```
curl -LO git.io/kind-pvc-hostpath.yaml
```

#### Launch a cluster with host-path config.. 
```
kind create cluster --name host-dyn-kind --config pvc/kind-hostpath-dynamic.yaml

kubectl cluster-info --context host-dyn-kind                                                                   ```

#### Deployed a simple nginx through helm

```
helm install pr-challenge .

kubectl port-forward svc/prchallenge 31235:80
```
Here is the simple 
```
curl -LO git.io/pvc-test.yaml
```
## Lets find out the host is created 

#### Recreate 

```
kind create cluster --name host-dyn-kind --config pvc/kind-hostpath-dynamic.yaml

kubectl apply -f git.io/pvc-test.yaml  (This PVC Test contains Deployement+PVC )

Check the volume is created 

Populate the data 
hostname > pvc/hostname

kubectl get pods -o wide 

let's force it over to our other worker!

kubectl drain --ignore-daemonsets kind-worker2

kubectl get pods -o wide 

now the pod is moved to another node
so make sure that it exists..

kubectl exec test-7f6685cbd7-lskb6 -- cat pvc/hostname  

now get the pv 
kubectl get pv pvc-a1047560-d0e5-4ba9-987f-27a40cf9cedc -o yaml > pv.yaml


clean up the pv to make it retain...
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pvc-de217954-4c84-4a36-bd90-c98d93aeb484
spec:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 2Gi
  claimRef:
    apiVersion: v1
    kind: PersistentVolumeClaim
    name: test
    namespace: default
  hostPath:
    path: /tmp/hostpath-provisioner/pvc-de217954-4c84-4a36-bd90-c98d93aeb484
    type: ""
  persistentVolumeReclaimPolicy: Retain
  storageClassName: standard
  volumeMode: Filesystem


  Now delete the cluster 

  kind delete cluster

Now recreate the cluster 
kind create cluster --config pvc/kind-hostpath-dynamic.yaml

apply the above pv config 
kubectl apply -f pv.yaml 

Now, deploy the deployment 

kubectl apply -f https://git.io/pvc-test.yaml  

kubectl get pvc   

kubectl get pv 

check the data exists between different clusters .. 
kubectl exec test-7f6685cbd7-9v5z9 

 kubectl exec test-7f6685cbd7-9v5z9 -- cat pvc/hostname

You should see the texts. 

Now clean up.. 
```

