Crear proyecto
---------------
docker run --rm \
-e HOME=/tmp \
-e BACKSTAGE_APP_NAME=demo-idp \
-e HOST_UID="$(id -u)" \
-e HOST_GID="$(id -g)" \
-v "$PWD:/workspace" \
-w /workspace \
node:24-trixie \
bash -lc 'corepack enable && npx -y @backstage/create-app@latest --path demo-idp && chown -R "$HOST_UID:$HOST_GID" demo-idp'

Ejecutar en local
-----------------
corepack enable
yarn --version
yarn install
yarn start

Construir con docker (multistage)
---------------------------------
docker buildx build -f Dockerfile \
--platform linux/arm64 -t wjma90/demo-idp:0.0.1-arm64 \
--cache-from type=registry,ref=wjma90/demo-idp:buildcache-base-arm64 \
--cache-to type=registry,ref=wjma90/demo-idp:buildcache-base-arm64,mode=max --push .

docker buildx build -f Dockerfile \
--platform linux/arm64 -t wjma90/demo-idp:0.0.1-amd64 \
--cache-from type=registry,ref=wjma90/demo-idp:buildcache-base-amd64 \
--cache-to type=registry,ref=wjma90/demo-idp:buildcache-base-amd64,mode=max --push .

docker buildx imagetools create \
  -t wjma90/demo-idp:0.0.1 \
  wjma90/demo-idp:0.0.1-arm64 \
  wjma90/demo-idp:0.0.1-amd64


Desplegar en K8S
-----------------
helm repo add cnpg https://cloudnative-pg.github.io/charts
helm repo add backstage https://backstage.github.io/charts
helm repo update

helm upgrade --install postgres-operator cnpg/cloudnative-pg --namespace operators --create-namespace --version 0.28.2 -f stack/postgresql/cnpg-operator-values.yaml

k create ns backstage

kba create secret generic backstage-postgresql-app \
--type=kubernetes.io/basic-auth \
--from-literal=username=backstage \
--from-literal=password=backstage 

helm upgrade --install backstage-postgresql cnpg/cluster --namespace backstage --create-namespace --version 0.6.0 -f stack/postgresql/cnpg-postgres-values.yaml
helm upgrade --install backstage backstage/backstage --namespace backstage --create-namespace --version 2.7.0 -f stack/backstage/backstage-values-v1.yaml
