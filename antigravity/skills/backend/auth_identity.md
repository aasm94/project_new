# Estándar de Autenticación, Identidad y Seguridad

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Implementación de Login, Registro, MFA y autorización (RBAC) en Web, API y Móvil.
**Ubicación sugerida**: `docs/security/`

---

## 1. Propósito

Evitar la "reinvención de la rueda" creando sistemas de autenticación caseros inseguros, promoviendo el uso de estándares probados de la industria para JWT, manejo seguro de sesiones y OAuth 2.0.

---

## 2. Almacenamiento de Contraseñas

**Bajo ninguna circunstancia las contraseñas viajarán en texto plano al almacenamiento, ni por logs**.
- Usar funciones de hash de retardo (Key Derivation Functions) modernas y aprobadas por OWASP: **Argon2**, **Bcrypt**, o **Scrypt**.
- Nunca usar algoritmos rápidos obsoletos (MD5, SHA1 o incluso SHA256 básicos sin salt fuerte).
- El "Salt" debe ser único por usuario.

---

## 3. JWT (Json Web Tokens) vs Sesiones Stateful

### 3.1 APIs y Microservicios (JWT Stateless)
- Usar JWT si el cliente es una app separada (SPA, Móvil).
- **Corto tiempo de vida (TTL)**: Un Access Token debe vivir poco tiempo (15 - 60 minutos como máximo).
- **Refresco de Tokens**: Implementar obligatoriamente un sistema de `Refresh Token` de mayor vida (días/meses), el cual puede ser *revocado* internamente en base de datos al cambiar contraseña o desloguear dispositivos.

### 3.2 Almacenamiento Seguro del Token (Client-Side)
- **Web**: No almacenar el Access Token ni el Refresh Token en `localStorage` (vulnerable a XSS). Enviarlos y manipularlos mediante cookies `HttpOnly`, `Secure` y `SameSite=Strict`.
- **Móvil**: Almacenar los tokens en las bobedas seguras cifradas de los Sistemas Operativos (iOS Keychain, Android Keystore).

---

## 4. OAuth 2.0 y Single Sign-On (SSO)

Al interactuar con Google, Apple, Microsoft o GitHub (Social Logins):
- Usar librerías estandarizadas o wrappers Identity Providers (Auth0, Firebase Auth, Supabase Auth, Keycloak). Intentar no programar el flujo manual a menos que se requiera 100% control de la data.
- Enviar state secrets durante el redireccionamiento para mitigar ataques CSRF.

---

## 5. Prevención de Ataques Comunes (OWASP)

- **Fuerza Bruta al Login**: Bloquear la IP temporalmente tras 5-10 intentos de contraseñas fallidas mediante Throttling / Rate Limiting.
- **Enumeración de Usuarios**: Tanto el endpoint de registro, intento de login fallido o "Recuperación de Contraseña" deben devolver un mensaje genérico. 
  - *Mal*: "Este correo no existe." / "Contraseña inválida para el correo existene."
  - *Bien*: "Si el correo existe en nuestro sistema, hemos enviado un enlace de recuperación." o "Credenciales inválidas." (Protege privacidad).

---

## 6. Autorización y Permisos (RBAC / PBAC)

Autenticar (Quién eres) no es Autorizar (Qué puedes hacer).
- Evitar esparcir validaciones tipo `if (user.role == 'admin')` por todas las pantallas o controladores.
- Usar un esquema RBAC (Role-Based Access Control) con *Guards* o *Middlewares* que verifiquen capacidades (Claims o Policies) antes de dejar entrar a una ruta o modificar un recurso en base de datos específico (Asegurarse que un usuario solo pueda editar su propia factura, no solo estar logueado).
