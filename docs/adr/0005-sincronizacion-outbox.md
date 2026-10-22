# ADR-0005. Sincronización con outbox y un solo dueño por dato

- **Estado:** aceptada el 22/10/2026 (PR #1). El valor de X sigue abierto (P-02).

## Contexto

La operación vive en la base local ([ADR-0001](0001-local-first.md)). El dueño y el contador miran en la nube. Hay que subir los cambios sin frenar la caja, sin perder nada si se corta internet y sin duplicar si un envío se repite.

## Decisión

### 1. Un solo dueño por dato

Cada tipo de dato se escribe en **un solo lugar**. Así no hay conflictos que resolver.

| Dato | Se escribe en | Viaja a |
|---|---|---|
| Salón, comandas, cobros, pagos, correcciones, caja, comprobantes, bitácora | Servidor local | Nube (réplica de lectura) |
| Carta, categorías, mesas, personal con PIN | Servidor local (configuración, solo el dueño) | Nube (para nombres y reportes) |
| Locales, membresías, usuarios con email (dueño, contador) | Nube | No baja al local en el MVP |
| Mis finanzas | Nube | No baja |
| Configuración del servidor local (intervalo X, canal de actualización, equipo revocado) | Nube | Baja al local |

Si más adelante el dueño quiere editar la carta desde el celular, la carta pasa a escribirse en la nube y bajar al local. Se mueve el dato de fila en esta tabla, sin cambiar el mecanismo.

### 2. Outbox transaccional en el local

Cada operación que cambia un dato replicable escribe, **en la misma transacción**, una fila en `sync_outbox` con:

- `id` (UUID v7, ordenable), `tenant_id` y `sequence` (número creciente por local);
- `entity`, `entity_id` y `version` de esa entidad;
- `payload`: la foto completa del agregado (por ejemplo, el cobro con sus pagos), no solo el cambio.

Mandar la foto completa hace que aplicar un evento dos veces, o fuera de orden, dé el mismo resultado. En la nube se hace *upsert* por `(tenant_id, entity, entity_id)` solo si la `version` recibida es mayor. Los eventos de auditoría y bitácora, que nunca cambian, se insertan por su `id` y se ignoran si ya existen.

### 3. Cuándo se envía

- Cada **X** minutos, configurable por local desde la nube. Valor inicial propuesto: 60.
- Al **cerrar la caja**, sin bloquear al cajero.
- Con el botón **"Sincronizar ahora"** del dueño.
- Al volver internet, si quedaron pendientes.

### 4. Cómo se envía

Lotes de hasta N eventos, en orden de `sequence`, por HTTPS, con la credencial propia del equipo del local. La nube responde la última secuencia aplicada y el local marca como confirmado hasta ahí. Si falla, reintenta con espera creciente. Nada sale del outbox hasta estar confirmado; lo confirmado se purga después de N días.

### 5. Cómo se controla

- Cada envío deja un registro `sync_batch` en los dos lados: cantidades, duración y resultado.
- La nube alerta si un local no sincroniza hace más de un umbral.
- Una vez por día se comparan conteos y totales por fecha entre el local y la nube. Si no coinciden, se pide el reenvío de ese día.

## Opciones descartadas

| Opción | Por qué no |
|---|---|
| Replicación lógica de PostgreSQL | Necesita conexión directa del local a la base de la nube, tolera mal cortes largos y ata la nube al esquema interno del local |
| Copiar la base completa cada X | Simple, pero crece con el tiempo y no dice qué cambió |
| Sincronización bidireccional con resolución de conflictos | Innecesaria si cada dato tiene un solo dueño. Demasiada complejidad para dos personas |

## Consecuencias

- Toda operación de negocio del local pasa por la capa de servicios que escribe el outbox. Un `UPDATE` hecho a mano en la base local no se replica, y está bien: nadie debería hacerlo.
- Los IDs se generan en el local (UUID v7). Nunca se usan autoincrementales como identidad entre bases.
