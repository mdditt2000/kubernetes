#!/bin/bash

#delete container f5-hello-world
kubectl delete service f5-hello-world
kubectl delete configmap f5-hello-world
kubectl delete -f f5-hello-world-https-configmap.yaml
kubectl delete -f f5-hello-world-waf-configmap.yaml
kubectl delete deployment f5-hello-world