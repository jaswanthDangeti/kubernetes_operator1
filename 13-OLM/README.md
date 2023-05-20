## OLM Installation. 
```
kubectl create -f https://raw.githubusercontent.com/operator-framework/operator-lifecycle-manager/master/deploy/upstream/quickstart/crds.yaml
kubectl create -f https://raw.githubusercontent.com/operator-framework/operator-lifecycle-manager/master/deploy/upstream/quickstart/olm.yaml	  
	  
```

## Check the OLM Installtion Status:
```
kubectl get all -n olm 
kubectl get ns 
kubectl get all -n operators
operator-sdk olm status 
```

## Build the Docker Image & Publish 

### 1. [Export Varibales]
```
export VERSION=1.0.0
export IMG=ttl.sh/amitvashist7-configmap-operator-test:v$VERSION 
export BUNDLE_IMG=ttl.sh/amitvashist7-configmap-operator-test-bundle:v$VERSION	 
export CATALOG_IMG=ttl.sh/amitvashist7-configmap-operator-test-catalog:v$VERSION	

echo $VERSION $IMG $BUNDLE_IMG $CATALOG_IMG
```



### 2. [Docker Build with Image Varibales]
```
make docker-build docker-push
```



### 3. [Make Bundle & Build Bundle Docker Image with Image Varibales]
```
make bundle bundle-build bundle-push
```

### 4. [Validate Bundle Image with Operator SDK]
```
operator-sdk bundle validate $BUNDLE_IMG
```
output
```
root@master: # operator-sdk bundle validate $BUNDLE_IMG
INFO[0000] Unpacking image layers                       
INFO[0000] running /usr/bin/docker pull ttl.sh/amitvashist7-configmap-operator-test-bundle:v1.0.0 
INFO[0002] running docker create                        
INFO[0002] running docker cp                            
INFO[0003] running docker rm                            
INFO[0003] All validation tests have completed successfully 
root@master: #
```

### 5. [Deploy Bundle Image with Operator SDK on K8s]
```
operator-sdk run bundle -n operators $BUNDLE_IMG 
```

### 6. Status Commands 
```
1. Check the OLM Status
   operator-sdk olm status

2. Check the Operator group:
   kubectl get og
   
3. Check a CatalogSource:
   kubectl get catalogsource

4. Check a Subscription 
   kubectl get subscription
   
5. Check the cluster service version (CSV):
   kubectl get csv

6. Check the pods for the Operator:
   kubectl get pods 

7. Check the Operator:
   kubectl get operators 
   
8. Check the object which operator has deployed 
   kubectl describe configmap my-configmap  
```   


## In case namespace stuck in termination, then apply the below workaroud. 

## Update the JSON files ( Remove the Kubernetes from Finalize Section & Make is Empty )

```
 2007  kubectl get ns olm  -o json > olm_tmp.json
 2008  kubectl get ns operators  -o json > operators_tmp.json
 2010  vim olm_tmp.json 
 2012  vim operators_tmp.json 
 2013  kubectl proxy & 
 2014  curl -k -H "Content-Type: application/json" -X PUT --data-binary @olm_tmp.json http://127.0.0.1:8001/api/v1/namespaces/olm/finalize
 2015  kubectl get ns 
 2016  curl -k -H "Content-Type: application/json" -X PUT --data-binary @operators_tmp.json http://127.0.0.1:8001/api/v1/namespaces/operators/finalize

 ```




 ## Operator Upgrade 
## Build the Docker Image & Publish 

### 1. [Export Varibales]
```
export VERSION=2.0.0
export IMG=ttl.sh/amitvashist7-configmap-operator-test:v$VERSION 
export BUNDLE_IMG=ttl.sh/amitvashist7-configmap-operator-test-bundle:v$VERSION	 
export CATALOG_IMG=ttl.sh/amitvashist7-configmap-operator-test-catalog:v$VERSION	

echo $VERSION $IMG $BUNDLE_IMG $CATALOG_IMG
```



### 2. [Docker Build with Image Varibales]
```
make docker-build docker-push
```

### 3. [Make Bundle & Build Bundle Docker Image with Image Varibales]
```
make bundle bundle-build bundle-push
```

### 4. [Validate Bundle Image with Operator SDK]
```
operator-sdk bundle validate $BUNDLE_IMG
```
output
```
root@master: # operator-sdk bundle validate $BUNDLE_IMG
INFO[0000] Unpacking image layers                       
INFO[0000] running /usr/bin/docker pull ttl.sh/amitvashist7-configmap-operator-test-bundle:v1.0.0 
INFO[0002] running docker create                        
INFO[0002] running docker cp                            
INFO[0003] running docker rm                            
INFO[0003] All validation tests have completed successfully 
root@master: #
```

### 5. [Deploy Bundle Image with Operator SDK on K8s]
```
operator-sdk run bundle-upgrade -n operators $BUNDLE_IMG --timeout=15m 
```

