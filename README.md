## Kubernetes-Ingress-Tutorial-for-Beginners-simply-explained-Kubernetes-Tutorial-22


Today

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

