# ADR-0003. PostgreSQL también en el servidor local

- **Estado:** propuesta. Se valida en el esqueleto, probando el instalador en una PC con Windows parecida a la de un local real.

## Contexto

El servidor local necesita una base. Hoy un local típico tiene una sola PC; a futuro, varias PCs y celulares contra el mismo servidor. La nube usa PostgreSQL.

## Opciones

| Opción | A favor | En contra |
|---|---|---|
| A. PostgreSQL en la PC del local | Mismo motor que la nube: mismas migraciones, mismos tipos (`numeric(14,2)`), mismas restricciones y mismos tests. Aguanta varias terminales sin problema | Instalación más pesada; hay que registrarlo como servicio, actualizarlo y hacerle backup |
| B. SQLite en modo WAL | Un archivo, cero instalación, backup trivial | Diferencias con PostgreSQL en tipos, concurrencia y funciones. Los tests tendrían que correr contra los dos motores. Con varias terminales y el worker escribiendo a la vez puede aparecer `database is locked` |

## Decisión propuesta

**PostgreSQL (A).** La instalación se paga una vez y se automatiza en el instalador. Tener el mismo motor en los dos lados evita errores que solo aparecerían en el local, que es justo donde no hay nadie para depurarlos.

## Consecuencias

- El instalador incluye PostgreSQL (binarios portables, `initdb` y registro como servicio de Windows) y crea un usuario de base con permisos mínimos para la app.
- Backups locales automáticos con `pg_dump`.
- CI corre los tests contra PostgreSQL en contenedor, igual que en producción.

## Qué podría cambiar esta decisión

Si las PCs de los locales son muy limitadas (poca memoria o disco), SQLite vuelve a estar en la mesa. Hay que relevar el equipo real antes de cerrar este ADR.
