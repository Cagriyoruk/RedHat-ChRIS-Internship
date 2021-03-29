---
layout: post
author: Cagri
title: "MOC Integration and Local Openshift"
---

After MOC routes being down for a week, I was able to start a documentation to integrate MOC with ChRIS. Personally I like to call these files living documents. Because from every experience and different problems we encounter, we update the documentation. It's a good opportunity to reflect back and see the changes that can be done. While I was working with the document, one of my mentors suggested that I should build a local openshift cluster so that whenever the MOC is down, I can continue to deploy and test services/plugins locally.

To be honest, I thought building a local openshift cluster would take a lot of time and effort. However, I found out that `Openshift 4.x` versions use a tool called CodeReady containers. which is super easy to use. You just download it and place it in your PATH. Then with couple command lines, you have access to your local Openshift Cluster. If you're interested learning more about CodeReady Containers, there's a dedicated documentation -> [CodeReady Containers](https://access.redhat.com/documentation/en-us/red_hat_codeready_containers/1.24/html/getting_started_guide/index)

```
[cyoruk@localhost ~]$ crc start
WARN A new version (1.24.0) has been published on https://cloud.redhat.com/openshift/create/local 
INFO Checking if running as non-root              
INFO Checking if podman remote executable is cached 
INFO Checking if admin-helper executable is cached 
INFO Checking minimum RAM requirements            
INFO Checking if Virtualization is enabled        
INFO Checking if KVM is enabled                   
INFO Checking if libvirt is installed             
INFO Checking if user is part of libvirt group    
INFO Checking if libvirt daemon is running        
INFO Checking if a supported libvirt version is installed 
INFO Checking if crc-driver-libvirt is installed  
INFO Checking if systemd-networkd is running      
INFO Checking if NetworkManager is installed      
INFO Checking if NetworkManager service is running 
INFO Checking if dnsmasq configurations file exist for NetworkManager 
INFO Checking if the systemd-resolved service is running 
INFO Checking if /etc/NetworkManager/dispatcher.d/99-crc.sh exists 
INFO Checking if libvirt 'crc' network is available 
INFO Checking if libvirt 'crc' network is active  
INFO Starting CodeReady Containers VM for OpenShift 4.7.0... 
INFO CodeReady Containers VM is running           
INFO Starting network time synchronization in CodeReady Containers VM 
INFO Check internal and public DNS query ...      
INFO Check DNS query from host ...                
INFO Verifying validity of the kubelet certificates ... 
INFO Starting OpenShift kubelet service           
INFO Waiting for kube-apiserver availability... [takes around 2min] 
INFO Starting OpenShift cluster ... [waiting for the cluster to stabilize] 
INFO All operators are available. Ensuring stability ... 
INFO Operator network is progressing              
INFO All operators are available. Ensuring stability ... 
INFO Operators are stable (2/3) ...               
INFO Operators are stable (3/3) ...               
INFO Updating kubeconfig                          
WARN The cluster might report a degraded or error state. This is expected since several operators have been disabled to lower the resource usage. For more information, please consult the documentation 
Started the OpenShift cluster.

The server is accessible via web console at:
  https://console-openshift-console.apps-crc.testing

Log in as administrator:
  Username: kubeadmin
  Password: XXXXXXXXXXXXXXXXX
Log in as user:
  Username: developer
  Password: developer

Use the 'oc' command line interface:
  $ eval $(crc oc-env)
  $ oc login -u developer https://api.crc.testing:6443

```

After getting local Openshift up and running with CodeReady Containers, I've decided to deploy our services `pfioh` and `pman` in order to test actual plugins. The deployment process was pretty straightforward. I just changed a couple of lines in the templates and the services were ready to roll. Right after I deployed the services, I tried push/pull files from Openstack with `pfioh` and run plugins using `pman`. As expected `pfioh` was working without any problems. I was able to push and pull files from Openstack.

```
(chris_env) [cyoruk@localhost ~]$ oc login -u developer https://api.crc.testing:6443
Logged into "https://api.crc.testing:6443" as "developer" using existing credentials.

You have access to the following projects and can switch between them with ' project <projectname>':

    flask-chris
  * local-chris

Using project "local-chris".
(chris_env) [cyoruk@localhost ~]$ oc status
In project local-chris on server https://api.crc.testing:6443

http://pfioh-local-chris.apps-crc.testing to pod port 5055-tcp (svc/pfioh)
  dc/pfioh deploys docker.io/fnndsc/pfioh:latest 
    deployment #1 deployed 1 hour ago - 1 pod

http://pman-local-chris.apps-crc.testing to pod port 5010-tcp (svc/pman)
  dc/pman deploys docker.io/cagriyoruk/pman:debug 
    deployment #1 deployed 1 hour ago - 1 pod

4 infos identified, use 'oc status --suggest' to see details.
```

I want to wrap up this blog post with saying that I finished the first draft of the MOC integration. If you want to take a look or give any feedback you can find it here -> [ChRIS MOC Integration](https://github.com/Cagriyoruk/CHRIS_docs/blob/master/usecases/MOC_integration/moc_integration.adoc). As always, Thanks for reading 😃
