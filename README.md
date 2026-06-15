# Platform IDP Backstage

Repositorio de plataforma para Backstage con:

- aplicacion principal en `backstage/demo-idp/`
- catalogo de entidades en `backstage/catalog/`
- despliegue con Helm en `helm/`
- manifiestos adicionales en `manifests/`

## Como navegar este repo

- Guia de Backstage (estructura interna, local dev, templates): `backstage/README.MD`
- Guia de Helm (Backstage, Argo CD, Argo Rollouts): `helm/README.md`

Este README raiz se enfoca en el uso del `Dockerfile` de la raiz y en la vista general del proyecto.

## Estructura de alto nivel

- `Dockerfile`: build de produccion multistage para la app Backstage.
- `backstage/`: codigo, catalogo y configuracion de la instancia.
- `helm/`: values y manifests auxiliares para despliegue en Kubernetes.
- `manifests/`: manifests Kubernetes complementarios.

## Dockerfile de la raiz: que hace

El `Dockerfile` de la raiz construye una imagen de produccion de Backstage usando 3 etapas:

1. `packages`
   - Copia metadatos de dependencias (`package.json`, `yarn.lock`, `.yarn`, `.yarnrc.yml`) y manifests de `packages/app` y `packages/backend`.
   - Optimiza cache de dependencias antes de copiar todo el codigo.
2. `build`
   - Instala toolchain de compilacion (python, g++, build-essential, sqlite headers).
   - Ejecuta `yarn install --immutable`, `yarn tsc` y build del backend.
   - Descomprime artefactos `skeleton.tar.gz` y `bundle.tar.gz`.
3. `runtime`
   - Copia solo artefactos necesarios para ejecucion.
   - Ejecuta `yarn workspaces focus --all --production` para reducir dependencias.
   - Corre como usuario no-root (`node`) y expone `7007`.

Comando de arranque dentro del contenedor:

```bash
node packages/backend --config app-config.yaml
```

## Como usar el Dockerfile de la raiz

Importante: el build context correcto para este `Dockerfile` es `backstage/demo-idp`.

### Build local rapido

```bash
docker build -f Dockerfile -t backstage:local backstage/demo-idp
```

### Build con buildx (recomendado)

```bash
docker buildx build -f Dockerfile \
  --platform linux/amd64 \
  -t backstage:local \
  --load \
  backstage/demo-idp
```

### Ejecutar la imagen

```bash
docker run --rm -p 7007:7007 backstage:local
```

Luego abre:

- `http://localhost:7007`

## Publicacion multi-arquitectura (ejemplo)

```bash
Construir con docker (multistage)
docker buildx build -f Dockerfile
--platform linux/arm64 -t edgarrth/demo-idp:0.0.1-arm64
--cache-from type=registry,ref=edgarrth/demo-idp:buildcache-base-arm64
--cache-to type=registry,ref=edgarrth/demo-idp:buildcache-base-arm64,mode=max --push .

docker buildx build -f Dockerfile
--platform linux/arm64 -t edgarrth/demo-idp:0.0.1-amd64
--cache-from type=registry,ref=edgarrth/demo-idp:buildcache-base-amd64
--cache-to type=registry,ref=edgarrth/demo-idp:buildcache-base-amd64,mode=max --push .

docker buildx imagetools create
-t edgarrth/demo-idp:0.0.1
edgarrth/demo-idp:0.0.1-arm64
edgarrth/demo-idp:0.0.1-amd64
```

## Despliegue en Kubernetes

Para evitar duplicar instrucciones, la guia operativa de despliegue esta en `helm/README.md`.
