# Install the BIG-IP Controller: Kubernetes 
Steps following to setup the big-ip controller for kubernetes. This process is documented at https://clouddocs.f5.com/containers/v2/kubernetes/kctlr-app-install.html#kctlr-initial-setup-bigip

## Assumptions
|Role|FQDN|IP|OS|VERSION|
|----|----|----|----|----|
|Master|big-ip-ve1-pme.example.com|192.168.200.91|BIG-IP|13.1|

## Initial Setup

##### Create a new partition on your BIG-IP system.
```
tmsh create auth partition K8s
```
##### Create vxlan tunnels on your BIG-IP system
```
tmsh create net tunnels vxlan fl-vxlan port 8472 flooding-type none
tmsh create net tunnels tunnel fl-vxlan key 1 profile fl-vxlan local-address 192.168.200.91
tmsh create net self 10.244.20.91 address 10.244.20.91/255.255.0.0 allow-service none vlan fl-vxlan
tmsh create net self 10.244.20.92 address 10.244.20.92/255.255.0.0 allow-service none vlan fl-vxlan traffic-group traffic-group-1
```

## Set up RBAC Authentication

##### Store your BIG-IP login credentials in a Secret
```
kubectl create secret generic bigip-login --namespace kube-system --from-literal=username=admin --from-literal=password=admin
```
##### Create a Service Account for the BIG-IP Controller.
```
kubectl create serviceaccount bigip-ctlr -n kube-system
```
##### Create cluster role for bigip service account.
```
kubectl create clusterrolebinding k8s-bigip-ctlr-clusteradmin --clusterrole=cluster-admin --serviceaccount=kube-system:k8s-bigip-ctlr
```
##### Create cluster deplpoyment.

We have two deployment mode options, Nodeport or Cluster.

###### Nodeport mode

```
kubectl create -f f5-nodeport-deployment.yaml
```

###### Cluster mode

```
kubectl create -f f5-cluster-deployment.yaml
```
##### Add the BIG-IP device to the flannel overlay network.
##### Find the VTEP MAC address
```
tmsh
show /net tunnels tunnel fl-vxlan all-properties

-------------------------------------------------
Net::Tunnel: fl-vxlan
-------------------------------------------------
MAC Address                     00:50:56:bb:2a:ac
Interface Name                           fl-vxlan
```
##### Create a Kubernetes Node for the BIG-IP device.
```
kubectl create -f f5-bigip-node.yaml
```