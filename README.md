vSRX HA Passthrough
================

**WORK IN PROGRESS**

A topology to test services through a vSRX High Availability Cluster 

Topology
========

The topology consists of four VMs: Client, Server, and two vSRXs in a cluster. The vSRXs are set in between the client and the server. Both the client and the server have appropriate routes pointing to the vSRX cluster so they can access each other through the cluster.

```
          +--------------------+                          
 <--------+                    |                          
      eth0|   Client           | +                        
   To host|                    | | Route to 192.168.0.0/24
          +---------------+----+ v                        
                          | eth1                                
                          | 172.16.0.10/24                      
                          |                              
                          | reth0.0
                          | 172.16.0.1/24
                    +-----+------------------------+
                    |                              |         
                    | ge-0/0/1.0                   | ge-0/0/8.0         
          +---------+----------+ fxp1 fxp1 +---------+---------+                   
 <--------+                    +-----------+                   +------->      
   fxp0.0 |  vSRX Node0        |           |  vSRX Node1       | fxp0.0 
  To host |                    +-----------+                   | To host
          +---------+----------+ fab0 fab1 +---------+---------+ 
                    | ge-0/0/2.0                   | ge-0/0/9.0         
                    |                              |         
                    +-----+------------------------+         
                          | reth1.0 
                          | 192.168.0.1/24
                          | 
                          | 192.168.0.10/24                     
                          | eth1                                
          +---------------+----+ ^                        
 <--------+                    | | Route to 172.16.0.0/24 
      eth0|   Server           | +                        
   To host|                    |                          
          +--------------------+                          
```

### Vagrant Note

Do to the inner workings of Vagrant each host has a virtual NIC connected back to the host running the virtual machines. This allows Vagrant to provision and control each VM over the SSH protocol. These interfaces are depicted on the topology above.

To use this lab with VMware Fusion or Workstation you must first purchase and install the Vagrant VMware Plugin.

[Vagrant VMware Plugin](https://www.vagrantup.com/vmware)

**VM Access Information**

-	[VM Host Passwords](https://github.com/JNPRAutomate/vSRXHA-Passthrough/blob/master/docs/vmpasswords.md)
