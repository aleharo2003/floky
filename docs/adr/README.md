# Decisiones de arquitectura (ADR)

Un ADR registra una decisión importante: el contexto, las opciones que se miraron, lo que se eligió y lo que eso trae. Sirve para que dentro de un año alguien entienda por qué el sistema es así, y para que una decisión no se discuta de nuevo cada mes.

## Estados

- **Propuesta:** escrita y en revisión. Se puede cambiar.
- **Aceptada:** el equipo la aprobó. Se cambia solo con un ADR nuevo.
- **Reemplazada por ADR-XXXX:** quedó vieja. No se borra, para conservar la historia.

## Índice

| ADR | Decisión | Estado |
|---|---|---|
| [0001](0001-local-first.md) | Local first: servidor y base en cada local, copia en la nube | Propuesta |
| [0002](0002-stack-django-react.md) | Django + DRF + React, un mismo código en dos modos | Propuesta |
| [0003](0003-base-local-postgresql.md) | PostgreSQL también en el servidor local | Propuesta, se valida en el esqueleto |
| [0004](0004-tenant-por-local.md) | Cada local es un tenant, con aislamiento en dos capas | Propuesta |
| [0005](0005-sincronizacion-outbox.md) | Sincronización con outbox y un solo dueño por dato | Propuesta |
| [0006](0006-arca-desde-el-servidor-local.md) | Factura electrónica desde el servidor local y qué hacer sin internet | Propuesta, con pendiente fiscal |
| [0007](0007-impresion-escpos.md) | Impresión directa en ESC/POS desde el servidor local | Propuesta |
| [0008](0008-entornos-hosting-ci-cd.md) | Entornos, hosting de bajo costo y CI/CD | Propuesta |
| [0009](0009-analisis-estatico-seguridad.md) | Análisis estático de seguridad (SAST) en CI | Propuesta |

## Cómo escribir uno nuevo

Copiá la estructura de cualquiera de estos: estado, contexto, opciones, decisión y consecuencias. Que sea corto: si no entra en dos pantallas, probablemente son dos decisiones.
