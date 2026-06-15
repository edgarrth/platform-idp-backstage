# emailservice

Servicio que envia correos de confirmacion.

## Relaciones

```txt
emailservice
  -> provides api: emailservice-grpc-api
  -> used by checkoutservice
```

## Manifiesto

```txt
repo-infra/manifests/emailservice.yaml
```
