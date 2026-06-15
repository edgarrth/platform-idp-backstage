# shippingservice

Servicio que calcula costos de envio y procesa envios.

## Relaciones

```txt
shippingservice
  -> provides api: shippingservice-grpc-api
  -> used by frontend and checkoutservice
```

## Manifiesto

```txt
repo-infra/manifests/shippingservice.yaml
```
