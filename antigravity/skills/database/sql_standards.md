# Estándar Crítico de SQL Arquitectura

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Diseño estricto de cualquier base de datos Relacional (MySQL, PostgreSQL, MariaDB).
**Ubicación sugerida**: `docs/database/`

---

## 1. Propósito

Establecer un convenio de esquemas limpio y predecible. Prevenir el caos cuando la base de datos crece por años sin reglas fundacionales, garantizando nombres consistentes y relaciones blindadas.

---

## 2. Nomenclatura en Plural (Tablas)

Toda tabla que contenga registros debe nombrarse en **Snake_Case y PLURAL**.
- Reflejan una colección poblada de elementos de la vida real.
- *Mal*: `user`, `invoice`, `customerPayment`
- *Bien*: `users`, `invoices`, `customer_payments`

*Excepción*: Tablas Pivote / Intersection que se basan en unir dos tablas para relación Mucho-A-Mucho, donde suelen fusionarse en formato alfabético: `role_user` o `invoice_product`.

---

## 3. Convenciones de Columnas y Llaves

Las columnas se escriben en **Snake_Case Y SINGULAR**.

### 3.1 Llaves Primarias (PK)
Toda tabla (sin excepción de relación M-t-M explícita) requiere una llave primaria numérica, Serial o de texto único UUID.
- Esta llave abstracta siempre debe nombrarse **simplemente como `id`**.
- *Mal*: `user_id` en la tabla users, o `UserId`, o `ID`.

### 3.2 Llaves Foráneas (FK)
Las columnas que sirven como relación o Foreign Key DEBEN llamarse exactamente como el singular de la tabla referenciada sumada al sufijo `_id`.
- *Referencia a tabla `users`* -> El campo se llamará obligatoriamente `user_id`.
- *Referencia a tabla `categories`* -> El campo se llamará `category_id`.

Las relaciones físicas Constraint obligatorias deben existir dentro del Schema real, indicando las reglas `ON UPDATE` y `ON DELETE` correctas (ej. `ON DELETE RESTRICT` ó `ON DELETE CASCADE` solo si borrar al padre lógicamente amerita purgar a los hijos invisibles de por vida).

---

## 4. Timestamps de Auditoría (Uso Obligatorio)

CUALQUIER tabla principal del sistema en producción (B2C o B2B), debe contener absolutamente siempre por defecto columnas de auditoría temporal (Timestamps).

1. `created_at` (o `creation_date`): Tipo de dato TIMESTAMP / DATETIME.
2. `updated_at` (o `modified_date`): Cuyo Engine relacional se autogestione con cada modificación (`ON UPDATE CURRENT_TIMESTAMP`).

### Soft Deletes (Borrado Lógico)
- Evitar hacer peticiones destructivas (`DELETE FROM users`) en objetos operacionales del negocio (facturas, ventas, empleados).
- Usar en su lugar la columna TIMESTAMP `deleted_at`. Si es Nula, el usuario existe. Si tiene fecha, el sistema tratará al registro como fantasma sin alterar los joins històricos de las facturas que ese empleado generó en vida.
