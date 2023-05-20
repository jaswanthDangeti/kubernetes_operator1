## Install Go for the Go Operators Demo.

## Install on Ubuntu 
```
apt-get update 
```

## Download Go Binary
```
mkdir /temp; cd 
wget https://dl.google.com/go/go1.20.linux-amd64.tar.gz
```

## Extract & Deploy
```
tar -zxvf go1.20.linux-amd64.tar.gz
mv go /usr/local/
```

## Environment Config [ update your ~/.bashrc with below mentioned exports]  
```
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```

```
source ~/.bashrc
```

## Check the Go Version 
```
go version 
```

## Create a project.
```
mkdir memcached-operator ; cd memcached-operator
```

## Execute SDK to gerenate project folders:
```
operator-sdk init --domain=example.com   --repo=github.com/example-inc/memcached-operator
```

## Execute SDK to gererate API
```
operator-sdk create api \
    --resource=true \
    --controller=true \
    --group cache \
    --version v1 \
    --kind Memcached
```

## Export the Variable Docker Image Build
```
 export VERSION=0.0.1
 export IMG=ttl.sh/amitvashist7-go-memcached:v$VERSION 
 export BUNDLE_IMG=ttl.sh/amitvashist7-configmap-go-memcached-bundle:v$VERSION
```

## Now Build the Docker Image & Push to Reg. 
```
 make docker-build docker-push
```

## Post Build is completed, we can deploy our operator on K8s. 
```
make deploy
```

## Check the operators 
```
kubectl get ns 
kubectl get pods -n memcached-operator-system
kubectl logs -f memcached-operator-controller-manager-858df5f6f6-c2fjv -n memcached-operator-system
```

## Undeploy the operators
```
make undeploy
```



## Let's build the bundle Image. 
```
make bundle bundle-build bundle-push
```

## Let's deploy the operator bundle via OLM. 
```
operator-sdk bundle validate $BUNDLE_IMG

operator-sdk run bundle -n operators $BUNDLE_IMG 
`
