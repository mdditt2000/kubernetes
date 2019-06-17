# Container Ingress Services using AS3 Declarative API
This how to document demenstrates how CIS take advantage of an declarative API to configure and update a BIG-IP from a kuberenetes cluster. This configuration take advantage of cluster mode. In a cluster mode BIG-IP can reach the containers directly. 

## Use Case
Determinstate the following BIG-IP capabilties 

* End to End SSL
* Cookie persistence
* Application Firewall prevent owasp top 10

## Declarative API
The Application Services 3 Extension uses a declarative model, meaning CIS sends a declaration file using a single Rest API call. An AS3 declaration describes the desired configuration of an Application Delivery Controller (ADC) such as F5 BIG-IP in tenant- and application-oriented terms. An AS3 tenant comprises a collection of AS3 applications and related resources responsive to a particular authority (the AS3 tenant becomes a partition on the BIG-IP system). An AS3 application comprises a collection of ADC resources relating to a particular network-based business application or system. AS3 declarations may also include resources shared by Applications in one Tenant or all Tenants as well as auxiliary resources of different kinds.

**Note:** CIS uses the partition defined in the controller configuration by default to commincate with the F5 BIG-IP when adding static mac address and forwarding enteries for VXLAN

# Prerequisites for using AS3

* Install the AS3 RPM on the F5 BIG-IP. Following the link https://clouddocs.f5.com/products/extensions/f5-appsvcs-extension/latest/userguide/installation.html
* If the F5 BIG-IP is using un-signed default ssl certificates add **insecure=true** as shown below to the controller deployment yaml file. Example https://github.com/mdditt2000/kubernetes/blob/dev/cis-1-9/big-ip-98/f5-cluster-deployment.yaml
    ```
    args: [
        "--bigip-username=$(BIGIP_USERNAME)",
        "--bigip-password=$(BIGIP_PASSWORD)",
        "--bigip-url=192.168.200.98",
        "--bigip-partition=AS3",
        "--namespace=default",
        "--pool-member-type=cluster",
        "--flannel-name=fl-vxlan",
        "--log-level=INFO",
        "--insecure=true"
    ```
* Add as3: "true" to any configmap applied so that CIS knows the data fields is AS3 and not legacy container connector input data. Please note that CIS will use gojsonschema to validate the AS3 data. If the declaration doesnt conform with the schema an error will be logged. Example https://github.com/mdditt2000/kubernetes/blob/dev/cis-1-9/blank/f5-as3-configmap.yaml
    ```
    metadata:
    name: f5-hello-world-https
    namespace: default
    labels:
        f5type: virtual-server
        as3: "true"
    ```
* Create and deploy the kuberenetes service discovery lables. CIS can dynamically discover and update load balancing pool members using service discovery. CIS maps each pool definition in the AS3 template to a Kubernetes Service resource using a label. To create this mapping, add the following labels to your Kubernetes Service. Example https://github.com/mdditt2000/kubernetes/blob/dev/cis-1-9/deployment/f5-hello-world-service.yaml
    ```
    labels:
        app: f5-hello-world-end-to-end-ssl
        cis.f5.com/as3-tenant: AS3
        cis.f5.com/as3-app: A5
        cis.f5.com/as3-pool: secure_ssl_waf_pool
    name: f5-hello-world-end-to-end-ssl-waf
    ```
## Using a configmap with AS3



# Prerequisites for using AS3




# Show all logs from pod nginx written in the last hour
kubectl logs --since=1h