# Migration from iApp to AS3 declaration
This page is to help migrate from configmap using iApp to AS3

Example of ConfigMap with iApp

    kind: ConfigMap
    apiVersion: v1
    metadata:
    name: k8s.http
    namespace: default
    labels:
        f5type: virtual-server
    data:
    # See the f5-schema table for schema-controller compatibility
    # https://clouddocs.f5.com/containers/latest/releases_and_versioning.html#f5-schema
    schema: "f5schemadb://bigip-virtual-server_v0.1.7.json"
    data: |
        {
        "virtualServer": {
            "backend": {
            "serviceName": "f5-hello-world",
            "servicePort": 8080
            },
            "frontend": {
            "partition": "k8s",
            "iapp": "/k8s/f5.http_custom",
            "iappPoolMemberTable": {
                "name": "pool__members",
                "columns": [
                    {"name": "addr", "kind": "IPAddress"},
                    {"name": "port", "kind": "Port"},
                    {"name": "connection_limit", "value": "0"}
                ]
            },
            "iappOptions": {
                "description": "myService_f5.http iApp"
            },
            "iappVariables": {
                "monitor__monitor": "/#create_new#",
                "monitor__response": "none",
                "monitor__uri": "/v1/welcome",
                "net__client_mode": "lan",
                "net__server_mode": "lan",
                "pool__addr": "172.17.32.172",
                "pool__pool_to_use": "/#create_new#",
                "pool__port": "80",
                "pool__hosts": "1.1.1.1"
            }
            }
        }
        }

Example of ConfigMap with AS3

    kind: ConfigMap
    apiVersion: v1
    metadata:
    name: k8s.http
    namespace: default
    labels:
        f5type: virtual-server
        as3: "true"
    data:
    template: |
        {
            "$schema": "https://raw.githubusercontent.com/F5Networks/f5-appsvcs-extension/master/schema/latest/as3-schema.json",
            "class": "AS3",
            "declaration": {
                "class": "ADC",
                "schemaVersion": "3.11.0",
                "id": "urn:uuid:33045210-3ab8-4636-9b2a-c98d22ab915d",
                "label": "http",
                "remark": "Simple HTTP application",
                "k8s": {
                    "class": "Tenant",
                    "myService_f5_http": {
                        "class": "Application",
                        "template": "generic",
                        "myService_f5_http": {
                            "class": "Service_HTTP",
                            "virtualAddresses": [
                                "10.192.75.111"
                            ],
                            "virtualPort": 8080,
                            "pool": "pool__members"
                        },
                        "pool__members": {
                            "class": "Pool",
                            "monitors": [
                                {
                                    "use": "http_monitor"
                                }
                            ],
                            "members": [
                                {
                                    "servicePort": 80,
                                    "serverAddresses": []
                                }
                            ]
                        },
                        "http_monitor": {
                            "class": "Monitor",
                            "monitorType": "http",
                            "send": "HTTP GET /v1/welcome",
                            "receive": "none",
                            "adaptive": false
                        }
                    }
                }
            }
        }