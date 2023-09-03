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
```
