
# paso previo instalar argoCD
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64


minikube start --driver=docker
minikube addons enable ingress


kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
#  esta no # kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

kubectl create namespace sgi-demo

# mapeamos el puerto de argo cd 443 al 8080
kubectl port-forward svc/argocd-server -n argocd 8080:443


# la constraseña que está en un secret de kubernetes
# y ahora si ejecuto algo en argocd se ejecuta contra el servidor de argo.
argocd login localhost:8080 --username admin --password $(kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d; echo)

# añadimos bitnami
argocd repo add --name bitnami https://charts.bitnami.com/bitnami --type helm


# argocd creamos una aplicación, el equivalente al upgrade de helm, es como un registro, la aplicación sgi, el repo de hel es --repo, el path es umbrela, el servidor donde lo quiero desplegar de minikube, el namespace de sgi-demo y el values desdesde el path del umbrella que es desde donde está el values del umbrella.

# argocd app create sgi --repo https://github.com/dialrepo/sgi-helm --path charts/sgi-umbrella --dest-server https://kubernetes.default.svc --dest-namespace sgi-demo --values ../../config/values.demo.yaml

argocd app create sgi --repo https://github.com/didiez/hercules-sgi-helm --path charts/sgi-umbrella --dest-server https://kubernetes.default.svc --dest-namespace sgi-demo --values ../../config/values.demo.yaml




# az aks get-credentials --resource-group grupo_recursos_060824 --name cluster_060824
