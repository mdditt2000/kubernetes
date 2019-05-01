# Container Ingress Services 1.9 
This page is for container ingress services 1.9 code. 

## Tested Version
The following version of K8S has been validated
- K8S version: 1.13.4
- AS3 Schema: 3.10.0
- Controller: f5networks/k8s-bigip-ctlr:1.9.0

##### Gotchas 
1. SSL certification verification failure

In particular you need to ensure you add the "--insecure" flag otherwise you will see errors like:
 
https://clouddocs.f5.com/containers/v2/kubernetes/kctlr-k8s-as3.html#parameters
 
In particular you need to ensure you add the "--insecure" flag otherwise you will see errors like:

```
2019/04/12 17:13:44 [DEBUG] [as3_log] Processing AS3 POST call with AS3 Manager
2019/04/12 17:13:44 [DEBUG] [as3_log] REST call with AS3 Manager
2019/04/12 17:13:44 [ERROR] [as3_log] REST call error: Post https://10.1.20.10/mgmt/shared/appsvcs/declare: x509: cannot validate certificate for 10.1.20.10 because it doesn't contain any IP SANs
```
 
Continue to not validate the SSL certificate for non-AS3 related REST API calls. There is work to improve this workflow in the future (currently it will only work with a CA signed certificate if you do not specify the --insecure flag).