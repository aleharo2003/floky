# ADR-0004. Cada local es un tenant

- **Estado:** propuesta. El modelo (tenant = local) está acordado; el mecanismo de aislamiento se valida al implementar la primera API de la nube.

## Contexto

El sistema tiene que servir a muchos locales sin cambiar el esquema. Hay que definir qué es un tenant, cómo entra una persona a varios locales y cómo se garantiza que un local no vea datos de otro.

## Decisión

- **Tenant = local.** Cada local tiene sus datos fiscales (CUIT, condición frente al IVA, punto de venta, certificado de ARCA), su servidor local, su personal y su caja.
- **Toda tabla de negocio lleva `tenant_id`.** También en la base local, aunque ahí haya un solo local: así la réplica en la nube no transforma nada.
- **Las personas entran por membresía:** usuario + local + rol. Un dueño con dos locales tiene dos membresías. Un contador puede tener membresías en locales de distintos dueños.
- **Roles del MVP:** dueño, cajero, mozo y contador. El contador ve solo la facturación, en solo lectura.
- **Mis finanzas es del dueño, no del local.** Cada movimiento puede indicar a qué local corresponde, o a ninguno si es personal, para mostrar el resultado por local y total.

## Aislamiento en la nube (defensa en profundidad)

1. **Aplicación:** un manager de Django por defecto que exige filtrar por los locales de la membresía activa, y un permiso de DRF que valida el local pedido contra las membresías del usuario.
2. **Base de datos:** Row Level Security de PostgreSQL en las tablas replicadas y en las de finanzas. Al empezar cada transacción, un middleware fija `SET LOCAL app.tenant_ids`; las políticas solo dejan ver las filas de esos locales.
3. **Tests de fuga:** cada endpoint tiene un test con dos locales y dos usuarios que intenta leer y escribir lo del otro. Pedir un recurso de otro local responde 404, no 403, para no confirmar que existe.

> Tip: la capa 1 sola alcanza casi siempre. La capa 2 está para el día que alguien escriba una consulta a mano y se olvide el filtro. Agregarla al principio cuesta poco; agregarla con datos en producción cuesta mucho.

## Consecuencias

- El usuario de base con el que corre la app en la nube no puede ser superusuario ni dueño de las tablas, porque si no RLS no aplica. Las migraciones corren con otro usuario.
- El esquema de las tablas replicadas es el mismo en el local y en la nube.
- Queda abierto si un mismo CUIT con dos locales usa puntos de venta distintos o comparte uno (ver preguntas abiertas del backlog).
