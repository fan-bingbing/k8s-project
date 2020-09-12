## This is a production grade deployment of sample microservices on AWS EKS
### infrastructure provisioning
prerequisite:
AWS IAM user(admin privilege) setup on local machine (access key, secret key, default region)

terraform>=0.12

awscli  

wget

kubectl<=1.17.0  # https://kubernetes.io/docs/tasks/tools/install-kubectl/

```bash
cd /your/local/clone/directory/terraform
terraform init # install aws plugin
terraform plan # verify 52 new resources is planned to be created
terraform apply # yes to confirm provisioning, it may take 15-20min to complete

aws eks --region <output-region-name> update-kubeconfig --name <output-cluster-name>
# get the access credentials for your cluster and automatically configure `kubectl`.

kubectl version # verify client and server version
```
### workload containers provisioning
```bash
cd /your/local/clone/directory/fleet_aws
kubectl get all # verify cluster running without workload
kubectl apply -f . # spin up workload
kubectl get all # verify pods/services are up and running
kubectl get svc fleetman-webapp # get webapp endpoint, verify access in browser

# download and unzip metrics-server
wget -O v0.3.6.tar.gz https://codeload.github.com/kubernetes-sigs/metrics-server/tar.gz/v0.3.6 && tar -xzf v0.3.6.tar.gz
# Deploy the metrics server to the cluster by running the following command
kubectl apply -f metrics-server-0.3.6/deploy/1.8+/
# Verify that the metrics server has been deployed
kubectl get deployment metrics-server -n kube-system
# monitor pods/nodes cpu/memory usage
kubectl top pods
kubectl top nodes

# deploy ELK stack logging system
cd /your/local/clone/directory/fleet_aws/elk_stack
kubectl apply -f . # spin up ELK stack
kubectl get all -n kube-system # verify stack is up and running
kubectl get svc kibana-logging # get kibana endpoint, verify acess in browser


# clean up
cd /your/local/clone/directory/fleet_aws/elk_stack
kubectl apply -f . # delete elk_stack
cd /your/local/clone/directory/fleet_aws/
kubectl delete -f metrics-server-0.3.6/deploy/1.8+/ # delete metrics-server
kubectl delete . # delete workload

# important! remember destroy infrastructure afterwards !
# EKS services is expensive !
cd /your/local/clone/directory/terraform/
terraform destroy  # yes to confirm destroy 52 resources
```

### explore more
#### refer to README to try deploy prometheus/grafana stack using helm chart
```bash
cd /your/local/clone/directory/monitoring
```
#### refer to README to try implement TLS termination in LB level using ingress controller
```bash
cd /your/local/clone/directory/ingress # a valid domain name is needed
```
