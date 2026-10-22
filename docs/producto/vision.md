# Visión del producto

## Qué es

Un sistema de punto de venta y gestión para restaurantes, confiterías y bares. Cubre el salón, las comandas, el cobro, la caja y la facturación electrónica ante ARCA, y le permite al dueño y a su contador ver la información desde cualquier lugar.

## Para quién

| Persona | Qué necesita |
|---|---|
| Cajero | Cargar pedidos y cobrar rápido, con teclado, aunque se corte internet |
| Mozo | Abrir mesas y cargar comandas (desde la caja en el MVP, desde el celular en v2) |
| Dueño | Ver ventas, caja y resultado desde afuera; administrar carta, mesas y personal; llevar sus finanzas (lo que hoy anota en cuadernos) |
| Contador | Consultar la facturación al día, en solo lectura, sin que nadie le mande archivos |

## Lo que no se negocia

1. **La velocidad.** El sistema reemplaza a uno de escritorio que responde al instante. Cargar un ítem, buscar un producto o cobrar no puede sentirse más lento. Se mide antes y después (ver backlog, épica de rendimiento).
2. **Vender sin internet.** Si se corta la conexión, el local sigue atendiendo.
3. **No perder plata ni datos.** Toda operación de dinero queda registrada y auditada. Nada se borra: se anula con motivo.

## Cómo está pensado

- **Local first** ([ADR-0001](../adr/0001-local-first.md)): cada local tiene un servidor con su base. La venta nunca sale de la red del local. La nube recibe una copia cada X horas y siempre al cerrar la caja.
- **Un local es un tenant** ([ADR-0004](../adr/0004-tenant-por-local.md)): el mismo sistema sirve a muchos locales, cada uno aislado de los demás.
- **Factura electrónica** ([ADR-0006](../adr/0006-arca-desde-el-servidor-local.md)): el servidor local pide el CAE a ARCA. El cobro no espera a ARCA.
- **Impresión directa** ([ADR-0007](../adr/0007-impresion-escpos.md)) en impresoras térmicas ESC/POS.

## Alcance por versión

| Versión | Qué incluye |
|---|---|
| MVP | Acceso del personal con PIN, carta y salón, comandas, cobro con varios medios de pago, caja con arqueo, factura electrónica A, B y C, con contingencia CAEA si se corta internet, impresión de tickets, trazabilidad del turno, sincronización con la nube, panel del dueño, vista del contador, finanzas del dueño, importación desde el sistema anterior |
| v1.x | Mejoras sobre lo anterior: pasar y unir mesas, comanda impresa en cocina, actualización masiva de precios, cuenta corriente de clientes |
| v2 | Multilenguaje, menú QR público por local, stock (empezando por bebidas), mozos cargando pedidos desde el celular |

Fuera de alcance por ahora: stock general y compras a proveedores. La integración con plataformas de delivery se evalúa para v2.

## Pensado para varios clientes

El sistema se va a ofrecer a otros restaurantes, comedores y confiterías. Por eso cada local prende o apaga las funciones que usa (mesas numeradas, impresora de cocina, cajón de dinero, stock de bebidas, delivery), y los roles se arman con permisos, para poder sumar uno nuevo sin tocar el código.

## Cómo sabemos que funciona

- Un cajero que viene del sistema anterior hace un turno completo sin pedir ayuda.
- Las operaciones del día a día están dentro del presupuesto de rendimiento medido.
- El dueño ve desde el celular lo vendido hasta el último cierre de caja.
- El contador encuentra todos los comprobantes del mes sin pedirlos.
