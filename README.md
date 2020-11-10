
helm repo add stable https://charts.helm.sh/stable
helm repo list
helm repo update
helm install stable/nginx-ingress -g --set rbac.create=true
kubectl apply -f nginx_plain.yaml
kubectl expose deploy nginx --port 80
kubectl deploy -f ingress-resource.yml
