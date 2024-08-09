
### con el cluster creado, lanzamos estos comandos
```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```
 kubectl delete -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml


#no teníamos registrado la implementación nginx para los ingress
#esto es para que nos de la ip pública
```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
helm dependency update  ./charts/sgi-umbrella/
helm package  ./charts/sgi-umbrella/
```
```sh
helm upgrade sgi sgi-umbrella-0.1.46.tgz --install --namespace sgi-demo -f ./config/values.demo.yaml
```

### prueba loadbalancer
```sh
kubectl apply -f loadbalancer.yaml
``` 



#kubectl create namespace sgi-demo
#kubectl config set-context --current --namespace=sgi-demo
#Esto es para configrar el contexto actual de kubectl para usar un namespace específico de forma predeterminada.
#Después de ejecutar esta instrucción, todos los comandos de kubectl que ejecutes sin especificar explícitamente un namespace se aplicarán al namespace sgi-dev. Esto es útil para evitar tener que especificar el namespace en cada comando, reduciendo errores y simplificando la administración.

