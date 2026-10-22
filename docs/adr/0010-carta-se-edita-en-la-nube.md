# ADR-0010. La carta se edita en la nube y baja al local

- **Estado:** propuesta.
- **Modifica:** el punto 1 de [ADR-0005](0005-sincronizacion-outbox.md), solo para la carta.

## Contexto

ADR-0005 dejó la carta (productos, categorías, precios y aclaraciones) escrita en el servidor local y copiada a la nube. Ya preveía este cambio: si el dueño quiere tocar precios desde el celular, la carta cambia de lugar.

Ese pedido llegó: el dueño quiere cambiar precios y la carta desde afuera del local. Además, el sistema se va a ofrecer a otros negocios, y para varios locales de un mismo dueño es más natural editar todo en un solo lugar.

## Opciones

| Opción | A favor | En contra |
|---|---|---|
| A. Carta solo en la nube, baja al local | Un solo dueño del dato, sin conflictos. Se edita desde cualquier lugar | Sin internet no se puede cambiar un precio desde el local |
| B. Carta editable en los dos lados | Se puede editar con o sin internet | Dos versiones del mismo precio. Hace falta resolver conflictos, y la resolución automática ("gana el último") pierde cambios sin avisar |
| C. Carta en el local, con una pantalla remota que escribe en el local | Sigue el diseño actual | El local no se publica a internet ([HU-251](../backlog/01-epicas-y-stories.md)). Habría que abrir un camino de entrada a la PC del local, que es justo lo que evitamos |

## Decisión propuesta

**Opción A.**

- La carta se escribe **solo en la nube**, desde el portal del dueño.
- El servidor local la **baja** en cada ciclo de sincronización, al abrir la caja y con el botón "Traer cambios ahora". La baja es por versión: el local pide lo que cambió desde la última versión que tiene, y aplicar dos veces lo mismo no cambia nada.
- En el local, la carta es **de solo lectura**. La caja muestra "carta actualizada a las HH:MM".
- Un cambio de precio afecta a las líneas nuevas. Lo ya cargado en una mesa abierta conserva su precio, como dice HU-120.

## Consecuencias

- Sin internet, la carta del local queda como estaba en la última baja. Se puede seguir vendiendo, pero no cambiar precios hasta que vuelva la conexión.
- La sincronización deja de ser solo de subida: hay un canal de bajada para la carta, igual al que ya existe para la configuración del servidor local.
- La pantalla de carta (HU-120, HU-121, HU-124) pasa al portal de la nube. La importación desde el sistema anterior (HU-123) carga la carta en la nube.
- Mesas y personal con PIN siguen escribiéndose en el local, porque son de la operación diaria.
- Si un día varios locales de un mismo dueño quieren compartir la misma carta, se agrega sobre este mismo modelo sin cambiar el mecanismo.

> Tip: esta es la ventaja de la regla "un solo dueño por dato". Cambiar quién es el dueño de algo es mover una fila en una tabla de decisiones, no reescribir la sincronización.
