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
$ kubectl apply -f app-protect/ap-banana-uds.yaml
$ kubectl apply -f app-protect/ap-dataguard-alarm-policy.yaml
$ kubectl apply -f app-protect/ap-logconf.yaml
$ kubectl apply -f app-protect/waf.yaml
$ kubectl apply -f app-protect/juice-shop-vs.yaml

For a full walk through of the demo please see the [webinar] (#https://www.nginx.com/resources/webinars/secure-your-kubernetes-apps-from-attacks-with-nginx/)

