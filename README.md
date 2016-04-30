# Kubernetes SSL-Proxy
_Install an nginx ssl-proxy into a kubernetes cluster, terminating TLS in front of a Kubernetes service_

Uses an ansible playbook to construct an nginx replication controller and the load-balancer service in front of it.

## Requirements:
- properly configured _kubectl_ utility to talk to your kubernetes cluster.  The
  environment variable KUBECONFIG should probably be set, unless you are using the
  default configuration file.
- the name of a kubernetes service which is listening on port 80
- ssl certificate and key files, and the directory where they are located

## What it does:
writes some kubernetes manifests to create
- a secret containing the ssl certificate data
- a config map containing the nginx server configuration
- a replication controller for nginx which references the config and secret
- a service specified as LoadBalancer type, exposing ports 80 and 443

applies those manifests using the local kubectl command

What's left for you to do:
- find the external ip of the LoadBalancer, and direct the domain name to it

## Problems

Playbooks are excessively customized for my particular uses.  The KUBECONFIG file is expected to be in a particular home-directory location based on the cluster name.  

The manifests should be implemented as a _Deployment_ rather than a _ReplicationController_ in order to make it easier to reload a changed certificate.

## Examples

```
ansible-playbook \
   -e clc_cluster_name=navarchos \
   -e service_name=imagelayers-web \
   -e cert_dir=~/src/github.com/CenturyLinkLabs/imagelayers-graph/ssl/  \
   -e service_namespace=production \
    install_sslproxy.yml
```


```
ansible-playbook \
   -e clc_cluster_name=navarchos \
   -e service_name=imagelayers-web \
   -e cert_dir=~/src/github.com/CenturyLinkLabs/imagelayers-graph/ssl/  \
   -e service_namespace=production \
    update_certificate.yml
```
After updating, make sure to cycle the existing sslproxy nginx pods in order to reload the secret.

## additional resources
Test your installation with [https://www.ssllabs.com/ssltest/](https://www.ssllabs.com/ssltest/)

Some certificate issuers seem to make it excessively hard to find the required intermediate certificates.  Fix your incomplete certificate chain using  information from [https://whatsmychaincert.com/](https://whatsmychaincert.com/)
