# ADR-0007. Impresión directa en ESC/POS desde el servidor local

- **Estado:** aceptada el 22/10/2026 (PR #1). En el primer local la impresora está por USB, se puede pasar a red y no tiene cajón. En cada instalación nueva hay que confirmar cómo está conectada.

## Contexto

El primer local tiene una **Hasar P-250N**: térmica de 80 mm, 250 mm/s, interfaces serie, USB y Ethernet, conector para cajón de dinero (24 V, 1 A) y comandos **ESC/POS**. Es una impresora **no fiscal**, que es lo que corresponde con factura electrónica: la validez la da el CAE y el ticket lleva el QR. La mayoría de las térmicas del mercado hablan el mismo ESC/POS.

## Decisión

- La app `printing` del servidor local arma el ticket en ESC/POS y lo manda directo:
  - por **Ethernet**: socket TCP crudo al puerto 9100 de la impresora, o
  - por **USB**: a través de la cola de impresión de Windows en modo RAW.
- Cada impresión va a una **cola local** (una tabla) que se escribe en la misma transacción que el cobro o la comanda. Si la impresora no responde, la venta no se frena: el trabajo queda "fallido", la pantalla avisa y se puede reimprimir.
- Codificación de texto: una página de códigos con acentos y ñ que soporte el equipo (por ejemplo CP858 o CP1252). Se valida en el spike de impresión.
- El **cajón de dinero** se abre con el pulso ESC/POS al cobrar en efectivo, si el local tiene uno conectado.

> Tip: si se puede elegir, conviene Ethernet. Con USB dependés del driver de Windows y del nombre de la cola; con Ethernet la impresora tiene una IP fija y cualquier terminal la alcanza a través del servidor.

## Consecuencias

- No hace falta un programa aparte de impresión con su propia credencial y su propio actualizador: es parte del servidor local.
- El spike se hace con la impresora real o con una del mismo modelo. Sin una a mano, se prueba con un emulador ESC/POS y se valida en el local.
- Una impresora de cocina o barra es otra entrada en la configuración de impresoras, con su ruteo por categoría de producto.
