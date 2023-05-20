```
1997  mkdir test; cd test
1998  operator-sdk init --plugins=ansible --domain example.com
1999  operator-sdk create api --group cache --version v1alpha1 --kind Memcached --generate-role
2000  echo $VERSION $IMG $BUNDLE_IMG $CATALOG_IMG
2001  export VERSION=0.0.1
2002  export IMG=ttl.sh/amitvashist7-memcache-operator:v$VERSION
2003  export BUNDLE_IMG=ttl.sh/amitvashist7-memcache-operator-bundle:v$VERSION
2004  export CATALOG_IMG=ttl.sh/amitvashist7-memcache-operator-catalog:v$VERSION
2005  echo $VERSION $IMG $BUNDLE_IMG $CATALOG_IMG
2006  make docker-build docker-push $IMG
2007  make bundle
2008  make bundle bundle-build bundle-push $BUNDLE_IMG
2009  operator-sdk bundle validate $BUNDLE_IMG
2010  operator-sdk run bundle -n operators $BUNDLE_IMG
2011  kubectl get ns
2012  kubectl get pods
2013  kubectl get all -n A
2014  kubectl get all -A
```
