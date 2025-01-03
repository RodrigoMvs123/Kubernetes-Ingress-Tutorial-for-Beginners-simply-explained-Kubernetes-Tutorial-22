## Kubernetes-Ingress-Tutorial-for-Beginners-simply-explained-Kubernetes-Tutorial-22

- https://www.youtube.com/watch?v=80Ew_fsV4rM 
- https://github.com/RodrigoMvs123/Kubernetes-Ingress-Tutorial-for-Beginners-simply-explained-Kubernetes-Tutorial-22

## Kubernetes Ingress Explained 

What is Ingress ?

Ingress YAML Configuration 

When do you need Ingress ?

Ingress Controller 

#### Kubernetes Cluster 

> Pod ( my-app pod )

Internal 

> SVC ( my-app service )

> ing ( my-app ingress ) // IP Address and Port is not opened 

Example YAML file: External Service 

> Assign external IP address to service 

ExampleYAMfile.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-external-service
spec:
  selector:
    app: my-app
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
      nodePort: 35010
```

#### Example YAML File: Ingress

- rules // Routing Rules

> Forward request to the internal service 

> Income request gets forwarded to internal service 

- host

> Valid domain address 

> Map domain name to Node's IP address, which is the entrypoint 

ExampleYAMLfileIngress.yaml
```yaml 
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata: 
    name: my-app-ingress
spec: 
    rules: 
    - host: myapp.com 
      http: 
        paths:
        - backend: 
            serviceName: my-app-internal-service
            servicePort: 8080 
```

How to **configure Ingress** in your **kubernetes cluster**

Ingress Controller 

> Evaluates all teh rules 

> Manages redirections 

> Entrypoint to cluster 

> Kubernetes Nginx Ingress Controller 

Kubernetes Cluster 

> Pod ( my-app pod )

Internal 

> SVC ( my-app service )

> ing ( my-app ingress ) // IP Address and Port is not opened 

> pod ( Ingress Controller Pod ) // Evaluates and process ingress rules 

#### Ingress Controller in Minikube 

> Install Ingress Controller in Minikube 

Command Prompt ( Terminal )
```
minikube addons enable ingress
> ingress was successfully enable
```

> Automatically starts the kubernetes Nginx implementation of ingress controller 

> Ingress Controller configured with just one command 

Command Prompt ( Terminal )
```
kubectl get pod -n kube-system
> NAME                                       READY   STATUS     RESTARTS    AGE 
  nginx-ingress-controller-6fc5bcc8c9-wd4g9  1/1     Running    0           30h // Pod
```

Create Ingress Rule

Command Prompt ( Terminal )
```
kubectl get ns 
...
> NAME                  STATUS    AGE 
  kubernetes-dashboard  Active    17d // Internal service and Pod already exists 

kubectl get all -n kubernetes-dashboard 
> NAME                                      READY  STATUS    RESTARTS   AGE    
  pod/kubernetes-dashboard-79d9cd965-fvrbt  1/1    Running   0          6d17h

  NAME                           TYPE        CLUSTER-IP     EXTERNAL-IP  PORT(S)
  service/kubernetes-dashboard   ClusterIP   10.96.220.185  <none>       80/TCP
```

Create Ingress Rule ( Configuration )

ExampleYAMLIngressRule.yaml
```yaml 
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata: 
    name: dashboard-ingress
    namespace: kubernetes-dashboard // Namespace = same as service and pod
spec: 
    rules: 
    - host: dashboard.com 
      http: 
        paths:
        - backend: 
            serviceName: kubernetes-dashboard
            servicePort: 80
```

Create Ingress Rule ( Resolve IP address to host name ) 

Command Prompt ( Terminal )
```
kubectl apply -f dashboard-ingress.yaml
> ingress.networking.k8s.io/dashboard-ingress created 

kubectl get ingress -n kubernetes-dashboard 
> NAME                 HOSTS           ADDRESS    PORTS    AGE
  dashboard-ingress    dashboard.com              80       14s

kubectl get ingress -n kubernetes-dashboard --watch
> NAME                 HOSTS           ADDRESS           PORTS    AGE
  dashboard-ingress    dashboard.com   192.168.64.5      80       14s

sudo vim /etc/hosts
> Password: ...
  
  ##
  # Host Database 
  #
  # localhost is used to configure the loopback interface when the system is 
  # booting. Do not change this entry
  ##
  127.0.0.1         localhost
  255.255.255.255   broadcasthost
  : :1              localhost
  192.168.64.5      dashboard.com // IP Address 192.168.64.5 will be mapped to dashboard.com 

  :wq
```

> The request will come to the minikube cluster, than will be headed over to Ingress Controller, Ingress Controller than will go and will evaluate the Ingress Controller Rule, and forward that request to service 

Open in Browser 

- dashboard.com 

```
Kubernetes ( UI Dashboard )
Overview

Namespace       ...

```

#### Ingress Default Backend 

Command Prompt ( Terminal )
```
kubectl describe ingress dashboard-ingress -n kubernetes-dashboard 
> NAME:               dashboard-ingress
  NAMESPACE:          Kubernetes-dashboard
  Address:            192.168.64.5
  Default backend:    default-http-backend:80 (<none>) // Request to k8s cluster default rule
  Rules: 
    Host            Path       Backends
    ----
    dashboard.com 
    ...
```

Open in Browser 

- dashboard.com/anypath ( Default backend rule )

```
404 page not found 

```

#### Configure Default Backend in Ingress

Command Prompt ( Terminal )
```
kubectl describe ingress myapp-ingress 
> Name:             myapp-ingress
  Namespace:        default
  Address:       
  Default backend:  default-http-backend:80 (<none>)
  Rules:
  Host              Path   Backends
  ----              ----   --------
  myapp.com         
                    myapp-internal-service:8080 (<none>)
```

ExampleYAMLfileDefaultBackendIngress.yaml
```yaml 
apiVersion: v1
kind: Service
metadata: 
  name: default-http-backend
spec: 
  selector: 
    app: default-response-app
  ports: 
    - protocol:  TCP
      port:  80
      targetPort:  8080
```

#### Multiple Paths for same Host

- http://myapp.com/analytics
```
            svc
    analytics service

            pod
       analytics pod
```
- http://myapp.com/shopping
```
            svc
     shopping service 

            pod
        shopping pod
```

ExampleYAMlfileMultiplePathsForSameHost.yaml
```yaml
apiVersion: networking.k8s.io/vibeta1
kind: Ingress
metadata: 
  name: simple-fanout-example
  annotations: 
    nginx.ingress.kubernetes.io/rewrite-target: /
spec: 
  rules: 
  - host: myapp.com
    http:
      paths: 
      - path: /analytics
        backend:
          serviceName: analytics-service
          servicePort: 3000
      - path: /shopping
        backend:
          serviceName: shopping-service
          servicePort: 8080
```

#### Multiple Sub-Domains or Domains

Instead of:

- http://myapp.com/anaçytics

- http://analytics.myapp.com
```
            svc
      analytics service 

            pod
        analytics pod
```

ExampleYAMlfileMultiplePathsForMultipleSubDomainsOrDomains.yaml
```yaml
apiVersion: networking.k8s.io/vibeta1
kind: Ingress
metadata: 
  name: name-virtual-host-ingress
spec: 
  rules: 
  - host: analytics.myapp.com
    http:
      paths: 
        backend:
          serviceName: analytics-service
          servicePort: 3000
  - host: shopping.myapp.com
    http:
      paths:
        backend:
          serviceName: shopping-service
          servicePort: 8080
``` 

## Configuring TLS Certificate - https://



ExampleYAMlConfiguringTLSCertificate.yaml
```yaml
apiVersion: networking.k8s.io/vibeta1
kind: Ingress
metadata: 
  name: tls-example-ingress
spec: 
  rules: 
  - host: myapp.com
    http:
      paths: 
        backend:
          serviceName: myapp-internal-service
          servicePort: 8080
```

```  
             ing
```

ExampleYAMlConfiguringTLSCertificateIngress.yaml
```yaml
apiVersion: networking.k8s.io/vibeta1
kind: Ingress
metadata: 
  name: tls-example-ingress
spec: 
  tsl: 
  - hosts: 
    - myapp.com
    secretName: myapp-secret-tls
  rules:
    - host: myapp.com
      http: 
        paths:
        - path:
          backend:
            serviceName: myapp-internal-service
            servicePort: 8080
```

```
            secret
```

1. Data keys need to be **"tls.crt"** and **"tls.key"**
2. Values are **file contents** 
3. Secret component must be in the **same namespace** as the Ingress component  

ExampleYAMlConfiguringTLSCertificateSecret.yaml
```yaml
apiVersion: v1
kind: Secret 
metadata: 
  name: myapp-secret-tls
  namespace: default
data: 
  tls: base64 encoded cert
  tls: base64 encoded key
type: kubernetes.k8s.io/tls
```



