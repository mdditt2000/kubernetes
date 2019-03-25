# Container Ingress Services 1.9 Beta 
This page is to beta container ingress services 1.9 beta code. 

## Tested Version
The following version of K8S has been validated
- K8S version: 1.13.4
- AS3 Schema: 3.10.0-2(f5-appsvcs-3.10.0-2.noarch)
- Controller: ssurenr/k8s-bigip-ctlr:as3-m1

##### Open Issues
- 1. Observed that pool member is not getting deleted in BIGIP when corresponding deployment is deleted in K8S.
- 2. Could not able to create multiple services in BIGIP using AS3.
- 3. Observed crash in f5-cccl when used mixed combination of Configmaps with as3 schema versions 3.8.0 and 3.10.0.
- 4. CC appends endpoints of all the services with same metadata label