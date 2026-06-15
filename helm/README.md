# Helm - Backstage y Argo

Esta carpeta centraliza configuraciones Helm y manifiestos de soporte para desplegar el stack de plataforma en Kubernetes.

## Contenido

- `backstage-values.yaml`
  - Archivo principal de valores para el chart de Backstage (version mas reciente del proyecto).
  - Incluye configuracion de aplicacion, integraciones y parametros de despliegue.
- `backstage-values-v1.yaml`
  - Version anterior/simplificada de valores para Backstage.
  - Util para comparar cambios o hacer rollback controlado.
- `configmap.yaml`
  - ConfigMap con variables de runtime no sensibles para Backstage.
- `secret.yaml`
  - Secret con credenciales/variables sensibles usadas por Backstage.
- `argo/argocd-values.yaml`
  - Valores de instalacion de Argo CD.
- `argo/argo-rollouts-values.yaml`
  - Valores de instalacion de Argo Rollouts.

## Usos principales

- Desplegar/actualizar Backstage con Helm.
- Separar configuracion no sensible (`ConfigMap`) de secretos (`Secret`).
- Instalar o ajustar componentes GitOps (`Argo CD`) y despliegues progresivos (`Argo Rollouts`).
- Mantener dos perfiles de values para Backstage (`v1` y actual) segun entorno/estrategia.

## Prerrequisitos

- Kubernetes accesible con `kubectl`.
- Helm 3 instalado.
- Permisos para crear recursos en namespaces objetivo.

## Flujo recomendado

1. Aplicar configuracion base (`ConfigMap` y `Secret`).
2. Instalar/actualizar Backstage con el values deseado.
3. Instalar/actualizar Argo CD y Argo Rollouts (si aplica en tu entorno).
4. Verificar releases y estado de pods.

## Comandos de referencia

> Ajusta nombres de release/namespace si tu entorno usa otros.

### 1) Configuracion base

```bash
cd helm
kubectl apply -f configmap.yaml
kubectl apply -f secret.yaml
```

### 2) Instalar/actualizar Backstage

```bash
helm repo add backstage https://backstage.io/charts
helm repo update

helm upgrade --install backstage backstage/backstage \
  -f backstage-values.yaml \
  -n backstage \
  --create-namespace
```

### 3) Usar values v1 (rollback o comparativa)

```bash
helm upgrade --install backstage backstage/backstage \
  -f backstage-values-v1.yaml \
  -n backstage \
  --create-namespace
```

### 4) Instalar/actualizar Argo CD

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update

helm upgrade --install argocd argo/argo-cd \
  -f argo/argocd-values.yaml \
  -n argocd \
  --create-namespace
```

### 5) Instalar/actualizar Argo Rollouts

```bash
helm upgrade --install argo-rollouts argo/argo-rollouts \
  -f argo/argo-rollouts-values.yaml \
  -n argo-rollouts \
  --create-namespace
```

### 6) Verificacion

```bash
helm list -A
kubectl get pods -n backstage
kubectl get pods -n argocd
kubectl get pods -n argo-rollouts
```

## Notas

- Evita versionar credenciales reales en `secret.yaml` para entornos productivos.
- Para cambios frecuentes de configuracion, prioriza editar `backstage-values.yaml` y aplicar `helm upgrade`.
- Si necesitas diagnostico rapido, revisa eventos y logs del namespace correspondiente.

