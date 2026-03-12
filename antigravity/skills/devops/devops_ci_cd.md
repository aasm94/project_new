# Estándar de DevOps y CI/CD Pipelines

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Cualquier plataforma de Integración y Despliegue Continuo (GitHub Actions, GitLab CI, CircleCI, Bitbucket Pipelines) y flujos Dockerizados.
**Ubicación sugerida**: `docs/infra/` o dentro de cada `.github/workflows/`.

---

## 1. Propósito

Garantizar que no existan interferencias humanas en los despliegues ("No despliegues desde tu laptop los viernes"). Todas las versiones llevadas hacia Producción son empaquetadas, probadas y distribuidas mediante la misma máquina predecible para prevenir el síndrome "funciona en mi máquina local".

---

## 2. Docker: El Contenedor Base

Para lograr un sistema agnóstico, toda aplicación Backend o servicio web debe estar Dockerizada y su construcción configurada explícitamente a través del `Dockerfile`.

- **Multi-stage Builds (Etapas)**: Separar siempre la fase de "Construcción" (Build) de la fase "Ejecución" (Runner). Así no arrastramos dependencias de desarrollo (`devDependencies`, librerías pesadas o llaves SSH falsas) a la imagen de producción.
- **Tamaño de Imagen**: Siempre basar los contenedores productivos en imágenes Mínimas (Ej. `node:20-alpine`, `python:3.11-slim`, `distroless`).
- **Usuario no-Root**: La app nunca debe ejecutarse utilizando el usuario `root` dentro de Docker por seguridad. Definir y cambiar al usuario de nivel menor (USER node) al final del Dockerfile.

---

## 3. Integración Continua (CI Pipeline)

La canalización de Integración se dispara en cada Push a una rama de tipo Feature/Bugfix, o al abrir un Pull Request hacia `main`. No despliega nada, **Solo aprueba**.

**Los 4 Pasos Obligatorios de un Pipe de CI**:
1. **Linter y Formato**: Fallar automáticamente la validación si el estilo (ESLint, Prettier, Black, Ruff) no coincide con las reglas base.
2. **Pruebas Unitarias o Integradas**: Correr toda la suite inyectando en memoria las bases que requiera usando "Servicios" o "Containers" temporales del pipeline.
3. **Análisis de Seguridad (SAST)**: Correr escaners de forma (Dependabot, Snyk, Gitleaks) para asegurar que no hay dependencias peligrosas o JWTs y Passwords quemados en el código accidentalmente.
4. **Construcción y Etiqueta (Si Merge)**: Generar el binario final o compilar la imagen de Docker, y asignarle un tag unívoco correspondiente al Git Commit SHA corto o SemVer.

---

## 4. Despliegue Continuo (CD Pipeline)

Se dispara únicamente cuando el código ha sido acoplado en las ramas de Entorno (ej. Merged to `main` o al realizar un Tag Release `v1.2.0`).

- **Push al Registro (Registry)**: La imagen Docker testeada en el CI previo se empuja firmemente a ECR/ACR/GCR o DockerHub.
- **Rollout Orquestado**: Notificar al sistema orquestador (Kubernetes / AWS ECS/ EKS, AppRunner o Vercel) el reemplazo del servicio progresivamente (Rolling update, Blue-Green Deployment).
- **Health Check Fuerte**: Jamás cambiar o matar la versión vieja si la nueva no responde positivamente a los escúteos de red (`/health-check`).
- **Notificación y Limpieza**: Alertar a Slack o Teams sobre el éxito o fracaso catastrófico de la operación. 

---

## 5. Secretos y Variables de Entorno del Pipeline

- NUNCA almacenar `.env.production` en el repositorio, ni mucho menos llaves privadas.
- Utilizar el Secret Manager del proveedor CI (ej. GitHub Secrets) de forma jerárquica por "Entorno" e inyectarlos localmente solo durante la fase de ejecución del Job (o proveérselo instanciado directo al servicio final AWS).
