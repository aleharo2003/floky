# Estados: qué cambios son válidos

**Estado: diseño objetivo.** Cada máquina de estados vive en un solo lugar del código (el servicio de dominio) y rechaza las transiciones que no están dibujadas.

<a id="est-01"></a>

## EST-01. Comprobante fiscal

**Stories:** HU-162, HU-164, HU-165, HU-226, HU-168, HU-169.

<!-- diagram: EST-01 -->
```mermaid
stateDiagram-v2
    [*] --> Cobrada
    state "Venta cobrada" as Cobrada
    state "Pendiente de CAE" as Pendiente
    state "En duda" as Duda
    state "Facturada" as Facturada
    state "Facturada con CAEA" as Caea
    state "CAEA informado" as Informada
    state "Rechazada" as Rechazada
    state "Sin comprobante" as SinComp
    state "Anulación pendiente" as AnulPend
    state "Anulada con nota de crédito" as Anulada
    Cobrada --> Pendiente: F8 factura
    Cobrada --> SinComp: F9 finalizar
    Cobrada --> Caea: F8 sin internet, hay CAEA
    Caea --> Informada: vuelve internet, se informa
    Pendiente --> Facturada: ARCA aprueba
    Pendiente --> Rechazada: ARCA rechaza
    Pendiente --> Duda: sin respuesta
    Duda --> Facturada: consulta encuentra el CAE
    Duda --> Pendiente: consulta no lo encuentra
    Rechazada --> Pendiente: reintento tras corregir
    Facturada --> AnulPend: anular venta
    AnulPend --> Anulada: nota de crédito aprobada
    AnulPend --> Facturada: nota de crédito rechazada
    SinComp --> [*]
    Anulada --> [*]
    Informada --> [*]
```

**Archivos EST-01:** [SVG](svg/EST-01.svg) · [PNG](png/EST-01.png) · [Mermaid](fuentes/EST-01.mmd).

**Lectura:** "Sin comprobante" es final: no se puede facturar después. "En duda" existe para no duplicar: antes de volver a pedir, se consulta (TEC-03). Sin internet, si hay CAEA vigente, la factura es válida en el momento y después se informa; "Pendiente de CAE" queda solo como último recurso.

<a id="est-02"></a>

## EST-02. Sesión de mesa

**Stories:** HU-130, HU-131, HU-136, HU-137, HU-140.

<!-- diagram: EST-02 -->
```mermaid
stateDiagram-v2
    state "Libre" as Libre
    state "Ocupada" as Ocupada
    state "Con precuenta" as Precuenta
    state "Cobrada" as Cobrada
    [*] --> Libre
    Libre --> Ocupada: abrir con caja abierta
    Ocupada --> Ocupada: enviar comanda o anular línea
    Ocupada --> Precuenta: F4 con todo enviado
    Precuenta --> Ocupada: agregar productos
    Precuenta --> Cobrada: cobro confirmado
    Ocupada --> Libre: pasar a otra mesa
    Cobrada --> Libre: mesa liberada
```

**Archivos EST-02:** [SVG](svg/EST-02.svg) · [PNG](png/EST-02.png) · [Mermaid](fuentes/EST-02.mmd).

<a id="est-03"></a>

## EST-03. Turno de caja

**Stories:** HU-150, HU-152, HU-154, HU-223, HU-262.

<!-- diagram: EST-03 -->
```mermaid
stateDiagram-v2
    state "Sin turno" as Sin
    state "Abierto" as Abierto
    state "En arqueo" as Arqueo
    state "Cerrado" as Cerrado
    state "Cerrado y enviado" as Enviado
    [*] --> Sin
    Sin --> Abierto: fondo inicial
    Abierto --> Abierto: ventas, movimientos, correcciones
    Abierto --> Arqueo: F10
    Arqueo --> Abierto: Esc
    Arqueo --> Cerrado: F10 de nuevo
    Cerrado --> Enviado: lote confirmado por la nube
    Cerrado --> Cerrado: corrección del dueño como ajuste posterior
    Enviado --> Sin: listo para el próximo turno
```

**Archivos EST-03:** [SVG](svg/EST-03.svg) · [PNG](png/EST-03.png) · [Mermaid](fuentes/EST-03.mmd).

**Lectura:** un turno cerrado nunca se edita; lo que se corrige después queda como ajuste. "Cerrado y enviado" no es necesario para abrir el siguiente turno: es informativo.

<a id="est-04"></a>

## EST-04. Fila del outbox y lote de sincronización

**Stories:** HU-260, HU-261, HU-263, HU-265.

<!-- diagram: EST-04 -->
```mermaid
stateDiagram-v2
    state "Pendiente" as Pend
    state "En vuelo" as Vuelo
    state "Confirmada" as Conf
    state "Purgada" as Purg
    [*] --> Pend: misma transacción que el cambio
    Pend --> Vuelo: entra en un lote
    Vuelo --> Conf: la nube devuelve su secuencia
    Vuelo --> Pend: error de red o rechazo, reintento
    Conf --> Purg: pasan N días
    Purg --> [*]
```

**Archivos EST-04:** [SVG](svg/EST-04.svg) · [PNG](png/EST-04.png) · [Mermaid](fuentes/EST-04.mmd).

**Lectura:** una fila vuelve a pendiente cuantas veces haga falta; reenviar no duplica porque la nube aplica por versión. Si una fila queda pendiente más que el umbral, la nube alerta (HU-265).
