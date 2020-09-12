## apply ingress in aws
https://kubernetes.github.io/ingress-nginx/deploy/

make sure workloads is running on k8s cluster, all svc are using ClusterIP

wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.35.0/deploy/static/provider/aws/deploy.yaml save to local as ingress implementation yaml file, apply this file.

add ingress-basic.yaml and ingress-secure.yaml to the working directory, apply these files
create secret.yaml to include login credentials for queue svc, apply this file

```bash
kubectl get svc -n ingress-nginx # get loadbalancer(attached to ingress controller) endpoint
nslookup loadbalancer endpoint # get public ip of this endpoint
```
fake this ip to fleetman.com by revising /etc/hosts
fake this ip to queue.fleetman.com by revising /etc/hosts
try this domain name in the browser

## configure https with TLS termination at LB level
### setup http
in aws Route53 configure A record pointing to loadbalancer created by ingress
verity in browser it's accessable
curl http://... verity it works
curl https://... verity it returns local issuer certificate problem
### setup https/tls termination at loadbalancer level
in aws certificate manager-> request a public certificate
-> add domain names: e.g.: *.rfexpert.net, rfexpert.net
-> dns validation
-> create CNAME record (asssume provided domain names are registered with AWS)
-> done validation
### manually set loadbalancer listening on ssl tcp and plugin certificate
### or revise ingress config file: deploy.yaml
rewire incomming port 443 to targetPort: 80 (tls terminated at LB level,
between LB and ingress controller level is http/port80 traffic )
