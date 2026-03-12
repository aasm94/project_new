# Estándar de Diseño de Arquitectura SQL

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Cualquier base de datos relacional (PostgreSQL, MySQL, SQL Server, Oracle).
**Ubicación sugerida**: `docs/database/` o `docs/architecture/`

---

## 1. Propósito

Definir lineamientos estandarizados y agnósticos al motor para el diseño de esquemas, nomenclatura de tablas, definición de índices, integridad relacional y gestión de migraciones para asegurar el rendimiento, consistencia estructural y mantenibilidad de la base de datos a futuro.

---

## 2. Convenciones de Nomenclatura

- **Tablas**: Usar nombres en `snake_case` y en **plural** (ej. `users`, `invoices`, `product_categories`).
- **Columnas**: Usar nombres en `snake_case` y en **singular** (ej. `email`, `created_at`, `total_amount`).
- **Llave Primaria (PK)**: Usar siempre la palabra `id` para la llave principal artificial de la tabla.
- **Llaves Foráneas (FK)**: Referenciar la tabla en singular + `_id` (ej. `user_id`, `invoice_id`).
- **Fechas y Tiempos**: 
  - Usar sufijos `_at` para timestamps (ej. `created_at`, `updated_at`, `deleted_at`).
  - Usar sufijos `_date` para campos de tipo DATE sin tiempo (ej. `birth_date`, `expiration_date`).

---

## 3. Integridad y Relaciones (Constraints)

El diseño debe garantizar siempre la integridad referencial y de dominio.

- NOT NULL: Todo campo que sea de captura obligatoria debe tener restricción `NOT NULL`.
- UNIQUE: Usar para prevenir duplicados lógicos (ej. `email` en la tabla `users`).
- DEFAULT: Utilizar valores por defecto en bases de datos para simplificar la capa de aplicación (ej. `is_active DEFAULT true`, `created_at DEFAULT CURRENT_TIMESTAMP`).
- FOREIGN KEY: Siempre deben existir físicamente en el esquema.
  - Definir las acciones `ON DELETE` o `ON UPDATE` de manera explícita (ej. `RESTRICT`, `CASCADE`, `SET NULL`). Jamás usar `CASCADE` si implica la pérdida en cascada de datos importantes del core business, en estos casos usar borrado lógico.

---

## 4. Tipado de Datos y Normalización

- Hasta donde sea razonable, intentar llegar a la **Tercera Forma Normal (3NF)** para evitar anomalías en inserción y actualización antes de pensar en desnormalizar por motivos de rendimiento.
- IDs artificiales: Preferir UUIDs (v4 o v7) si se trata de sistemas distribuidos; u optimizar usando `BIGINT`/`SERIAL` si es un monolito centralizado con necesidad de máximo rendimiento de joins.
- Almacenar cantidades monetarias en campos enteros como centavos (ej. 1000 = $10.00) o usar tipos definidos `DECIMAL/NUMERIC` especificando precisión exacta. NUNCA usar `FLOAT` o reales de precisión flotante.
- Documentos complejos: Usar JSONB / JSON de manera controlada y justificada para atributos dinámicos, NUNCA usarlo para reemplazar relaciones estructuradas o Foreign Keys.

---

## 5. Índices y Rendimiento

Todo lo que se busca o se cruza debe considerar índices.

1. **Índices de Llaves Foráneas**: Toda columna FK que sea usada en cláusulas JOIN debe tener su propio índice.
2. **Íncides de Filtrado**: Aquellas columnas comúnmente usadas en `WHERE` (ej. `status`, `tenant_id`, `created_at`).
3. **Paginación y Orden**: Las columnas sobre las que se hace frecuentemente un `ORDER BY` y `LIMIT`.
4. **Índices Únicos**: Columnas que tienen garantía algorítmica de unicidad.

*Cuidado*: No sobre-indexar tablas que tienen un fuerte volumen de inserciones o actualizaciones, ya que degrada el rendimiento de la escritura.

---

## 6. Migraciones de Base de Datos

El diseño debe poder evolucionar. Todas las actualizaciones del esquema deben ser:
1. **Controladas por código**: Usando herramientas DDL versionadas (ej. Flyway, Alembic, Liquibase, Prisma, TypeORM).
2. **Idempotentes**: Deben tener verificaciones estilo `IF NOT EXISTS` donde el motor lo soporte, o deben poder correr múltiples veces fallando o ignorando en caso de que ya estén aplicadas.
3. **Reversibles (Rollback)**: Toda migración `up` (cambio) debería tener, en la medida de lo posible, su correspondiente migración `down` que regrese la BB.DD a su estado original sin romper otros datos pre-existentes de manera irreversible (cuando sea lógico).
