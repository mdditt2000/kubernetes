# Install Kubernetes Dashbaord
When setting up dashboard you will need three components and a service account as displayed by the four files below

- dashboard.yaml
- heapster.yaml "metrix collection application"
- influxdb.yaml "Storage engine"
- sa_cluster_admin.yaml

Get these files from https://github.com.mdditt2000/kubernetes - please note i forked from justmeandopensource/kubernetes

##### Clone yaml files justmeandopensource/kubernetes GIT repo
```
git clone https://github.com.mdditt2000/kubernetes
```
##### Create influxdb components 
Create influxdb and validate
```
su kube
kubectl create -f influxdb.yaml

kubectl -n kube-system get pods -o wide
NAME                                                       READY   STATUS    RESTARTS   AGE     IP               NODE                               NOMINATED NODE   READINESS GATES
monitoring-influxdb-7db9fd7459-ntxkb                       1/1     Running   0          32s     10.244.1.4       k8s-1-13-node.example.com     <none>           <none>
```
##### Create heapster components 
Create heapster and validate
```
kubectl create -f heapster.yaml

kubectl -n kube-system get pods -o wide
NAME                                                       READY   STATUS    RESTARTS   AGE     IP               NODE                               NOMINATED NODE   READINESS GATES
heapster-855fc65cd7-66jnj                                  1/1     Running   0          8s      10.244.1.5       k8s-1-13-node.example.com     <none>           <none>
monitoring-influxdb-7db9fd7459-ntxkb                       1/1     Running   0          92s     10.244.1.4       k8s-1-13-node.example.com     <none>           <none>
```
##### Create dashboard components 
Create dashboard and validate. In my example added nodeport

```
# ------------------- Dashboard Service ------------------- #

kind: Service
apiVersion: v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kube-system
spec:
  type: NodePort -- changed from cluster port
  ports:
    - port: 443
      targetPort: 8443
      nodePort: 32323  -- Added listerner port
  selector:
    k8s-app: kubernetes-dashboard   
```

```
kubectl create -f dashboard.yaml 
kubectl -n kube-system get pods -o wide
NAME                                                       READY   STATUS    RESTARTS   AGE     IP               NODE                               NOMINATED NODE   READINESS GATES
heapster-855fc65cd7-66jnj                                  1/1     Running   0          2m19s   10.244.1.5       k8s-1-13-node.example.com     <none>           <none>
kubernetes-dashboard-79ff88449c-2cfjr                      1/1     Running   0          8s      10.244.1.6       k8s-1-13-node.example.com     <none>           <none>
monitoring-influxdb-7db9fd7459-ntxkb                       1/1     Running   0          3m43s   10.244.1.4       k8s-1-13-node.example.com     <none>           <none>
```
##### Check cluster information 
Validate the cluster information to make sure the services are running. 

```
kubectl cluster-info
Kubernetes master is running at https://192.168.200.81:6443
Heapster is running at https://192.168.200.81:6443/api/v1/namespaces/kube-system/services/heapster/proxy
KubeDNS is running at https://192.168.200.81:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
monitoring-influxdb is running at https://192.168.200.81:6443/api/v1/namespaces/kube-system/services/monitoring-influxdb/proxy
```
##### Create service-account
To access the dashboard you need to create a service account
```
kubectl create -f sa_cluster_admin.yaml
```
###### Get secrets

```
kubectl describe sa dashboard-admin -n kube-system
Name:                dashboard-admin
Namespace:           kube-system
Labels:              <none>
Annotations:         <none>
Image pull secrets:  <none>
Mountable secrets:   dashboard-admin-token-h5pvx
Tokens:              dashboard-admin-token-h5pvx
Events:              <none>
```
###### Get token to access the dashboard
```
[kube@k8s-1-13-master root]$ kubectl describe sa dashboard-admin -n kube-system
Name:                dashboard-admin
Namespace:           kube-system
Labels:              <none>
Annotations:         <none>
Image pull secrets:  <none>
Mountable secrets:   dashboard-admin-token-h5pvx
Tokens:              dashboard-admin-token-h5pvx
Events:              <none>

kubectl describe secret dashboard-admin-token-h5pvx -n kube-system
Name:         dashboard-admin-token-h5pvx
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: dashboard-admin
              kubernetes.io/service-account.uid: 24576691-186d-11e9-9f7d-005056bb599e

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkYXNoYm9hcmQtYWRtaW4tdG9rZW4taDVwdngiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGFzaGJvYXJkLWFkbWluIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiMjQ1NzY2OTEtMTg2ZC0xMWU5LTlmN2QtMDA1MDU2YmI1OTllIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmUtc3lzdGVtOmRhc2hib2FyZC1hZG1pbiJ9.QuxTQo7-Aa6TArBaWfbPJpTJ1RWJemHxcyqU6HJd5Twmrmfsg4BXwb31mymaGrfHgRR6mpfaGcQqFW1tLELu2dXLy-Q6d9hNouURo19jG1ZdGqYTGde-OwVgRgZqF3EqAOzM90A9nGcSCwTihlzzeYgseuLGwknYtUp5-70eVqy9dYvlEFCEBvoKJ02JRep7qIZHP3KAL-mqm2c2wYj6mbFyyR6dOSTVm9fcfexzl4r_8OZu8mChmdQId0ZROaCE2Kk8P0gP_vbMUT6SnQvgMXIuntrhf0NyyR9IY9PeQ5ahXl8FyaAYmnnvRq0gNg1zhr-tdvOXgeoTSXPfTV4V_Q
```
