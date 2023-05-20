## In this demo will be creating a webapp Operator. 

## We will be deploying Deployment & Service Object in K8s via this operator. 

### Procedure
```
cd webapp-operator/
```
```
operator-sdk init --plugins=ansible --domain example.com
```
```
operator-sdk create api --group cache --version v1alpha1 --kind Webapp --generate-role
```

## Now update the webpp role with Deploy & Service Tasks. 
```
vim  roles/webapp/tasks/main.yml 
```



Sample File 
```
---
- name: start webapp
  kubernetes.core.k8s:
    definition:
      kind: Deployment
      apiVersion: apps/v1
      metadata:
        name: '{{ ansible_operator_meta.name }}-webapp'
        namespace: '{{ ansible_operator_meta.namespace }}'
      spec:
        replicas: "{{size}}"
        selector:
          matchLabels:
            app: webapp
        template:
          metadata:
            labels:
              app: webapp
          spec:
            containers:
            - name: webapp
              image: "{{image}}"
              ports:
                - containerPort: "{{port}}"  
- name: start webapp service
  kubernetes.core.k8s:
    definition:
      kind: Service
      apiVersion: v1
      metadata:
        name: '{{ ansible_operator_meta.name }}-webapp-svc'
        namespace: '{{ ansible_operator_meta.namespace }}'
      spec:
        ports:
        - port: 80
          protocol: TCP
          targetPort: "{{port}}"
        selector:
          app: webapp
        type: NodePort
```


```
vim  roles/webapp/defaults/main.yml 
```

Sample
```
---
# defaults file for Webapp
size: 1
image: nginx
port: 80
```

## Update the Cache Deployment file 
```
vim config/samples/cache_v1alpha1_webapp.yaml
```

Sample
```
apiVersion: cache.example.com/v1alpha1
kind: Webapp
metadata:
  labels:
    app.kubernetes.io/name: webapp
    app.kubernetes.io/instance: webapp-sample
    app.kubernetes.io/part-of: webapp-operator
    app.kubernetes.io/managed-by: kustomize
    app.kubernetes.io/created-by: webapp-operator
  name: webapp-sample
spec:
  size: 3
  image: nginx
  port: 80
```

## Let's build the Operator Docker Image & Publish 

```
make docker-build docker-push IMG=ttl.sh/amitvashist7-webapp3:2h
```

## Let's Deploy newly built Operator on K8s Cluster
```
make deploy IMG=ttl.sh/amitvashist7-webapp3:2h
```

## Let's check the Operator Deployment Status 
```
kubectl get pod -A 
```

## Logs of the Operator
```
 kubectl logs -f  operator-controller-pod-name -n webapp-operator-system
```

## Now Trigger the deployment from different terminal & check the operators log status.
```
kubectl  apply -f webapp-operator/config/samples/cache_v1alpha1_webapp.yaml
```

## Expecting Web Deploy & its pods to be running, along with webapp service. 
```
kubectl get pods,svc 
```

## In case service is missing check the ansible log & try to debug the issue ( mostlikely issue is with RBAC - Check & Fixed the same.)


