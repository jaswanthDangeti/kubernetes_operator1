  ```
  mkdir memcached-operator
  cd memcached-operator
  operator-sdk init --plugins=ansible --domain example.com
  operator-sdk create api --group cache --version v1alpha1 --kind Memcached --generate-role
  cat roles/memcached/tasks/main.yml
  vim roles/memcached/tasks/main.yml
  vim roles/memcached/defaults/main.yml
  vim roles/memcached/tasks/main.yml
  vim config/samples/cache_v1alpha1_memcached.yaml
  make docker-build docker-push IMG=ttl.sh/example-memcached:2h
  make deploy IMG=ttl.sh/example-memcached:2h
  kubectl get pods -n memcached-operator-system
  kubectl get deployment -n memcached-operator-system
  kubectl get pods  -n memcached-operator-system
  kubectl get pods -n memcached-operator-system
  kubectl logs -f memcached-operator-controller-manager-7c8c8fc556-5xjvb  -n memcached-operator-system
  kubectl get pods -n memcached-operator-system
  kubectl logs -f memcached-operator-controller-manager-7c8c8fc556-5xjvb  -n memcached-operator-system

```
```
  kubectl get memcached/memcached-sample -o yaml
  kubectl patch memcached memcached-sample -p '{"spec":{"size": 5}}' --type=merge
  kubectl get deploy
  kubectl scale --replicas=10 deploy memcached-sample-memcached
  watch -n .5 kubectl get pods
```
  
```
  make undeploy
```
