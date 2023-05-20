## In this demo will be creating a Operator Testing  Operator. 

## We will be deploying a ConfigMap Object with Some Data in K8s via this operator. 

### Procedure
```
cd operator-testing/
```
```
operator-sdk init --plugins=ansible --domain example.com
```
```
operator-sdk create api --group cache --version v1alpha1 --kind Configmap --generate-role
```

## We need to install molecule to test our operator. 
```
pip install molecule 
```
```
snap install kustomize
```

## List of the molecule Genrate

Sample File 
```
molecule/
├── default
│   ├── converge.yml
│   ├── create.yml
│   ├── destroy.yml
│   ├── kustomize.yml
│   ├── molecule.yml
│   ├── prepare.yml
│   ├── tasks
│   │   └── configmap_test.yml
│   └── verify.yml
└── kind
    ├── converge.yml
    ├── create.yml
    ├── destroy.yml
    └── molecule.ym
```

## Devlop the Configmap Operators

```
vim  roles/configmap/tasks/main.yml 
```

Sample
```
---
- name: ConfigMap Example is "{{ state }}"
  kubernetes.core.k8s:
    definition: 
      kind: ConfigMap
      apiVersion: v1
      metadata: 
        name: my-configmap
        namespace: default
      data:
        key: "Hello world"  
    state: "{{ state }}"
  ignore_errors: true
```

```
vim  roles/configmap/defaults/main.yml
```

Sample
```
---
state: present
```


## Update the Cache Deployment file 
```
vim config/samples/cache_v1alpha1_webapp.yaml
```

Sample
```
apiVersion: cache.example.com/v1alpha1
kind: Configmap
metadata:
  labels:
    app.kubernetes.io/name: configmap
    app.kubernetes.io/instance: configmap-sample
    app.kubernetes.io/part-of: operator-testing
    app.kubernetes.io/managed-by: kustomize
    app.kubernetes.io/created-by: operator-testing
  name: configmap-sample
spec:
  state: present

```

## Update the RBAC Role for Configmaps Object

## Let's build the Operator Docker Image & Publish 

```
make docker-build docker-push IMG=ttl.sh/amitvashist7-operator-testing-2:2h
```

## It's to setup molecule environment for operator testing ( Export the Latest Operator Image)
```
export OPERATOR_IMAGE=ttl.sh/amitvashist7-operator-testing-2:2h
```

## Let's Run molecule  Create to Set the baseline Env.
```
molecule create
```

## Let molecule deploy the operator environment on K8s Local Cluster.
```
molecule converge
```

## Let's run molecule default test case.
```
molecule verify
```

NotePoint : The above command might fail with assert error "FIXME Add real assertions for your operator"

## Let's update the tasks/_test.yaml with our custom test usecase. 

### In this usecase we are looking for a configmap which has a key [key] with respective value [Hello world]. 

```
---
- name: Create the cache.example.com/v1alpha1.Configmap
  k8s:
    state: present
    namespace: '{{ namespace }}'
    definition: "{{ lookup('template', '/'.join([samples_dir, cr_file])) | from_yaml }}"
    wait: yes
    wait_timeout: 300
    wait_condition:
      type: Successful
      status: "True"
  vars:
    cr_file: 'cache_v1alpha1_configmap.yaml'

- debug: var=configmap
  vars:
    configmap: '{{lookup("kubernetes.core.k8s", api_version="v1", kind="ConfigMap", namespace="default", resource_name="my-configmap") }}'

- debug: var=namesp
  vars:
    namesp: '{{lookup("kubernetes.core.k8s", api_version="v1", kind="Namespace") }}'

- assert: 
    that: configmap.data.key == 'Hello world'
  vars:
    configmap: '{{lookup("kubernetes.core.k8s", api_version="v1", kind="ConfigMap", namespace="default", resource_name="my-configmap") }}'

```


## Let's run molecule custom test case ( if everything works as expected then we can destroy the molecule deployed env. )
```
molecule verify
```

## if everything works as expected then we can destroy the molecule deployed env. 
```
molecule destroy
```

## Let's Deploy newly built Operator on K8s Cluster
```
make deploy IMG=ttl.sh/amitvashist7-operator-testing-2:2h
```

## Let's check the Operator Config Status 
```
kubectl get configmap
```



