# redis-cart

Redis usado por `cartservice` para almacenar el carrito de compras.

## Catalog

```txt
kind: Resource
type: redis
system: online-boutique
owner: team-platform
```

## Relaciones

```txt
cartservice
  -> depends on resource: redis-cart

redis-cart
  -> dependency of component: cartservice
```

## Manifiesto

```txt
repo-infra/manifests/redis-cart.yaml
```

## Nota

`redis-cart` se modela como `Resource`, no como `Component`, porque representa
infraestructura consumida por un servicio.
