# Arquitectura: del local a la nube

**Estado: arquitectura objetivo, todavía no implementada.**

Cinco dibujos para explicar el sistema en niveles: para quién existe, qué piezas corren y dónde, cómo se ordena el código, cómo se separan los locales y por qué entornos pasa un cambio. No reemplazan a los ADR: todos los ADR están en propuesta ([índice](../adr/README.md)).

<a id="arq-01"></a>

## ARQ-01. Contexto y actores

**Propósito:** presentar el sistema sin hablar de tecnología. **Stories:** HU-110, HU-140, HU-162, HU-181, HU-200, HU-211.

<!-- diagram: ARQ-01 -->
```mermaid
flowchart TB
    Mozo["Mozo<br/>abre mesas y carga comandas"]
    Cajero["Cajero<br/>cobra, factura y cierra la caja"]
    Dueno["Dueño<br/>configura, mira el negocio<br/>y lleva sus finanzas"]
    Contador["Contador<br/>solo facturación, solo lectura"]
    subgraph Sistema["Sistema nuevo"]
        direction LR
        Local["En el local<br/>vender aunque no haya internet"]
        Nube["En la nube<br/>ver desde cualquier lugar"]
        Local -->|"copia cada X<br/>y al cerrar caja"| Nube
    end
    ARCA["ARCA<br/>autoriza facturas con CAE"]
    Impresora["Impresora térmica ESC/POS<br/>tickets y facturas"]
    Cliente["Cliente del local<br/>recibe su comprobante"]
    Mozo --> Local
    Cajero --> Local
    Dueno --> Local
    Dueno --> Nube
    Contador --> Nube
    Local --->|"pide CAE<br/>cuando hay conexión"| ARCA
    Local -->|"imprime"| Impresora
    Impresora --> Cliente
```

**Archivos ARQ-01:** [SVG](svg/ARQ-01.svg) · [PNG](png/ARQ-01.png) · [Mermaid](fuentes/ARQ-01.mmd).

**Lectura:** el personal trabaja contra el local; el dueño usa los dos lados; el contador solo la nube. La flecha del local a la nube va en un solo sentido para la operación (ADR-0005).

**Casos para validar:** sin internet el mozo y el cajero siguen trabajando; el contador nunca entra al local; el dueño ve desde afuera los datos hasta el último envío.

<a id="arq-02"></a>

## ARQ-02. Contenedores: qué corre en el local y qué en la nube

**Propósito:** ubicar cada pieza que se ejecuta por separado y las fronteras de confianza. **Stories:** HU-232, HU-250, HU-251, HU-252, HU-103.

<!-- diagram: ARQ-02 -->
```mermaid
flowchart TB
    subgraph LocalRed["Frontera: red del local"]
        Navegador["Navegador en la PC de caja<br/>(a futuro, celulares)"]
        subgraph PC["PC del local, servicios de Windows"]
            Django["Django en modo local<br/>API de la caja"]
            Worker["Worker local<br/>ARCA, impresión y envío"]
            PGL[("PostgreSQL local<br/>fuente de verdad de la operación")]
        end
        Hasar["Hasar P-250N<br/>ESC/POS por USB o red"]
    end
    subgraph Nube["Frontera: nube"]
        Portal["Portal React<br/>panel, contador, Mis finanzas"]
        API["Django en modo nube<br/>ingesta, lectura, finanzas"]
        PGN[("PostgreSQL nube<br/>réplica por local y finanzas")]
    end
    ARCA["ARCA<br/>WSAA y WSFEv1"]
    Navegador -->|"HTTP en la red local, PIN"| Django
    Django --> PGL
    Worker --> PGL
    Worker -->|"ESC/POS"| Hasar
    Worker -->|"HTTPS con certificado del local"| ARCA
    Worker -->|"HTTPS saliente con credencial del equipo"| API
    Portal -->|"HTTPS, email y contraseña"| API
    API --> PGN
```

**Archivos ARQ-02:** [SVG](svg/ARQ-02.svg) · [PNG](png/ARQ-02.png) · [Mermaid](fuentes/ARQ-02.mmd).

**Lectura:** el servidor local nunca se publica a internet: todas sus conexiones hacia afuera son salientes. La nube no puede llamar al local; el local le pregunta a la nube su configuración cuando sincroniza.

| Pieza | Qué tiene que sostener |
|---|---|
| Django local | Reglas de dinero, permisos por rol, transacción con auditoría y outbox |
| Worker local | Nunca frenar la caja: ARCA, impresión y envío van por colas |
| Django nube | Aislamiento entre locales, ingesta idempotente, solo lectura para el contador |

<a id="arq-03"></a>

## ARQ-03. Un código Django, dos modos

**Propósito:** mostrar cómo se ordena el código para que lo mismo sirva en el local y en la nube sin duplicar reglas. **Stories:** HU-100, HU-104, HU-232.

<!-- diagram: ARQ-03 -->
```mermaid
flowchart TB
    subgraph Front["frontend: React"]
        Pantallas["Pantallas"]
        Contratos["capa de servicios<br/>contratos tipados"]
        Pantallas --> Contratos
    end
    subgraph Back["backend: un proyecto Django"]
        Vistas["Vistas DRF<br/>serializers y permisos"]
        Servicios["Servicios de dominio<br/>cobrar, cerrar caja, facturar"]
        Repos["Modelos y consultas"]
        Vistas --> Servicios --> Repos
        subgraph SoloLocal["solo FLOKY_MODE=local"]
            Fiscal["fiscal: worker ARCA"]
            Impresion["printing: ESC/POS"]
            Emisor["sync_sender: outbox"]
        end
        subgraph SoloNube["solo FLOKY_MODE=cloud"]
            Receptor["sync_receiver: ingesta"]
            Panel["dashboard y accountant"]
            Finanzas["finances"]
        end
        Servicios --> Fiscal
        Servicios --> Emisor
        Servicios --> Impresion
        Receptor --> Repos
        Panel --> Repos
        Finanzas --> Repos
    end
    Contratos -->|"HTTP JSON"| Vistas
```

**Archivos ARQ-03:** [SVG](svg/ARQ-03.svg) · [PNG](png/ARQ-03.png) · [Mermaid](fuentes/ARQ-03.mmd).

**Lectura:** las vistas no tienen reglas de negocio; validan y delegan. Los servicios de dominio son los únicos que escriben dinero y, en la misma transacción, la auditoría y el outbox. Una app que no corresponde al modo no se instala (`INSTALLED_APPS` según `FLOKY_MODE`).

**Casos para validar:** un test de arquitectura falla si una vista escribe un modelo de dinero sin pasar por un servicio; la app `finances` no existe en el servidor local.

<a id="arq-04"></a>

## ARQ-04. Locales como tenants

**Propósito:** explicar quién ve qué cuando hay varios locales. **Stories:** HU-240, HU-241, HU-243, HU-200, HU-210.

<!-- diagram: ARQ-04 -->
```mermaid
flowchart LR
    Lucia["Usuaria: Lucía, dueña"]
    Contador["Usuario: Marcos, contador"]
    subgraph Membresias["Membresías"]
        M1["Lucía · Local Centro · dueña"]
        M2["Lucía · Local Norte · dueña"]
        M3["Marcos · Local Centro · contador"]
    end
    subgraph Datos["Datos en la nube, cada fila con tenant_id"]
        L1[("Local Centro<br/>ventas, comprobantes, caja")]
        L2[("Local Norte<br/>ventas, comprobantes, caja")]
        Fin[("Mis finanzas de Lucía<br/>tenant_id opcional")]
    end
    Lucia --> M1 --> L1
    Lucia --> M2 --> L2
    Contador --> M3 -->|"solo comprobantes"| L1
    Lucia -->|"segunda clave"| Fin
```

**Archivos ARQ-04:** [SVG](svg/ARQ-04.svg) · [PNG](png/ARQ-04.png) · [Mermaid](fuentes/ARQ-04.mmd).

**Lectura:** el acceso nace de una membresía, no del nombre ni del parentesco. Mis finanzas cuelga del dueño y no del local. El aislamiento se aplica dos veces: en Django (permisos y managers) y en PostgreSQL (Row Level Security), ADR-0004.

**Casos para validar:** Marcos pide una venta del Local Norte por su id y recibe 404; Lucía cambia de local sin volver a ingresar; una consulta cruda sin filtro solo devuelve filas de los locales de la membresía activa.

<a id="arq-05"></a>

## ARQ-05. Entornos: desarrollo, pruebas y producción

**Propósito:** mostrar por dónde pasa un cambio y qué usa cada entorno. **Stories:** HU-231, HU-234, HU-235, HU-236.

<!-- diagram: ARQ-05 -->
```mermaid
flowchart LR
    subgraph Dev["Desarrollo: PC de cada uno"]
        DC["Docker Compose<br/>Django local + Django nube<br/>2 PostgreSQL + Vite"]
        DA["ARCA: doble o homologación"]
    end
    subgraph Pru["Pruebas"]
        PL["Servidor local de prueba<br/>canal pruebas"]
        PN["Nube de pruebas<br/>Cloud Run + Neon + Pages"]
        PA["ARCA homologación"]
    end
    subgraph Prod["Producción"]
        RL["Servidor del local<br/>canal estable"]
        RN["Nube de producción<br/>Cloud Run + Neon + Pages"]
        RA["ARCA producción"]
    end
    GH["GitHub Actions"]
    DC -->|"pull request"| GH
    GH -->|"merge a main"| PL
    GH -->|"merge a main"| PN
    GH -->|"tag y aprobación"| RL
    GH -->|"tag y aprobación"| RN
    PL --> PA
    RL --> RA
```

**Archivos ARQ-05:** [SVG](svg/ARQ-05.svg) · [PNG](png/ARQ-05.png) · [Mermaid](fuentes/ARQ-05.mmd).

**Lectura:** cada entorno tiene su base, sus secretos y su ARCA. Nada de pruebas toca datos reales. El hosting es una propuesta (ADR-0008) y se verifica el día que se contrata.
