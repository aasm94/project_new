# Estándar de Integración de Pasarelas de Pago

**Versión**: 1.0
**Fecha**: 2026-03-10
**Aplica a**: Cualquier sistema que procese transacciones monetarias o suscripciones (Stripe, PayPal, MercadoPago, Apple/Google Pay).
**Ubicación sugerida**: `docs/integrations/` o `docs/architecture/`

---

## 1. Propósito

Garantizar integraciones de pago seguras, idempotentes y resilientes. El manejo de pagos jamás debe depender puramente del flujo del cliente (Frontend) y debe prevenir ataques de "doble cobro" o desincronización de estados mediante el uso de Webhooks.

---

## 2. El Flujo de Cobro (Backend como Fuente de la Verdad)

1. **Calculo de Montos**: El Frontend NUNCA envía el total a cobrar. El Frontend envía el "ID del Producto" (o carrito). El Backend calcula el precio final verificando cupones, descuentos e inventario en su base de datos segura.
2. **Generación de Intención (Intent)**: El Backend se comunica con la pasarela (ej. Stripe API) y genera un `PaymentIntent` o un `Session Token`.
3. **Carga en el Cliente**: El Frontend usa ese Token para renderizar el formulario oficial de la pasarela y capturar los datos de la tarjeta (PCI Compliance). **Ningún número de tarjeta debe tocar jamás nuestros servidores directamente**.

---

## 3. Idempotencia y Prevención de Doble Cobro

Toda petición de cobro o devolución enviada desde nuestro servidor a la pasarela debe incluir un **Idempotency Key**.
- Un `Idempotency Key` suele ser un UUID único atado a *esa* transacción interna en el carrito de compras.
- Si el servidor falla al recibir la respuesta de Stripe y vuelve a intentar con el mismo Key, Stripe no cobrará dos veces.

---

## 4. Webhooks: Sincronización Real del Pago

Jamás confiar en que el usuario regresará a la "Página de Éxito" (Thank You Page) después del pago. Pueden cerrar la pestaña antes y perderíamos la notificación de éxito.

**Todo cambio de estado debe ser procesado vía Webhooks.**
1. El proveedor de pago notifica a nuestro servidor (ej. `POST /api/webhooks/stripe`).
2. **Verificación de Firma**: Siempre validar criptográficamente la petición del Webhook usando el secreto asignado (Signing Secret) para evitar que un atacante simule ser la pasarela de pago y auto-apruebe sus pedidos.
3. Procesar el evento (`payment_intent.succeeded` o `invoice.payment_failed`) para actualizar la base de datos interna y disparar correos de confirmación.

---

## 5. Manejo de Suscripciones (SaaS)

Las suscripciones son entidades vivas y cambiantes.

- Evitar manejar la lógica de retención, "Prórrogas" (Grace Periods) o ciclos de renovación cronológicos en la base de datos interna.
- Delegar todo el Ciclo de Vida al proveedor de facturación (Billing Provider). Nuestra base de datos solo debe reflejar el estado actual sincronizado vía Webhooks (ej. `status: 'active' | 'past_due' | 'canceled'`).
- Ofrecer Portales de Cliente administrados directamente por el proveedor (ej. Stripe Customer Portal) para disminuir el riesgo de programar una mala gestión de cancelación o cambio de tarjetas.

---

## 6. Moneda y Decimales

- **No usar Float/Decimal flotante en las peticiones API**.
- Todo monto monetario se transfiere (y en su mayoría de pasarelas, se recibe) en la unidad más baja posible sin fraccionamiento (**centavos**).
  - *Mal*: `$10.50`
  - *Bien*: `1050` (Se asume 1050 centavos).
