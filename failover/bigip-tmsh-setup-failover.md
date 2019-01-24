##### Initial Setup for HA environment

## node01 create the VXLAN tunnel

```
tmsh create auth partition K8s
tmsh create net tunnels vxlan fl-vxlan port 8472 flooding-type none
tmsh create net tunnels tunnel fl-vxlan key 1 profile fl-vxlan local-address 192.168.200.93 secondary-address 192.168.200.91 traffic-group traffic-group-1
tmsh create net self vxlan-self address 10.244.20.91/255.255.0.0 allow-service none vlan fl-vxlan
tmsh create net self vxlan-float address 10.244.20.93/255.255.0.0 allow-service none vlan fl-vxlan traffic-group traffic-group-1
```

## node02 create the VXLAN tunnel

```
tmsh create auth partition K8s
tmsh create net tunnels vxlan fl-vxlan port 8472 flooding-type none
tmsh create net tunnels tunnel fl-vxlan key 1 profile fl-vxlan local-address 192.168.200.93 secondary-address 192.168.200.92 traffic-group traffic-group-1
tmsh create net self vxlan-self address 10.244.20.91/255.255.0.0 allow-service none vlan fl-vxlan
tmsh create net self vxlan-float address 10.244.20.93/255.255.0.0 allow-service none vlan fl-vxlan traffic-group traffic-group-1
```