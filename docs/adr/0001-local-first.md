# ADR-0001. Local first: servidor y base en cada local, copia en la nube

- **Estado:** propuesta.

## Contexto

Tres requisitos mandan sobre todo lo demás:

1. **La velocidad es vital.** El sistema que reemplazamos corre en la PC del local y responde al instante.
2. **El local no puede frenar si se corta internet.** Se vende igual.
3. **El dueño y el contador quieren ver desde afuera.** Ventas y movimientos tienen que llegar a una base online cada X tiempo y cuando el cajero cierra la caja.

## Opciones

| Opción | A favor | En contra |
|---|---|---|
| A. Cloud first: la caja habla directo con la nube | Una sola base, menos piezas, sin sincronización | Cada operación depende de internet; la latencia se nota; sin internet no se vende |
| B. Local first: servidor y base en el local, réplica en la nube | La venta nunca sale de la red del local; funciona sin internet; la nube deja de ser crítica para operar | Hay que instalar y actualizar un servidor en la PC del local, sincronizar y vigilar que la copia esté al día |
| C. App de escritorio con base embebida en cada PC | No hace falta servidor | Con varias PCs y celulares en el mismo local, cada equipo tendría su base y habría conflictos entre ellos |

## Decisión

**Opción B.** En cada local corre un **servidor local** con su **base de datos**. Las PCs de caja, y más adelante los celulares de los mozos, usan la app web contra ese servidor por la red del local. El servidor local:

- guarda toda la operación: salón, comandas, cobros, caja, comprobantes y bitácora;
- pide el CAE a ARCA cuando hay internet y encola cuando no hay ([ADR-0006](0006-arca-desde-el-servidor-local.md));
- imprime directo en la impresora térmica ([ADR-0007](0007-impresion-escpos.md));
- anota cada cambio en un outbox y lo sube a la nube cada X tiempo y al cerrar la caja ([ADR-0005](0005-sincronizacion-outbox.md)).

En la **nube** viven el panel del dueño, la vista del contador, Mis finanzas, el alta de locales y la réplica de lo que manda cada local.

## Consecuencias

- La nube es una **copia de lectura** de la operación. Si se cae, el local sigue vendiendo. Por eso se puede arrancar con planes gratuitos ([ADR-0008](0008-entornos-hosting-ci-cd.md)).
- **Mis finanzas vive solo en la nube**: ahí la nube es la fuente de verdad y necesita backups propios con restauración probada.
- Desde afuera, el dueño ve los datos con un atraso de hasta X tiempo, o hasta el último cierre. En el local todo es en vivo. El panel siempre muestra "datos al HH:MM".
- Aparecen piezas nuevas para construir y operar: instalador, actualización remota, backups locales y sincronización.

> Tip: la pregunta que decide entre cloud first y local first no es técnica, es de negocio: ¿qué pasa si se corta internet un sábado a la noche? Si la respuesta es "no se puede vender", la arquitectura ya está elegida.
