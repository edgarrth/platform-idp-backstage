# recommendationservice

Servicio que genera recomendaciones de productos.

## Relaciones

```txt
recommendationservice
  -> provides api: recommendationservice-grpc-api
  -> consumes api: productcatalogservice-grpc-api
  -> used by frontend
```

## Manifiesto

```txt
repo-infra/manifests/recommendationservice.yaml
```
