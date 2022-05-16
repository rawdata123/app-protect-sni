# App Protect Policy Configuration by SNI Routing

## Overview

The Ingress Controller supports WAF policies by SNI (Server Name Indication).
In this repository, we will set two different WAF signatures to two applications running in the cluster separated by hostname.
  

## Requirements

NGINX App Protect WAF is only available with NGINX Plus. Before moving forward, make sure you have the NGINX Plus Ingress Controller with App Protect already installed and running in your Kubernetes cluster. You can get started with a [30 day free trial](https://www.nginx.com/free-trial-request-nginx-ingress-controller/) and following the [documentation](https://docs.nginx.com/nginx-ingress-controller/installation/installation-with-manifests/) for installation steps.

## Deployment 

1. Deploy a secret and juice-shop application

``` $ kubectl apply -f secret.yaml
    $ kubectl apply -f juice-shop.yaml
```

to test, this SQL injection commmand will extract the data from the juice-shop database:

```curl -s --insecure "https://owasp-juiceshop.example.com/rest/products/search?q=qwert%27))%20UNION%20SELECT%20sql,%20%272%27,%20%273%27,%20%274%27,%20%275%27,%20%276%27,%20%277%27,%20%278%27,%20%279%27%20FROM%20sqlite_master--" | python -m json.tool```

Expected reponse: 
```json
{
    "data": [
        {
            "createdAt": "7",
            "deletedAt": "9",
            "deluxePrice": "5",
            "description": "3",
            "id": null,
            "image": "6",
            "name": "2",
            "price": "4",
            "updatedAt": "8"
        },
        {
            "createdAt": "7",
            "deletedAt": "9",
            "deluxePrice": "5",
            "description": "3",
            "id": "CREATE TABLE `Addresses` (`UserId` INTEGER REFERENCES `Users` (`id`) ON DELETE NO ACTION ON UPDATE CASCADE, `id` INTEGER PRIMARY KEY AUTOINCREMENT, `fullName` VARCHAR(255), `mobileNum` INTEGER, `zipCode` VARCHAR(255), `streetAddress` VARCHAR(255), `city` VARCHAR(255), `state` VARCHAR(255), `country` VARCHAR(255), `createdAt` DATETIME NOT NULL, `updatedAt` DATETIME NOT NULL)",
            "image": "6",
            "name": "2",
            "price": "4",
            "updatedAt": "8"
        },
        {
            "createdAt": "7",
            "deletedAt": "9",
            "deluxePrice": "5",
            "description": "3",
            "id": "CREATE TABLE `BasketItems` (`ProductId` INTEGER REFERENCES `Products` (`id`) ON DELETE CASCADE ON UPDATE CASCADE, `BasketId` INTEGER REFERENCES `Baskets` (`id`) ON DELETE CASCADE ON UPDATE CASCADE, `id` INTEGER PRIMARY KEY AUTOINCREMENT, `quantity` INTEGER, `createdAt` DATETIME NOT NULL, `updatedAt` DATETIME NOT NULL, UNIQUE (`ProductId`, `BasketId`))",
            "image": "6",
            "name": "2",
            "price": "4",
            "updatedAt": "8"
            ....
```

2. Now apply web app protection for the juice-shop deployment. 

```
$ kubectl apply -f app-protect/juice-shop/ap-banana-uds.yaml
$ kubectl apply -f app-protect/juice-shop/ap-dataguard-alarm-policy.yaml
$ kubectl apply -f app-protect/juice-shop/ap-logconf.yaml
$ kubectl apply -f app-protect/juice-shop/waf.yaml
$ kubectl apply -f app-protect/juice-shop/juice-shop-vs.yaml
$ kubectl apply -f app-protect/juice-shop/syslog.pod
```

to test, let's run the same SQL injection command again

```curl -s --insecure "https://owasp-juiceshop.example.com/rest/products/search?q=qwert%27))%20UNION%20SELECT%20sql,%20%272%27,%20%273%27,%20%274%27,%20%275%27,%20%276%27,%20%277%27,%20%278%27,%20%279%27%20FROM%20sqlite_master--"```

Expected response:

```html
<html><head><title>Request Rejected</title></head><body>The requested URL was rejected. Please consult with your administrator.<br><br>Your support ID is: 17209185635870199087<br><br><a href='javascript:history.back();'>[Go Back]</a></body></html>
```

You can also test that the bespoke signature (ap-banana) for juice-shop is in effect.

```curl -iX POST --insecure -d 'banana' https://owasp-juiceshop.example.com```

Expected response:

```html
<html><head><title>Request Rejected</title></head><body>The requested URL was rejected. Please consult with your administrator.<br><br>Your support ID is: 3480987504780435337<br><br><a href='javascript:history.back();'>[Go Back]</a></body></html>
```

3. Now deploy the bookinfo application and add web app protection.

```
$ kubectl apply -f bookinfo.yaml
$ kubectl apply -f app-protect/bookinfo/ap-apple-uds.yaml
$ kubectl apply -f app-protect/bookinfo/ap-dataguard-alarm-policy.yaml
$ kubectl apply -f app-protect/bookinfo/ap-logconf.yaml
$ kubectl apply -f app-protect/bookinfo/waf.yaml
$ kubectl apply -f app-protect/bookinfo/bookinfo-vs.yaml
```

To test we can post data with content 'apple', and the App Protect signature should reject it.

```curl -iX POST --insecure -d 'apple' https://bookinfo.example.com```

Expected response: 

```html
<html><head><title>Request Rejected</title></head><body>The requested URL was rejected. Please consult with your administrator.<br><br>Your support ID is: 3480987504780423607<br><br><a href='javascript:history.back();'>[Go Back]</a></body></html>
``` 

You can view the logs of rejected requests from the syslog deployment

```
$ kubectl exec -it <name-of-syslog-pod> -- cat /var/log/messages
```

For a full walk through of the demo please see the [webinar](https://www.nginx.com/resources/webinars/secure-your-kubernetes-apps-from-attacks-with-nginx/)


## Author information

[Amir Rawdat](https://github.com/rawdata123)
