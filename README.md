# Setting up Nginx Ingress Controller

Prerequisites:
- kubernetes cluster
- [metallb]

Using helm install nginx-ingress
```sh
$  helm repo add stable https://charts.helm.sh/stable
$  helm repo list
$  helm repo update
$  helm install stable/nginx-ingress -g --set rbac.create=true
```
Deploy simple nginx to test
```sh
$  kubectl apply -fhttps://raw.githubusercontent.com/tixsalvador/kubernetes-ingress/main/nginx_plain.yaml
```
Expose the port
```sh
$  kubectl expose deploy nginx --port 80
```
Create ingress resource yaml
```sh
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations: 
    kubernetes.io/ingress.class: nginx
  name: nginx-ingress
spec:
  rules:
  - host: wordpress.salvador.org
    http:
      paths:
      - backend: 
          serviceName: nginx
          servicePort: 80
```
Apply the resource 
```sh
$  kubectl apply -f https://raw.githubusercontent.com/tixsalvador/kubernetes-ingress/main/ingress-resource.yml
```
Check IP address of Metallb load balancer and edit host file to match FQDN

Since I'm using vagrant I need to  add  Iptable rules to route port 80 traffic from public to the load balancer
```sh
$   sudo iptables -A PREROUTING -t nat -i eth2 -p tcp --dport 80 -j DNAT --to 10.10.10.70:80
$   sudo iptables -A FORWARD -p tcp  -i eth2  --dport 80 -j ACCEPT
$   sudo iptables -A POSTROUTING -t nat -p tcp -d 10.10.10.70 --dport 80 -j SNAT --to-source 10.10.10.60
```
eth2 : Public NIC  
10.10.10.70: Load Balancer IP  
10.10.10.60: Private IP  

[metallb]: https://github.com/tixsalvador/kubernetes-metallb


