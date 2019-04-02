#!/bin/bash

#delete kubernetes bigip container connecter, authentication and RBAC
kubectl delete secret generic bigip-login -n kube-system --from-literal=username=admin --from-literal=password=admin
kubectl delete serviceaccount k8s-bigip-ctlr -n kube-system
kubectl delete clusterrolebinding k8s-bigip-ctlr-clusteradmin --clusterrole=cluster-admin --serviceaccount=kube-system:k8s-bigip-ctlr
kubectl delete -f f5-cluster-deployment2.yaml
kubectl delete -f f5-bigip-node2.yaml