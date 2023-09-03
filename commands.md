### microk8s

```bash
### to uninstall microk8s
sudo snap remove microk8s
### to install microk8s
sudo snap install microk8s --classic --channel=1.28
sudo usermod -a -G microk8s $USER
sudo chown -f -R $USER ~/.kube
### log out
microk8s status --wait-ready
### Use add-ons
microk8s enable dns
microk8s enable hostpath-storage
### kubeconfig
microk8s config view > /home/work/.kube/config
```

### Istio

```bash
### Install
helm repo add istio https://istio-release.storage.googleapis.com/charts
helm repo update
kubectl create namespace istio-system
helm install istio-base istio/base -n istio-system --set defaultRevision=default
helm ls -n istio-system
helm install istiod istio/istiod -n istio-system --wait
helm status istiod -n istio-system
kubectl create namespace istio-ingress
helm install istio-ingress istio/gateway -n istio-ingress --wait
### Remove
helm delete istio-ingress -n istio-ingress
kubectl delete namespace istio-ingress
helm delete istiod -n istio-system
helm delete istio-base -n istio-system
kubectl delete namespace istio-system
kubectl get crd -oname | grep --color=never 'istio.io' | xargs kubectl delete
```

### Signoz

```bash
###Install
helm repo add signoz https://charts.signoz.io
helm repo list
kubectl create ns monitoring
helm --namespace monitoring install signoz signoz/signoz
### verify
export SERVICE_NAME=$(kubectl get svc --namespace monitoring -l "app.kubernetes.io/component=frontend" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace monitoring port-forward svc/$SERVICE_NAME 3301:3301
### Remove
helm -n platform monitoring "signoz"
kubectl -n monitoring patch \
  clickhouseinstallations.clickhouse.altinity.com/my-release-clickhouse \
  -p '{"metadata":{"finalizers":[]}}' --type=merge
kubectl -n monitoring delete pvc -l app.kubernetes.io/instance=signoz
kubectl delete namespace monitoring

```
