# Backstage - Guia rapida

Este directorio agrupa la plataforma de Backstage, su catalogo de entidades y plantillas.

## Carpetas

- `catalog/`: entidades del catalogo de software.
  - `apis/`: definiciones de APIs (por ejemplo, servicios gRPC).
  - `components/`: componentes por servicio/frontend y su documentacion (`catalog-info.yaml`, `mkdocs.yml`, `docs/`).
  - `org/`: estructura organizacional (equipos/grupos).
  - `resources/`: recursos de infraestructura (namespaces, Redis, etc.).
  - `systems/`: definicion de sistemas de negocio/plataforma.
- `demo-idp/`: aplicacion principal de Backstage para este proyecto.
  - `packages/app`: frontend.
  - `packages/backend`: backend.
  - `plugins/`: plugins locales (frontend/backend).
  - `software-template/`: plantillas internas usadas por la aplicacion `demo-idp` durante su build/ejecucion (alcance local de esta instancia).
- `software-template/`: plantillas compartidas a nivel `backstage/` (por ejemplo, `java-service-golden-path`) para reutilizarlas desde fuera de `demo-idp`.

### Diferencia entre ambas carpetas `software-template/`

- `backstage/demo-idp/software-template/`:
  - Uso: contenido acoplado a la app `demo-idp`.
  - Alcance: local a esa instancia de Backstage.
  - Cuando usarla: si la plantilla depende de configuracion interna de `demo-idp` o solo se va a mantener/versionar junto con esa app.
- `backstage/software-template/`:
  - Uso: plantillas base compartidas del repositorio.
  - Alcance: comun para otros entornos/proyectos que consuman el contenido de `backstage/`.
  - Cuando usarla: si quieres un golden path reutilizable y desacoplado de una unica instancia de Backstage.

Regla practica:
- Si la plantilla es especifica de la instancia `demo-idp`, dejala en `demo-idp/software-template/`.
- Si la plantilla es reutilizable por varios equipos o despliegues, dejala en `backstage/software-template/`.

## Como ejecutar Backstage en local

> Basado en la configuracion actual (`backstage/demo-idp/package.json` y `app-config.yaml`).

### Prerrequisitos

- Node.js `22` o `24`.
- Corepack habilitado para usar la version de Yarn del proyecto (`yarn@4.4.1`).

### Pasos

```bash
cd backstage/demo-idp
corepack enable
yarn --version
yarn install
yarn start
```

Con esto se levantan frontend y backend en modo desarrollo. Por defecto:

- Frontend: `http://localhost:7007`
- Backend API: `http://localhost:7007/api`

## Scripts utiles (`demo-idp/package.json`)

- `yarn start`: desarrollo local.
- `yarn build:backend`: build del backend.
- `yarn build:all`: build completo.
- `yarn test`: tests del monorepo.
- `yarn lint` / `yarn fix`: calidad de codigo.

## Dockerfiles en `demo-idp/`

| Punto                                         | Dockerfile | Dockerfile.v2 |
| --------------------------------------------- | ---------- | ------------- |
| Multi-stage build                             | Si         | Si            |
| Cache BuildKit                                | Si         | Si            |
| Usuario no root                               | Si         | Si            |
| Yarn immutable                                | Si         | Si            |
| Build separado de runtime                     | Si         | Si            |
| Soporte TechDocs local                        | No         | Si            |
| Evita depender de Docker socket para TechDocs | No         | Si            |
| Incluye `software-template`                   | No         | Si            |
| Tamano de imagen                              | Menor      | Mayor         |
| Superficie de ataque                          | Menor      | Mayor         |

Si necesitas TechDocs local dentro de contenedor, usa `Dockerfile.v2`.

