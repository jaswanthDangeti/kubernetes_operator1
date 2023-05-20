# Ansible Local Env with Kubernetes Collection

### Install Python Package Manager 
```
apt-get update ; apt-get install pip -y 
```

### Install Ansible 
```
pip install ansible
pip install ansible-runner
```

### Kubernetes Collections 
```
ansible-galaxy collection install kubernetes.core
```

### Openshift Rest Clinet Python Packages
```
pip3 install openshift
```

### Create a new ConfigMap Operator
```
mkdir configmap-operator
cd configmap-operator
operator-sdk init --plugins=ansible --domain example.com
operator-sdk create api --group cache --version v1alpha1 --kind Configmap --generate-role
```

### Install Ansible Dependency Collections & Packages
```
ansible-galaxy collection install -r requirements.yml --force
```


### New update a configmap task file with below config. 
```
root@master:~/configmap-operator# cat roles/configmap/tasks/main.yml 
---
- name: ConfigMap Example is "{{ state }}"
  kubernetes.core.k8s:
    kind: ConfigMap
    api_version: v1
    name: example-configmap
    namespace: default
    state: "{{ state }}"
  ignore_errors: true
root@master:~/configmap-operator# 
```

```


root@master:~/configmap-operator# cat roles/configmap/defaults/main.yml 
---
# defaults file for Configmap
state: present
root@master:~/configmap-operator# 
```

```
root@master:~/configmap-operator# cat configmap-playbook.yml 
---
- hosts: localhost
  roles:
    - configmap
root@master:~/configmap-operator# 
```

## Now Run the Playbook & Apply the changes 
```
ansible-playbook configmap-playbook.yml  
kubectl get configmap 
```

## Ad-hoc Variable Update 
```
ansible-playbook configmap-playbook.yml  --extra-vars state=absent
kubectl get configmap 
```

## Now let build a Operator & test the same. 
```
make docker-build docker-push IMG=ttl.sh/amitvashist7-configmap1:2h
make deploy IMG=ttl.sh/amitvashist7-configmap1:2h
```

```
 kubectl apply -f config/samples/cache_v1alpha1_configmap.yaml
```

### In case getting RBAC Error please do consider RBAC Roles For Configmap. 
