#### Notas prueba instalación argoCD

* Paso previo instalar argoCD
```sh
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64
```

```sh
minikube start --driver=docker
minikube addons enable ingress
```
```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
* esta no hace falta
```sh
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

* creamos namespace
```sh
kubectl create namespace sgi-demo
```

* mapeamos el puerto de argo cd 443 al 8080
```sh
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

* la constraseña que está en un secret de kubernetes
* La contraseña inicial de la admincuenta se genera automáticamente y se almacena como texto sin formato en el campo passwordde un secreto nombrado argocd-initial-admin-secret en el espacio de nombres de instalación de Argo CD. Puede recuperar esta contraseña simplemente usando la argocdCLI:

```sh
argocd admin initial-password -n argocd
```

* y ahora si ejecuto algo en argocd se ejecuta contra el servidor de argo.
```sh
argocd login localhost:8080 --username admin --password $(kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d; echo)
```

* añadimos bitnami
```sh
argocd repo add --name bitnami https://charts.bitnami.com/bitnami --type helm
```

* argocd creamos una aplicación, el equivalente al upgrade de helm, es como un registro, la aplicación sgi, el repo de helm es --repo, el path es umbrela, el servidor donde lo quiero desplegar de minikube, el namespace de sgi-demo y el values desde el path del umbrella que es desde donde está el values del umbrella.

```sh
argocd app create sgi --repo https://github.com/didiez/hercules-sgi-helm --path charts/sgi-umbrella --dest-server https://kubernetes.default.svc --dest-namespace sgi-demo --values ../../config/values.demo.yaml
```


* 
```sh
az aks get-credentials --resource-group grupo_recursos_060824 --name cluster_060824
```
