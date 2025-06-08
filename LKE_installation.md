login to linode dashboard > \
click kubernetes > \
click create cluster > \
create cluster label > \
select region of choice > \
select latest kubernetes version > \
select HA Control Plane to No > \
disable Control Plane ACL > \
Add Node Pools > \
select shared cpu 2GB plan and count to 2 from default 3 and click add \
click create cluster

download the kubeconfig.yaml file to a location of your choice in your system
from the location of kubeconfig.yaml file run the below command
```
export KUBECONFIG=$PWD/lke-demo-kubeconfig.yaml
```
to validate the installation of Linode Kubernetes Engine
```
kubectl get nodes
```

