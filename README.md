# App Protect Policy Configuration by SNI routing
The Ingress Controller supports setting different WAF policies by hostnames.

We will define two bespoke WAF signatures and apply each one to hostname Ingress definition (bookinfo and juiceshop respectively)

**Note:** This feature is only available in NGINX Plus. Before moving forward, make sure you have the NGINX Plus Ingress Controller with App Protect already installed and running in your Kubernetes cluster, and a service with an EXTERNAL-IP that exposes the NGINX Ingress Controller.


Apply the juice-shop and bookinfo applications.

```
$ kubectl apply -f bookinfo.yaml
$ kubectl apply -f juice-shop.yaml

```


```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: nginx-config
  namespace: nginx-ingress
data:
  resolver-addresses: "10.0.0.10"
```

Additional resolver parameters, including the caching of DNS records, are available. Check the corresponding [ConfigMap](https://docs.nginx.com/nginx-ingress-controller/configuration/global-configuration/configmap-resource/) section.


## Example
In the following yaml file we define an ExternalName service with the name my-service:

```yaml
kind: Service
apiVersion: v1
metadata:
  name: my-service
spec:
  type: ExternalName
  externalName: my.service.example.com
```

In the following Ingress resource we use my-service:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    kubernetes.io/ingress.class: "nginx"
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-service
            port:
              number: 80

```

As a result, NGINX Plus will route requests for “example.com” to the IP addresses behind the DNS name my.service.example.com.
