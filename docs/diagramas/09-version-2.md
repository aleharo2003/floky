# Versión 2: menú QR y stock de bebidas

**Estado: idea para conversar, sin diseño detallado ni estimación.**

<a id="fut-01"></a>

## FUT-01. Menú QR multilenguaje

**Stories:** HU-300, HU-302, HU-310, HU-311.

<!-- diagram: FUT-01 -->
```mermaid
sequenceDiagram
    participant Cli as Cliente del local
    participant Cel as Celular
    participant Pag as Carta pública en la nube
    participant API as Django nube
    participant PN as PostgreSQL nube
    Cli->>Cel: escanea el QR de la mesa
    Cel->>Pag: abre la URL del local
    Pag->>API: GET carta pública del local, idioma del navegador
    API->>PN: productos activos de ese local, traducción si existe
    API-->>Pag: categorías, productos, precios, agotados
    Pag-->>Cli: carta en su idioma, con selector
    Note over API,PN: solo lectura, sin datos de ventas ni del personal
```

**Archivos FUT-01:** [SVG](svg/FUT-01.svg) · [PNG](png/FUT-01.png) · [Mermaid](fuentes/FUT-01.mmd).

**Lectura:** la carta pública se arma con la réplica de la nube, así que muestra los precios al último envío del local. Para que un cambio de precio aparezca enseguida, el cambio de carta puede disparar un envío inmediato.

<a id="fut-02"></a>

## FUT-02. Stock de bebidas

**Stories:** HU-320, HU-321, HU-322, HU-323, HU-324.

<!-- diagram: FUT-02 -->
```mermaid
flowchart LR
    Compra["Ingreso de mercadería"] -->|"suma"| Mov[("Movimientos de stock<br/>solo agregar")]
    Venta["Comanda enviada"] -->|"resta en la misma transacción"| Mov
    Anul["Línea anulada"] -->|"devuelve"| Mov
    Inv["Conteo de inventario"] -->|"ajuste con motivo"| Mov
    Mov --> Exist["Existencia por producto<br/>suma de movimientos"]
    Exist --> Min{"¿Debajo del mínimo?"}
    Min -->|"sí"| Alerta["Aparece en el panel del dueño"]
```

**Archivos FUT-02:** [SVG](svg/FUT-02.svg) · [PNG](png/FUT-02.png) · [Mermaid](fuentes/FUT-02.mmd).

**Lectura:** la existencia no se guarda "a mano": es la suma de movimientos. Vender sin stock se permite y avisa, para no frenar la caja. El stock general queda fuera de alcance; esto cubre el control de bebidas pedido para v2 (P-18).
