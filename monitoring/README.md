# Helm-k8s package management tool
make sure k8s cluster is up and running
from a server that's kubectl configured, do following:

install helm here https://github.com/helm/helm/releases
wget linux amd64 link to local
tar -zxvf the downloaded file at current directory

```bash
sudo mv linux-amd64/helm /usr/local/bin/
rm -rf ./linux-amd64/
helm version
helm init
kubectl get pods -n kube-system # verity tiller pod is up and running

```
# Helm-chart prometheus-operator integrating grafana
https://github.com/helm/charts/tree/master/stable/prometheus-operator

fix tiller pod priviledges  
```bash
kubectl create serviceaccount --namespace kube-system tiller

kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller

kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
```
```bash
helm repo update
helm install --name monitoring --namespace monitoring stable/prometheus-operator
```
export EDITOR=nano
kubectl edit grafana pod, change service type to LoadBalancer, verity in browser
helm will check yaml syntax when saving the file
refer to documentation get admin password to login grafana UI

delete package when done with it 
```bash
helm ls
helm delete --purge monitoring
```
