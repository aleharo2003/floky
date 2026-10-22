# Clases: responsabilidades del dominio

**Estado: modelo conceptual.** Un diagrama de clases conceptual no dice que ya existan esas clases en Python, ni cómo se llaman sus métodos. Dice qué responsabilidad tiene cada concepto.

<a id="cla-01"></a>

## CLA-01. Salón y venta

**Propósito:** ubicar las reglas de la operación diaria en el objeto que las tiene que cumplir. **Stories:** HU-120, HU-131, HU-135, HU-140, HU-142, HU-162.

<!-- diagram: CLA-01 -->
```mermaid
classDiagram
    class Producto {
        codigo
        nombre
        precio
        alicuotaIva
        activo
    }
    class SesionMesa {
        mesa
        mozo
        comensales
        estado
        abrir()
        pedirPrecuenta(descuento)
    }
    class Comanda {
        numero
        claveIdempotencia
        enviar()
    }
    class LineaComanda {
        nombreCopiado
        precioUnitarioCopiado
        cantidad
        aclaraciones
        anular(motivo)
    }
    class Venta {
        subtotal
        descuento
        total
        estadoFiscal
        cobrar(pagos)
        anular(motivo)
        finalizarSinComprobante()
    }
    class Pago {
        medio
        importe
        corregirMedio(nuevo, motivo)
    }
    class ComprobanteFiscal {
        tipo
        puntoVenta
        numero
        condicionIvaReceptor
        cae
        vencimientoCae
    }
    SesionMesa "1" --> "*" Comanda
    Comanda "1" --> "*" LineaComanda
    LineaComanda "*" --> "1" Producto : copia nombre y precio
    SesionMesa "1" --> "0..1" Venta
    Venta "1" --> "1..2" Pago
    Venta "1" --> "0..*" ComprobanteFiscal : factura y notas de crédito
```

**Archivos CLA-01:** [SVG](svg/CLA-01.svg) · [PNG](png/CLA-01.png) · [Mermaid](fuentes/CLA-01.mmd).

**Lectura:** `LineaComanda` copia nombre y precio del producto: un cambio de carta nunca reescribe una venta pasada. `Venta` decide su total; la pantalla solo lo muestra. `Venta` tiene hasta dos pagos, como en el prototipo.

<a id="cla-02"></a>

## CLA-02. Caja, trazabilidad y Mis finanzas

**Propósito:** separar la caja del turno (en el local) de la libreta del dueño (en la nube), y mostrar dónde se apoya la trazabilidad. **Stories:** HU-150, HU-152, HU-220, HU-223, HU-225, HU-210, HU-212.

<!-- diagram: CLA-02 -->
```mermaid
classDiagram
    class TurnoCaja {
        cajero
        fondoInicial
        efectivoEsperado
        efectivoContado
        diferencia
        abrir(fondo)
        cerrar(conteo)
    }
    class MovimientoCaja {
        tipo
        medio
        importe
        motivo
    }
    class Evento {
        hora
        usuario
        accion
        sesionMesa
        venta
        turno
    }
    class CorreccionPago {
        medioAnterior
        medioNuevo
        motivo
        posteriorAlCierre
    }
    class MovimientoFinanzas {
        fecha
        tipo
        importe
        categoria
        local opcional
    }
    class CategoriaFinanzas {
        nombre
        tipo
        delNegocio
    }
    TurnoCaja "1" --> "*" MovimientoCaja
    TurnoCaja "1" --> "*" Evento
    CorreccionPago ..> Evento : genera
    MovimientoFinanzas "*" --> "1" CategoriaFinanzas
    note for MovimientoFinanzas "Vive solo en la nube. Solo el dueño, con segunda clave."
    note for Evento "Solo agregar. Se replica tal cual."
```

**Archivos CLA-02:** [SVG](svg/CLA-02.svg) · [PNG](png/CLA-02.png) · [Mermaid](fuentes/CLA-02.mmd).

**Lectura:** el turno calcula su efectivo esperado con ventas en efectivo, movimientos y gastos; nunca se edita cerrado. Las correcciones y los eventos son de solo agregar. Mis finanzas no conoce a la caja: el panel los cruza (HU-182).
