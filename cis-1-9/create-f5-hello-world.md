#!/bin/bash

#create container f5-hello-wrold
kubectl create -f f5-hello-world-deployment.yaml
kubectl create -f f5-as3-configmap.yaml
kubectl create -f f5-hello-world-service.yaml