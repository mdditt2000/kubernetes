#!/bin/bash

#delete container f5-hello-world
kubectl delete -f f5-hello-world-service.yaml
kubectl delete -f f5-hello-world-configmap.yaml
kubectl delete -f f5-hello-world-https-configmap.yaml
kubectl delete -f f5-hello-world-waf-configmap.yaml
kubectl delete -f f5-hello-world-deployment.yaml