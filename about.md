
## External
- domain name attached to ip address

## Kubernetes:

- sslproxy service exposed as loadbalancer on port 443
  configuration required:
  - a name
  generates
  - the external ip address
- connects to an nginx container
  configuration required
  - ssl certs for the external DNS name
  - name of the upstream http kubernetes service

## Docker
The nginx container can just be the stock library container
since the proxy configuration can be mixed in, and the certificates
mounted at runtime 
