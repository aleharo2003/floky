# Dominio: los conceptos del negocio

**Estado: modelo propuesto.**

Antes de las tablas y las clases, las palabras. Si los dos usan "venta" para cosas distintas, el código lo va a reflejar.

<a id="dom-01"></a>

## DOM-01. Mapa de capacidades: MVP y v2

**Propósito:** ver de un vistazo qué se construye primero y qué queda para después. **Stories:** HU-132, HU-140, HU-162, HU-211, HU-261, HU-310, HU-320, HU-330.

<!-- diagram: DOM-01 -->
```mermaid
flowchart TB
    subgraph MVP["MVP: reemplazar el sistema anterior"]
        Salon["Salón y comandas"]
        Cobro["Cobro y caja"]
        Fact["Facturación ARCA"]
        Imp["Impresión"]
        Traz["Trazabilidad del turno"]
        Sync["Sincronización"]
        Panel["Panel del dueño"]
        Cont["Vista del contador"]
        Fin["Mis finanzas"]
        Salon --> Cobro --> Fact --> Imp
        Cobro --> Traz
        Cobro --> Sync --> Panel
        Sync --> Cont
        Fin -.-> Panel
    end
    subgraph V2["v2"]
        I18n["Multilenguaje"]
        QR["Menú QR"]
        Stock["Stock de bebidas"]
        Cel["Pedidos desde celulares"]
    end
    Salon -.-> Cel
    Salon -.-> Stock
    I18n -.-> QR
    Sync -.-> QR
```

**Archivos DOM-01:** [SVG](svg/DOM-01.svg) · [PNG](png/DOM-01.png) · [Mermaid](fuentes/DOM-01.mmd).

**Lectura:** línea continua = dependencia dentro del MVP; punteada = lo que habilita después. El menú QR se apoya en la carta sincronizada; el stock, en las comandas.

<a id="dom-02"></a>

## DOM-02. Del pedido al comprobante

**Propósito:** separar comanda, venta, pago, comprobante y turno. Al analizar sistemas anteriores se ve seguido que sumar comandas y comprobantes como si fueran ventas da totales que no cierran. **Stories:** HU-135, HU-140, HU-142, HU-162, HU-165, HU-226, HU-223.

<!-- diagram: DOM-02 -->
```mermaid
flowchart LR
    Mesa["Sesión de mesa<br/>o mostrador"] -->|"una o varias"| Comanda["Comanda enviada<br/>líneas con precio copiado"]
    Comanda -->|"al cobrar se cierra en"| Venta["Venta<br/>total calculado por el servidor"]
    Venta -->|"uno o dos"| Pago["Pago<br/>medio e importe"]
    Pago -.->|"si se equivocó el medio"| Correccion["Corrección<br/>antes, después y motivo"]
    Venta -->|"0 o 1"| Factura["Comprobante fiscal<br/>B o A, con CAE"]
    Venta -->|"o"| SinComp["Sin comprobante<br/>F9, lista aparte"]
    Factura -.->|"si se anula"| NC["Nota de crédito"]
    Turno["Turno de caja"] -->|"agrupa"| Venta
    Turno -->|"agrupa"| Mov["Movimientos de caja<br/>retiros, aportes, gastos del mozo"]
```

**Archivos DOM-02:** [SVG](svg/DOM-02.svg) · [PNG](png/DOM-02.png) · [Mermaid](fuentes/DOM-02.mmd).

**Lectura:** la **venta** es el hecho económico (lo que se cobró). El **comprobante** es su documento fiscal y puede no existir (sin comprobante) o estar pendiente (sin CAE todavía). La comanda no es venta: una mesa puede tener varias comandas y una sola venta.

**Casos para validar:** el total de ventas del turno no cambia si una factura queda pendiente; anular una venta facturada pide nota de crédito; corregir un medio de pago no toca la factura.

<a id="dom-03"></a>

## DOM-03. ¿De quién es cada dato?

**Propósito:** la regla que evita conflictos de sincronización: cada dato se escribe en un solo lugar. **Stories:** HU-120, HU-112, HU-243, HU-211, HU-255, HU-260.

<!-- diagram: DOM-03 -->
```mermaid
flowchart LR
    subgraph EscribeLocal["Se escribe en el local"]
        A1["Salón, comandas, ventas, pagos"]
        A2["Caja, comprobantes, eventos"]
        A3["Carta, mesas, personal con PIN"]
    end
    subgraph EscribeNube["Se escribe en la nube"]
        B1["Locales y membresías"]
        B2["Usuarios con email: dueño y contador"]
        B3["Mis finanzas"]
        B4["Configuración del servidor local"]
    end
    EscribeLocal -->|"sube por el outbox"| Replica[("Réplica de lectura en la nube")]
    B4 -->|"baja al sincronizar"| ServidorLocal["Servidor local"]
```

**Archivos DOM-03:** [SVG](svg/DOM-03.svg) · [PNG](png/DOM-03.png) · [Mermaid](fuentes/DOM-03.mmd).

**Lectura:** si un dato tiene un solo dueño, nunca hay dos versiones que reconciliar. Si mañana el dueño quiere editar precios desde el celular (P-01), la carta cambia de columna y pasa a bajar al local; el mecanismo es el mismo.
