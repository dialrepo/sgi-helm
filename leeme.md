
### con el cluster creado, lanzamos estos comandos para registrar la implementación nginx para los ingress
```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```
kubectl delete -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml


```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
helm dependency update  ./charts/sgi-umbrella/
helm package  ./charts/sgi-umbrella/
```
```sh
helm upgrade sgi sgi-umbrella-0.1.46.tgz --install --namespace sgi-demo -f ./config/values.demo.yaml
```

* Acceder a la bbdd o panel keycloak
```sh
kubectl port-forward sgi-postgresql-0 5432:5432 -n sgi-demo
```

```sh
kubectl port-forward sgi-sgi-keycloak-67995bdc5d-n5crh 8080:8080 -n sgi-demo
```

### Conectar Azure
```sh
az login
az aks get-credentials --resource-group grupo_recursos_aks --name sgi_demo 
```

* Esto es para configrar el contexto actual de kubectl para usar un namespace específico de forma predeterminada.
```sh
kubectl create namespace sgi-demo
kubectl config set-context --current --namespace=sgi-demo
```

Después de ejecutar esta instrucción, todos los comandos de kubectl que ejecutes sin especificar explícitamente un namespace se aplicarán al namespace sgi-dev. Esto es útil para evitar tener que especificar el namespace en cada comando, reduciendo errores y simplificando la administración.


### paso previo instalar argoCD
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
(esta no # kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}')

```sh
kubectl create namespace sgi-demo
kubectl config set-context --current --namespace=sgi-demo
```

### mapeamos el puerto de argo cd 443 al 8080
```sh
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

### la constraseña que está en un secret de kubernetes y ahora si ejecuto algo en argocd se ejecuta contra el servidor de argo.
```sh
argocd login localhost:8080 --username admin --password $(kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d; echo)
```

### añadimos bitnami
```sh
argocd repo add --name bitnami https://charts.bitnami.com/bitnami --type helm
```


### argocd creamos una aplicación, el equivalente al upgrade de helm, es como un registro, la aplicación sgi, el repo de hel es --repo, el path es umbrela, el servidor donde lo quiero desplegar de minikube, el namespace de sgi-demo y el values desdesde el path del umbrella que es desde donde está el values del umbrella.

```sh
argocd app create sgi --repo https://github.com/didiez/hercules-sgi-helm --path charts/sgi-umbrella --dest-server https://kubernetes.default.svc --dest-namespace sgi-demo --values ../../config/values.demo.yaml
```sh




