Default k8s nginx-ingress setup using helm templates
------------------------------------------------------

An ingress (among NodePort & LoadBalancer service types) exposes services externally.

Resources that are part of ingress implementation:

* namespace
* config map
* tls
* cluster role
* cluster role binding
* service account
* ingress controller
* service
* ingress


Demo shares steps that can be used to speed up configuring nginx ingress.

Get charts info at https://kubernetes-charts.storage.googleapis.com/index.yaml 

Create directory structure for helm templates:

```
./charts
./manifests
```

Fetch nginx-ingress charts:

```
helm fetch \
  --repo https://kubernetes-charts.storage.googleapis.com \
  --untar \
  --untardir ./charts \
  --version 1.6.13 \
    nginx-ingress
```

values.yaml file contains configuration for the nginx-ingress chart. <br/>
Default values are used in this demo and all resources will be created in default namespace.

Generate the manifests:

```
helm template \
  --values ./charts/nginx-ingress/values.yaml \
  --output-dir ./manifests \
    ./charts/nginx-ingress
```    

Update controller-service.yaml file with external ip.

```
externalIPs:
- 192.168.xxx.xxx
```

Map ip and hostname in /etc/hosts file.

Edit ingress.yaml file providing the rules details.

Create defined resources:
```
kubectl apply --recursive --filename ./manifests/nginx-ingress
```

Access the service:
```
http://<hostname>/<service_path>
```

Use --publish-service argument in controller-deployment.yaml to display the ip addess in ingress resource.  <br/>
```
--publish-service=$(POD_NAMESPACE)/release-name-nginx-ingress-controller
```

```
kubectl get ingress
NAME      HOSTS         ADDRESS           PORTS   AGE
ingress   <hostname>    192.168.xxx.xxx   80      105s
```
<br/>

[NGINX Ingress Controller user guide](https://kubernetes.github.io/ingress-nginx/user-guide/cli-arguments/ "Command line arguments")
