# Datos: base local y base en la nube

**Estado: modelo propuesto.** Muestra las tablas que importan para entender local first y tenants, no el esquema completo. Los nombres son los del código (en inglés); el [glosario](../producto/glosario.md) da su equivalente en español.

<a id="dat-01"></a>

## DAT-01. Base local: núcleo de venta y outbox

**Propósito:** mostrar cómo cada operación deja su rastro para la sincronización y la auditoría. **Stories:** HU-100, HU-140, HU-244, HU-260.

<!-- diagram: DAT-01 -->
```mermaid
erDiagram
    TENANT ||--o{ CASH_SHIFT : has
    TENANT ||--o{ SALE : records
    CASH_SHIFT ||--o{ SALE : groups
    SALE ||--|{ PAYMENT : paid_with
    SALE ||--o{ FISCAL_DOCUMENT : documented_by
    SALE ||--o{ FISCAL_JOB : queues
    SALE ||--o{ PRINT_JOB : queues
    TENANT ||--o{ SYNC_OUTBOX : emits
    TENANT ||--o{ AUDIT_LOG : records
    TENANT {
        uuid id PK
        string cuit
        int point_of_sale
    }
    SALE {
        uuid id PK "UUID v7 generado en el local"
        uuid tenant_id FK
        uuid cash_shift_id FK
        numeric total
        string status
        string fiscal_status
        int version
    }
    PAYMENT {
        uuid id PK
        uuid sale_id FK
        string method
        numeric amount
    }
    FISCAL_DOCUMENT {
        uuid id PK
        uuid sale_id FK
        string type
        int number
        string cae
        string status
    }
    FISCAL_JOB {
        uuid id PK
        string status
        int attempts
        timestamp next_attempt_at
    }
    PRINT_JOB {
        uuid id PK
        string printer
        string status
    }
    SYNC_OUTBOX {
        uuid id PK
        uuid tenant_id FK
        bigint sequence UK
        string entity
        uuid entity_id
        int version
        jsonb payload
        timestamp confirmed_at
    }
    AUDIT_LOG {
        uuid id PK
        uuid tenant_id FK
        string actor_type
        string actor_id
        string action
        jsonb before
        jsonb after
    }
    CASH_SHIFT {
        uuid id PK
        uuid tenant_id FK
        numeric expected_cash
        numeric counted_cash
    }
```

**Archivos DAT-01:** [SVG](svg/DAT-01.svg) · [PNG](png/DAT-01.png) · [Mermaid](fuentes/DAT-01.mmd).

**Lectura:** cobrar escribe en una transacción: `SALE`, `PAYMENT`, `FISCAL_JOB`, `PRINT_JOB`, `AUDIT_LOG` y `SYNC_OUTBOX`. Si algo falla, no queda nada. Los trabajos y el outbox son tablas usadas como colas.

<a id="dat-02"></a>

## DAT-02. Base en la nube: locales, membresías, réplica y finanzas

**Propósito:** mostrar qué es réplica, qué es propio de la nube y cómo se aísla. **Stories:** HU-240, HU-241, HU-243, HU-263, HU-264, HU-210.

<!-- diagram: DAT-02 -->
```mermaid
erDiagram
    USER ||--o{ MEMBERSHIP : has
    TENANT ||--o{ MEMBERSHIP : grants
    TENANT ||--o{ DEVICE : links
    TENANT ||--o{ SALE_REPLICA : receives
    TENANT ||--o{ FISCAL_DOCUMENT_REPLICA : receives
    TENANT ||--o{ SYNC_BATCH : records
    USER ||--o{ FINANCE_ENTRY : keeps
    FINANCE_CATEGORY ||--o{ FINANCE_ENTRY : classifies
    TENANT |o--o{ FINANCE_ENTRY : optional
    USER {
        uuid id PK
        string email UK
    }
    MEMBERSHIP {
        uuid user_id FK
        uuid tenant_id FK
        string role "owner o accountant"
        bool is_active
    }
    TENANT {
        uuid id PK
        string name
        string cuit
        int sync_interval_min
    }
    DEVICE {
        uuid id PK
        uuid tenant_id FK
        string credential_hash
        string version
        timestamp last_seen_at
    }
    SALE_REPLICA {
        uuid id PK "mismo id que en el local"
        uuid tenant_id FK "con RLS"
        int version
        numeric total
        string fiscal_status
    }
    FISCAL_DOCUMENT_REPLICA {
        uuid id PK
        uuid tenant_id FK "con RLS"
        string type
        int number
        numeric net
        numeric vat
    }
    SYNC_BATCH {
        uuid id PK
        uuid tenant_id FK
        bigint sequence_to
        int applied
        int ignored
        string result
    }
    FINANCE_ENTRY {
        uuid id PK
        uuid owner_id FK
        uuid tenant_id FK "opcional"
        date date
        numeric amount
    }
    FINANCE_CATEGORY {
        uuid id PK
        string name
        bool is_business
    }
```

**Archivos DAT-02:** [SVG](svg/DAT-02.svg) · [PNG](png/DAT-02.png) · [Mermaid](fuentes/DAT-02.mmd).

**Lectura:** las tablas `_REPLICA` guardan el mismo `id` que en el local, así el upsert es directo. `MEMBERSHIP` es la única puerta de acceso a un local. `FINANCE_ENTRY` cuelga del dueño; el `tenant_id` es opcional para el resultado por local.
