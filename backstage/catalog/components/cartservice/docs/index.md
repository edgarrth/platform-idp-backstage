# cartservice

Servicio que administra el carrito de compras.

## Relaciones

```txt
cartservice
  -> provides api: cartservice-grpc-api
  -> depends on resource: redis-cart
  -> used by frontend and checkoutservice
```

`redis-cart` esta registrado como `Resource`, no como `Component`.
En Backstage se visualiza filtrando por kind `Resource` o desde la relacion
`dependsOn` de `cartservice`.

## Manifiesto

```txt
repo-infra/manifests/cartservice.yaml
repo-infra/manifests/redis-cart.yaml
```
