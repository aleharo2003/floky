# Flujos funcionales: lo que hace la persona

**Estado: diseño objetivo, basado en el prototipo.** Cada flujo muestra decisiones y qué pasa ante errores. Las teclas son las del prototipo; la configuración final vive en un solo archivo del frontend (HU-102).

<a id="fun-01"></a>

## FUN-01. Ingresar con PIN y abrir la caja

**Stories:** HU-110, HU-113, HU-150.

<!-- diagram: FUN-01 -->
```mermaid
flowchart TD
    A["Elegir usuario y escribir PIN"] --> B{"¿PIN correcto?"}
    B -->|"no"| C{"¿5 intentos fallidos?"}
    C -->|"no"| A
    C -->|"sí"| D["Usuario bloqueado unos minutos<br/>queda auditado"]
    B -->|"sí"| E{"¿Hay turno abierto?"}
    E -->|"sí"| F["Ir al salón"]
    E -->|"no"| G{"¿Rol?"}
    G -->|"mozo"| H["Mensaje: la caja está cerrada"]
    G -->|"cajero o dueño"| I["Cargar fondo inicial y turno"]
    I --> J["Turno abierto"] --> F
```

**Archivos FUN-01:** [SVG](svg/FUN-01.svg) · [PNG](png/FUN-01.png) · [Mermaid](fuentes/FUN-01.mmd).

**Lectura:** todo el flujo funciona sin internet: el PIN lo valida el servidor local.

<a id="fun-02"></a>

## FUN-02. Abrir una mesa y cargar la comanda por código

**Stories:** HU-131, HU-132, HU-133, HU-134, HU-135, HU-130.

<!-- diagram: FUN-02 -->
```mermaid
flowchart TD
    A["F2: elegir mesa"] --> B{"¿Mesa libre?"}
    B -->|"sí"| C["Comensales y mozo"] --> D
    B -->|"no"| D["Comanda de la mesa"]
    D --> E["Escribir código, cantidad o nombre"]
    E --> F{"¿Código existe?"}
    F -->|"no"| G["Aviso y sonido, campo limpio"] --> E
    F -->|"sí"| H{"¿Tiene aclaraciones?"}
    H -->|"sí"| I["Selector numerado, Enter o Esc"] --> J
    H -->|"no"| J["Línea agregada sin enviar"]
    J --> K{"¿Más productos?"}
    K -->|"sí"| E
    K -->|"no"| L["Enviar a cocina"]
    L --> M["Comanda guardada, ticket a cocina"]
    M --> N{"¿Imprimió?"}
    N -->|"no"| O["Aviso, se puede reimprimir"]
    N -->|"sí"| P["Volver al salón"]
    O --> P
```

**Archivos FUN-02:** [SVG](svg/FUN-02.svg) · [PNG](png/FUN-02.png) · [Mermaid](fuentes/FUN-02.mmd).

**Lectura:** una impresora sin papel avisa pero no frena: la comanda ya está guardada.

<a id="fun-03"></a>

## FUN-03. Cobrar, facturar o finalizar sin comprobante

**Stories:** HU-136, HU-140, HU-162, HU-164, HU-226, HU-172.

<!-- diagram: FUN-03 -->
```mermaid
flowchart TD
    A["F4: precuenta"] --> B["F6: cobrar"]
    B --> C["Uno o dos medios de pago"]
    C --> D{"¿Suman el total?"}
    D -->|"no"| C
    D -->|"sí"| E["Venta guardada, mesa libre"]
    E --> F{"¿Factura o F9?"}
    F -->|"F9"| G["Sin comprobante<br/>lista aparte para el contador"]
    F -->|"F8 factura"| H["Condición IVA del receptor<br/>consumidor final por defecto"]
    H --> I{"¿Hay internet?"}
    I -->|"no"| J["Ticket provisorio<br/>no válido como factura"]
    J --> K["Pendiente de CAE<br/>se reintenta solo"]
    I -->|"sí"| L{"¿ARCA aprueba?"}
    L -->|"sí"| M["Factura con CAE y QR impresa"]
    L -->|"rechaza"| N["Pendiente con motivo<br/>reintentar o dejar para después"]
    K --> L
```

**Archivos FUN-03:** [SVG](svg/FUN-03.svg) · [PNG](png/FUN-03.png) · [Mermaid](fuentes/FUN-03.mmd).

**Lectura:** el cobro termina en "venta guardada" sin importar lo que pase después. El ticket provisorio está pendiente de validación con el contador (P-10).

<a id="fun-04"></a>

## FUN-04. Cerrar la caja

**Stories:** HU-152, HU-153, HU-224, HU-262.

<!-- diagram: FUN-04 -->
```mermaid
flowchart TD
    A["F10: cierre de caja"] --> B{"¿Mesas abiertas?"}
    B -->|"sí"| C{"¿Es el dueño?"}
    C -->|"no"| D["Aviso con la lista de mesas"]
    C -->|"sí, fuerza"| E
    B -->|"no"| E["Ver esperado por medio de pago"]
    E --> F["Contar billetes y, opcional, posnet, QR y banco"]
    F --> G{"¿Hay diferencia?"}
    G -->|"sí"| H["Sugerencias de cobros con ese importe"]
    H --> I{"¿Era un medio mal cargado?"}
    I -->|"sí"| J["Corregir medio con motivo"] --> E
    I -->|"no"| K["Comentar la diferencia"]
    G -->|"no"| L["F10 otra vez para confirmar"]
    K --> L
    L --> M["Turno cerrado, reporte impreso"]
    M --> N["Envío a la nube sin esperar"]
    N --> O{"¿Hay internet?"}
    O -->|"no"| P["Aviso: se envía al volver la conexión"]
    O -->|"sí"| Q["Dueño ve el cierre desde afuera"]
```

**Archivos FUN-04:** [SVG](svg/FUN-04.svg) · [PNG](png/FUN-04.png) · [Mermaid](fuentes/FUN-04.mmd).

**Lectura:** Enter no cierra la caja: hace falta F10 dos veces, a propósito. El envío a la nube nunca bloquea el cierre.

<a id="fun-05"></a>

## FUN-05. Anular una venta

**Stories:** HU-142, HU-165.

<!-- diagram: FUN-05 -->
```mermaid
flowchart TD
    A["Elegir venta y motivo"] --> B{"¿Turno de la venta?"}
    B -->|"cerrado"| C{"¿Es el dueño?"}
    C -->|"no"| D["Rechazado"]
    C -->|"sí"| E["Ajuste en el turno actual"]
    B -->|"abierto"| F{"¿Tiene factura autorizada?"}
    E --> F
    F -->|"no"| G["Venta anulada, sale de los totales"]
    F -->|"sí"| H["Pedir nota de crédito a ARCA"]
    H --> I{"¿Autorizada?"}
    I -->|"sí"| G
    I -->|"no"| J["Venta sigue cobrada<br/>anulación pendiente con motivo"]
```

**Archivos FUN-05:** [SVG](svg/FUN-05.svg) · [PNG](png/FUN-05.png) · [Mermaid](fuentes/FUN-05.mmd).

<a id="fun-06"></a>

## FUN-06. Mis finanzas: cargar un movimiento

**Stories:** HU-211, HU-212, HU-214.

<!-- diagram: FUN-06 -->
```mermaid
flowchart TD
    A["Dueño en el portal"] --> B["Candado de la barra"]
    B --> C["Segunda clave"]
    C --> D{"¿Correcta?"}
    D -->|"no"| E{"¿5 seguidas?"}
    E -->|"sí"| F["Área bloqueada unos minutos"]
    E -->|"no"| C
    D -->|"sí"| G["Fecha, tipo, categoría, importe, medio, local opcional"]
    G --> H{"¿Datos válidos?"}
    H -->|"no"| I["Error claro, no se guarda"] --> G
    H -->|"sí"| J["Movimiento y auditoría en una transacción"]
    J --> K["Aparece en su mes y en el saldo"]
```

**Archivos FUN-06:** [SVG](svg/FUN-06.svg) · [PNG](png/FUN-06.png) · [Mermaid](fuentes/FUN-06.mmd).

**Lectura:** este flujo no pasa por el local; vive en la nube.

<a id="fun-07"></a>

## FUN-07. El contador consulta la facturación

**Stories:** HU-200, HU-201, HU-202, HU-203, HU-242.

<!-- diagram: FUN-07 -->
```mermaid
flowchart TD
    A["Email y contraseña"] --> B{"¿Válidos?"}
    B -->|"no"| C["Rechazo sin decir cuál falló<br/>auditado"]
    B -->|"sí"| D{"¿Más de un local?"}
    D -->|"sí"| E["Elegir local"] --> F
    D -->|"no"| F["Facturación del mes"]
    F --> G["Filtro de fechas o períodos rápidos"]
    G --> H["Comprobantes y totales por medio y tipo"]
    H --> I["Ventas sin comprobante en lista aparte"]
    F --> J["Datos al HH:MM del último envío"]
    F --> K{"¿Intenta otra ruta?"}
    K -->|"sí"| L["Vuelve a facturación, 403 en la API"]
```

**Archivos FUN-07:** [SVG](svg/FUN-07.svg) · [PNG](png/FUN-07.png) · [Mermaid](fuentes/FUN-07.mmd).

**Lectura:** el contador ve solo facturación (decisión del 05/10/2026). La hora visible evita que trabaje con datos viejos sin saberlo.
