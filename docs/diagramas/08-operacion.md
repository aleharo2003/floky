# Operación: corte, backups y alertas

**Estado: propuesta.**

<a id="ops-01"></a>

## OPS-01. Del sistema anterior al nuevo

**Propósito:** el corte en pasos, con un solo emisor de comprobantes en cada momento. **Stories:** HU-123, HU-194, HU-250, HU-270, HU-280, HU-281.

<!-- diagram: OPS-01 -->
```mermaid
flowchart TD
    A["Medir el sistema anterior en uso: línea base"] --> B["Copia actual de la base del sistema anterior"]
    B --> C["Importar carta, mozos y clientes con CUIT"]
    C --> D["Instalar servidor local y vincularlo"]
    D --> E["Ensayo en paralelo<br/>el anterior factura, el nuevo en homologación"]
    E --> F{"¿Servicio de prueba completo bien?"}
    F -->|"no"| G["Corregir y repetir el ensayo"] --> E
    F -->|"sí"| H["Día del corte, antes de abrir"]
    H --> I["Último número del sistema anterior por tipo y punto de venta"]
    I --> J["Sistema nuevo pasa a ARCA producción<br/>toma el siguiente número"]
    J --> K["Acta del corte"]
    K --> L["Primeros servicios con el equipo disponible<br/>sistema anterior instalado sin facturar"]
```

**Archivos OPS-01:** [SVG](svg/OPS-01.svg) · [PNG](png/OPS-01.png) · [Mermaid](fuentes/OPS-01.mmd).

**Lectura:** en ningún momento facturan los dos sistemas contra ARCA producción. El sistema anterior queda como respaldo operativo, no fiscal.

<a id="ops-02"></a>

## OPS-02. Backups y restauración

**Propósito:** qué se copia, dónde y cómo se prueba. **Stories:** HU-246, HU-253, HU-254.

<!-- diagram: OPS-02 -->
```mermaid
flowchart LR
    subgraph Local["Local"]
        PGL[("PostgreSQL local")]
        BL["pg_dump al cerrar caja y de noche"]
        Ext["Disco externo o carpeta configurada"]
        PGL --> BL --> Ext
    end
    subgraph Nube["Nube"]
        PGN[("PostgreSQL nube<br/>réplica y Mis finanzas")]
        GA["GitHub Actions nocturno<br/>pg_dump cifrado"]
        R2[("Almacenamiento externo<br/>Cloudflare R2")]
        PGN --> GA --> R2
    end
    PGL -.->|"sincronización"| PGN
    R2 -->|"restauración mensual en entorno aislado"| Prueba["Base de prueba"]
    Ext -->|"restauración en PC de prueba"| Prueba2["Servidor local de prueba"]
```

**Archivos OPS-02:** [SVG](svg/OPS-02.svg) · [PNG](png/OPS-02.png) · [Mermaid](fuentes/OPS-02.mmd).

**Lectura:** la réplica no es un backup (si se borra algo en el local, se replica el borrado lógico). Un backup cuenta recién cuando se restauró al menos una vez.

<a id="ops-03"></a>

## OPS-03. Alertas operativas

**Propósito:** enterarse antes que el cliente. **Stories:** HU-161, HU-245, HU-255, HU-265, HU-266.

<!-- diagram: OPS-03 -->
```mermaid
flowchart LR
    S["Reporte de salud del servidor local<br/>viene en cada lote"] --> Eval{"Evaluación en la nube"}
    Cron["Chequeo programado en la nube"] --> Eval
    Eval -->|"sin lotes hace más del umbral"| A1["Sync atrasada"]
    Eval -->|"facturas pendientes viejas"| A2["CAE pendientes"]
    Eval -->|"faltan 30 días"| A3["Certificado por vencer"]
    Eval -->|"backup local fallido"| A4["Backup local"]
    Eval -->|"actualización revertida"| A5["Versión fallida"]
    Eval -->|"conciliación diaria no cierra"| A6["Diferencia local y nube"]
    A1 --> Mail["Email al dueño y al equipo"]
    A2 --> Mail
    A3 --> Mail
    A4 --> Mail
    A5 --> Mail
    A6 --> Mail
```

**Archivos OPS-03:** [SVG](svg/OPS-03.svg) · [PNG](png/OPS-03.png) · [Mermaid](fuentes/OPS-03.mmd).

**Lectura:** la alerta de sincronización atrasada es la más importante: es la que detecta la falla silenciosa.
