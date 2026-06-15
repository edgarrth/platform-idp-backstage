# productcatalogservice

Servicio que entrega informacion de productos.

## Relaciones

```txt
productcatalogservice
  -> provides api: productcatalogservice-grpc-api
  -> used by frontend, checkoutservice and recommendationservice
```

## Manifiesto

```txt
repo-infra/manifests/productcatalogservice.yaml
```
