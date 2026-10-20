# ADR-0002. Django + DRF + React, un mismo código en dos modos

- **Estado:** propuesta.

## Contexto

Somos dos desarrolladores. Necesitamos un servidor que corra en la PC de cada local y otro en la nube, y queremos escribir las reglas de dinero una sola vez. Django y React son el stack que el equipo ya domina.

## Decisión

- **Backend:** Django + Django REST Framework, con PostgreSQL. Todo cambio de esquema va por migraciones.
- **Frontend:** React + TypeScript + Vite. Las pantallas hablan con la API a través de una capa de servicios con contratos tipados, así se pueden probar con datos falsos y cambiar la implementación sin tocar las pantallas.
- **Un solo repositorio y un solo código Django con dos modos**, elegidos por variable de entorno (`FLOKY_MODE=local` o `FLOKY_MODE=cloud`):

| App Django | Modo local | Modo nube | Qué hace |
|---|:---:|:---:|---|
| `core` | sí | sí | Tenant, dinero, utilidades, auditoría |
| `floor` | sí | réplica | Mesas, sectores, comandas |
| `cash` | sí | réplica | Turnos, cobros, pagos, movimientos de caja, correcciones |
| `fiscal` | sí | réplica | Comprobantes, worker de ARCA, QR |
| `printing` | sí | no | Plantillas y cola ESC/POS |
| `sync_sender` | sí | no | Outbox y envío a la nube |
| `sync_receiver` | no | sí | Ingesta idempotente y modelo de lectura |
| `accounts` | PIN del personal | email y contraseña | Identidad y membresías |
| `dashboard`, `accountant` | no | sí | Panel del dueño y vista del contador |
| `finances` | no | sí | Mis finanzas, solo el dueño |

- **Tareas en segundo plano:** un proceso worker por modo. En el local: ARCA, impresión y sincronización. En la nube: alertas y backups. La herramienta se elige en el esqueleto; el criterio es que funcione como servicio de Windows y no necesite Redis.
- **Autenticación en la nube:** con el `User` de Django y la autenticación de DRF. Sin proveedores externos de identidad en el MVP.

## Consecuencias

- Un solo lenguaje de backend para los dos modos: lo que se aprende en uno sirve en el otro, y las reglas de dinero se testean una vez.
- Toda operación de dinero corre dentro de `transaction.atomic()`, junto con su auditoría y su fila de outbox.
- Para el servidor local hay que empaquetar Python para Windows ([ADR-0003](0003-base-local-postgresql.md)).
- Hay que cuidar que una app de modo nube no se importe en modo local (y al revés). Un test de arranque en cada modo lo controla.
