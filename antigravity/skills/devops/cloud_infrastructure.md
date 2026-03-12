# Estándar de Infraestructura en la Nube (Cloud)

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Cualquier orquestación de recursos de nube y DevOps sobre proveedores públicos (AWS, GCP, Azure, DigitalOcean).
**Ubicación sugerida**: `docs/infra/` o dentro del repositorio de Infrastructure as Code (IaC).

---

## 1. Propósito

Establecer lineamientos comunes sobre cómo provisionar, nombrar, gestionar, asegurar e implementar recursos en un entorno Cloud que sea mantenible por múltiples manos sin perder control sobre costos y seguridad.

---

## 2. Infrastructure as Code (IaC)

Toda infraestructura permanente debe poder recrearse sin necesidad de entrar a la consola manual UI del proveedor (Click-Ops).

- **Herramientas**: Usar preferentemente plataformas como Terraform, AWS CloudFormation / CDK, Pulumi.
- **Flujo CI/CD**: Los cambios en recursos de nube deben revisarse a través de un Pull Request e integrarse automatizadamente.
- **Aislamiento de Cuentas / Proyectos**: Evitar mezclar Ambientes. Una buena práctica dicta tener proyectos independientes (o sub-scripciones) para `desarrollo`, `staging` y `producción`.
- *Excepciones a IaC*: Exploraciones, proofs of concept (PoC) cortas (si duran menos de una semana se permite usar interfaz visual y destruirlas).

---

## 3. Conveción de Etiquetas y Nomenclatura (Tagging)

Todos y cada uno de los recursos instanciados (servidores, redes, buckets, funciones) en los proveedores principales deben obligatoriamente contener etiquetas (Tags) para permitir un monitoreo financiero y de responsabilidad. 

Ejemplo de estructura mínima mandatoria:
- `Env`: `dev`, `stg`, `prod` (Ambiente asignado).
- `Project` o `App`: Nombre de la aplicación (ej. `payments-gateway`).
- `Owner`: Nombre del equipo desarrollador o correo del responsable técnico.

**Nomenclatura (Naming)**
Un recurso general se nombrará con la convención:
`{empresa}-{app}-{ambiente}-{tipo}-{zona}`
Ej. `mycorp-payments-prod-bucket-us-east-1`

---

## 4. Gestión de IAM y Seguridad Mínima Privilege

La principal vulnerabilidad de seguridad es siempre un exceso de permisos otorgados en los roles operacionales.

1. **Principio del Mínimo Privilegio (PoLP)**: Las aplicaciones de software o los desarrolladores sólo deben recibir permisos exactos de acción requerida, no acceso genérico (NUNCA asignar `AdministratorAccess` ni `FullAccess` a roles de máquinas productivas).
2. **Sin Keys de Larga Duración**: Tratar por todos los medios de no compartir Credenciales de Acceso Key permanentes (ej. AWS Access Keys) que terminen locales o commiteadas si no es estrictamente necesario, en favor de **Roles o Workload Identities**.
3. **IAM Users vs Roles**: Usar OIDC y de preferencia Roles efímeros o vinculados al contenedor/máquina.

---

## 5. Redes (Networking, VPCs) y Exposición

No todo debe ser accesible a Internet público.

- **Servidores Back-end / DB**: Toda base de datos analítica o repositorio relacional central debe residir en Tiers y sub-redes totalmente Privadas sin posibilidad de entrada IPV4 por Internet, utilizando NAT Gateways para salidas si lo requiriese y Bastions / VPNs controlados o Session Managers en caso de necesitar control remoto administrativo.
- **Ingreso Público**: Queda reservado sólo para Application/Network Load Balancers, API Gateways o CDN/Edge Caching (CloudFront, Cloudflare, etc.).

---

## 6. Almacenamiento Estático (Buckets - S3/GCS/Blob)

Lineamientos de vida al utilizar Buckets (S3 de AWS, Storage de GCP):
1. Bloquear siempre totalmente **Public Access Block**. Los repositorios que alberguen imágenes que se van a consumir abiertamente se sirven detrás de un CDN firmado; no a nivel raíz público (con raras excepciones marcadas de Assets PWA estáticos).
2. **Ciclo de vida (Lifecycle Rules)**: Si se guardan archivos temporales, logs diarios de analítica o reportes PDF esporádicos intermedios, usar transiciones de costo, migrando automáticamente recursos viejos (>90 días) a capas de *Glacier* cold-storage y borrando automáticamente (*TTL*) tras X años.
3. No utilizar buckets como una base de datos de registro rápido transaccional, es decir, el Bucket sirve para albergar la persistencia Binary Large, un servicio alterno (BB.DD) indexará el path subyacente de ubicación en el disco de red y su lógica relacional atada al negocio.
