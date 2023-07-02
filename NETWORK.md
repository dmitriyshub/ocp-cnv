# Kubernetes Networking
#### Kubernetes SDN (Software defined networking)
- Kubernetes automatically assigns an IP address to every pod. By default, all pods can communicate with each other

- k8s implements this infrastructure by managing a software-defined network (SDN). The SDN allows Kubernetes to control the network traffic and resources
___
#### Cluster Network Operator (CNO)

- Cluster Network Operator managing the SDN.
- The operator calls a plug-in that adheres to the Container Network Interface (CNI) specification to configure container network interfaces, That allows to provide different plug-ins for their implementation

- OCP includes a few plug-in providers such as OpenShift SDN, OVN-Kubernetes, and Kuryr. By default, the OpenShift SDN provider, which implements the SDN by using Open vSwitch (OVS) virtual switches
___
![service sdn](/images/pod-service-sdn.svg)

- The DNS operator automatically creates and updates DNS records. For services, the DNS Operator manages the `svc.cluster.local` domain name and creates records in the form `servicename.namespace.svc.cluster.local`

- In addition to managing the DNS server, the operator automatically creates the `/etc/resolv.conf` configuration file inside the pods  without further configuration

- You can also use the short names `servicename.namespace` or `servicename` to access the application.

#### **NOTE**
- The IP address Kubernetes assigns to the pod is not the IP address you get from inside the VM

- The `virt-launcher` process running inside the pod runs a DHCP server that provides an IP address and the DNS configuration to the VM and redirects the inbound traffic to the VM and routes the outbound traffic to its destination
___
#### Labels 
- Because services rely on labels to select the targeted pods, you need to add a label to the VirtualMachine resource

- Add the label to the virtual machine labels section under the `.spec.template.metadata.` labels path. This change ensures that the label is set to the `virt-launcher` pod
___

To create the service use this files:
- [clusterIP](yaml-examples/services/clusterIP.yaml)
- [NodePort](yaml-examples/services/NodePort.yaml)
- [LoadBalancer](yaml-examples/services/LoadBalancer.yaml)


To test and perform DNS queries for the service name:
```
oc run mytestnet -it --rm --image=rhel8/toolbox
getent hosts servicename.namespace.svc.cluster.local
getent hosts servicename.namespace
getent hosts servicename
```

#### Network Policies

- Kubernetes network policies allow you to configure isolation policies for individual pods. You apply network policies to pods and namespaces by using labels.

- Use the `oc label` namespace command to add a label to a namespace
`oc label namespace name key=value`

To create Network Policies use this files:
- [Network Policy](yaml-examples/network-policies/network-policy.yaml)
- [Less RestrictedNetwork Policy](yaml-examples/network-policies/network-policy.yaml)

___
```
oc get vmi,svc,endpoints
```

