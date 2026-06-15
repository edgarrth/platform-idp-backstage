# checkoutservice

Servicio que coordina el proceso de compra.

## Relaciones

```txt
checkoutservice
  -> provides api: checkoutservice-grpc-api
  -> consumes productcatalogservice, shippingservice, paymentservice
  -> consumes emailservice, currencyservice and cartservice
  -> used by frontend
```

## Manifiesto

```txt
repo-infra/manifests/checkoutservice.yaml
```
