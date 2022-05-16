# App Protect Policy Configuration by SNI routing

## Overview

The Ingress Controller supports WAF policies by SNI (Server Name Indication).
In this repository, we will set two different WAF signatures to two applications running in the cluster separated by hostname.
  

## Requirements

NGINX App Protect WAF is only available with NGINX Plus. Before moving forward, make sure you have the NGINX Plus Ingress Controller with App Protect already installed and running in your Kubernetes cluster. You can get started with a [30 day free trial] (#https://www.nginx.com/free-trial-request-nginx-ingress-controller/)

## Deploymemnt 

1. Deploy a secret and juice-shop application

``` $ kubectl apply -f secret.yaml
    $ kubectl apply -f juice-shop.yaml
```

to test:

```curl -s https://owasp-juiceshop.example.com```

Expected reponse: 

```<!--
  ~ Copyright (c) 2014-2022 Bjoern Kimminich & the OWASP Juice Shop contributors.
  ~ SPDX-License-Identifier: MIT
  --><!DOCTYPE html><html lang="en"><head>
  <meta charset="utf-8">
  <title>OWASP Juice Shop</title>
  <meta name="description" content="Probably the most modern and sophisticated insecure web application">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link id="favicon" rel="icon" type="image/x-icon" href="assets/public/favicon_js.ico">
  <link rel="stylesheet" type="text/css" href="//cdnjs.cloudflare.com/ajax/libs/cookieconsent2/3.1.0/cookieconsent.min.css">
  <script src="//cdnjs.cloudflare.com/ajax/libs/cookieconsent2/3.1.0/cookieconsent.min.js"></script>
  <script src="//cdnjs.cloudflare.com/ajax/libs/jquery/2.2.4/jquery.min.js"></script>
  <script>
    window.addEventListener("load", function(){
      window.cookieconsent.initialise({
        "palette": {
          "popup": { "background": "#546e7a", "text": "#ffffff" },
          "button": { "background": "#558b2f", "text": "#ffffff" }
        },
        "theme": "classic",
        "position": "bottom-right",
        "content": { "message": "This website uses fruit cookies to ensure you get the juiciest tracking experience.", "dismiss": "Me want it!", "link": "But me wait!", "href": "https://www.youtube.com/watch?v=9PnbKL3wuH4" }
      })});
  </script>
<style>.bluegrey-lightgreen-theme.mat-app-background{background-color:#303030;color:#fff}@charset "UTF-8";@media screen and (-webkit-min-device-pixel-ratio:0){}</style><link rel="stylesheet" href="styles.css" media="print" onload="this.media='all'"><noscript><link rel="stylesheet" href="styles.css"></noscript></head>
<body class="mat-app-background bluegrey-lightgreen-theme">
  <app-root></app-root>
<script src="runtime.js" type="module"></script><script src="polyfills.js" type="module"></script><script src="vendor.js" type="module"></script><script src="main.js" type="module"></script>

</body></html>```html


For a full walk through of the demo please see the [webinar] (#https://www.nginx.com/resources/webinars/secure-your-kubernetes-apps-from-attacks-with-nginx/)
