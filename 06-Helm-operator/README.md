## In this demo will be creating a webapp Operator via helm plugin

## We will be deploying Deployment & Service Object in K8s via this operator. 

### Procedure
```
cd helm-operator/
```
```
operator-sdk init --plugins=helm --domain example.com
```
```
operator-sdk create api --group cache --version v1alpha1 --kind Webapp 
```

## Let check the watch file
```
cat watches.yaml
```

## Now update the webpp role with Deploy & Service Tasks. 

## Update the Cache Deployment file 
```
vim config/samples/cache_v1alpha1_webapp.yaml
```

Sample
```
apiVersion: cache.example.com/v1alpha1
kind: Webapp
metadata:
  name: webapp-sample
spec:
  # Default values copied from <project_dir>/helm-charts/webapp/values.yaml
  affinity: {}
  autoscaling:
    enabled: false
    maxReplicas: 100
    minReplicas: 1
    targetCPUUtilizationPercentage: 80
  fullnameOverride: ""
  image:
    pullPolicy: IfNotPresent
    repository: nginx
    tag: ""
  imagePullSecrets: []
  ingress:
    annotations: {}
    className: ""
    enabled: false
    hosts:
    - host: chart-example.local
      paths:
      - path: /
        pathType: ImplementationSpecific
    tls: []
  nameOverride: ""
  nodeSelector: {}
  podAnnotations: {}
  podSecurityContext: {}
  replicaCount: 3
  resources: {}
  securityContext: {}
  service:
    port: 80
    type: NodePort
  serviceAccount:
    annotations: {}
    create: true
    name: ""
  tolerations: []
```

## Let's build the Operator Docker Image & Publish 

```
make docker-build docker-push IMG=ttl.sh/amitvashist7-helm1:2h
```

## Let's Deploy newly built Operator on K8s Cluster
```
make deploy IMG=ttl.sh/amitvashist7-helm1:2h
```

## Let's check the Operator Deployment Status 
```
kubectl get pod -A 
```

## Logs of the Operator
```
 kubectl logs -f  helm-operator-pod-name -n webapp-operator-system
```

## Now Trigger the deployment from different terminal & check the operators log status.
```
kubectl  apply -f helm-operator/config/samples/cache_v1alpha1_webapp.yaml
```

## Expecting Web Deploy & its pods to be running, along with webapp service. 
```
kubectl get pods,svc 
```



