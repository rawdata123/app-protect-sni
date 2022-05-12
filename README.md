# App Protect Policy Configuration by SNI routing
The Ingress Controller supports setting different WAF policies by hostnames.

We will define two bespoke WAF signatures and apply each one to hostname Ingress definition (bookinfo and juiceshop respectively)

**Note:** This feature is only available in NGINX Plus. Before moving forward, make sure you have the NGINX Plus Ingress Controller with App Protect already installed and running in your Kubernetes cluster. You can get started with [30-day free trial] (#https://www.nginx.com/free-trial-request-nginx-ingress-controller/)
For a walk through of the demo please see the [webinar] (#https://www.nginx.com/resources/webinars/secure-your-kubernetes-apps-from-attacks-with-nginx/)
