# Estándar de Diseño de Base de Datos NoSQL

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Bases Orientadas a Documentos (MongoDB, Couchbase), Documentales Cloud (Firestore/Datastore, DynamoDB), o similares.
**Ubicación sugerida**: `docs/database/`

---

## 1. Propósito

Definir los lineamientos para el diseño de esquemas en bases de datos orientadas a documentos (NoSQL). El modelado NoSQL a diferencia de SQL, se prioriza de acuerdo a los patrones de acceso (cómo leeremos los datos) más que a la redundancia de los mismos.

---

## 2. Paradigma NoSQL y Modelado Dirigido por Consultas

En entornos NoSQL, **los datos se modelan para responder a las preguntas de la aplicación**.

Antes de diseñar colecciones y documentos, se DEBE contar con la siguiente información:
1. ¿Cuáles son los queries más frecuentes?
2. ¿Cuál será el query crítico para el rendimiento del negocio?
3. ¿Cuál es el ratio de Lectura / Escritura de la entidad en cuestión?

---

## 3. Nomenclatura General

- **Bases y Colecciones**: Usar `snake_case` o `camelCase` pero ser consistentes durante todo el negocio. Preferible: Colecciones en **plural** (ej. `user_profiles`, `analytics_events`).
- **Campos del documento (Llaves)**: Usar convenciones dependientes de la capa de aplicación, comúnmente `camelCase` (ej. `createdAt`, `phoneNumber`).
- **Identificadores (Keys)**: Usar nombres como `_id`, `id` o particiones semánticas (`userId`).

---

## 4. Tipos de Relaciones: Embeber vs. Referenciar

La decisión más crítica en arquitecturas NoSQL orientadas a documentos suele ser cómo se manejan entidades dependientes.

| Técnica | Escenario de Uso Ideal | Desventaja |
|---|---|---|
| **Referenciar** (Normalización NoSQL) | Relaciones "Muchos a Muchos", O la data embebida crecería ilimitadamente (> 16MB), o la sub-entidad se visualiza/actualiza independientemente. Ejemplo: Referenciar `sellerId` en la colección `products`. | Requiere *Application-Level Joins* (Hacer 2 peticiones a la DB). |
| **Embeber** (Desnormalización NoSQL) | Relaciones "1 a 1" o "1 a Pocos" donde la data subyacente SIEMPRE se presenta junta, y el array hijo tiene un crecimiento acotado. Ejemplo: Embeber array de `addresses` dentro del documento `user`. | Las actualizaciones a la información redundante pueden ser costosas. Límite de tamaño por documento. |

### Regla General:
*Los datos a los que se accede juntos deben almacenarse juntos.*

---

## 5. Diseño de Claves y Particionado (Sharding y Claves Híbridas)

Especialmente importante para DynamoDB o entornos distribuidos horizontalmente.

### 5.1 Single Table Design (Patrón Opcional Avanzado)
Si existen restricciones fuertes en los costos o el rendimiento total, se emplean menos contenedores/tablas, y se segmentan mediante llaves de partición (PK) genéricas y Llaves de Ordenamiento (SK). Ejemplo (DynamoDB): 
- **PK**: `USER#123` 
- **SK**: `PROFILE#123` o `METADATA#456`

### 5.2 Estructura de Llaves Semánticas
Las llaves en bases distribuidas deben ayudar a definir acceso e integridad.
- **Prefixing**: Es buena práctica usar un prefijo para clarificar tipos de datos (`usr_9f45x`, `ord_78112`).

---

## 6. Manejo del Tiempo e Historial

NoSQL es excelente para grandes volúmenes y tiempos flexibles:
- Utilizar formato `ISO-8601` en string o `Epoch` timestamps genéricos en vez de tipos Time nativos rígidos para garantizar portabilidad cruzada entre motores o frameworks.

---

## 7. Versionado de Documentos e Integridad

Dado que las BD NoSQL carecen de *esquemas fuertes*, la validación debe recaer en otro lado:
1. **Validación a Nivel DB**: Activar validadores JSON Schema si el motor lo soporta (ej. JSON Schema Validator de MongoDB).
2. **Versionado de Esquema**: Todo documento deberia integrar idealmente un campo de `schemaVersion` interno (ej. `v: 2`) que le permita a la app de código transicionar información vieja en una migración lenta subyacente sin caer en el caos.
3. **Constraints por APP**: Todo el tipado (`String`, `Boolean`) debe ser asegurado primero mediante la capa modelo (ej. usando de Mongoose, Pydantic, Zod).
