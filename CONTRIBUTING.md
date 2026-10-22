# Cómo trabajamos

## Idiomas

- **Código, nombres, commits y ramas:** en inglés.
- **Documentación (README, ADRs, diagramas, backlog):** en español.

## Regla número uno: el repo es público

Todo lo que entra a git queda visible y se puede copiar, aunque después se borre o el repo pase a privado. Por eso nunca se suben:

- Secretos: contraseñas, tokens, claves de API, archivos `.env`.
- Certificados o claves privadas (ARCA, TLS).
- Datos reales de clientes: nombres, CUIT, precios, ventas, copias de bases.

Los secretos van en los secretos de cada environment de GitHub o en la máquina donde corre el servicio. Los datos de prueba se inventan.

Si algo se subió por error, avisá enseguida. Un secreto expuesto se rota, no alcanza con borrar el commit.

## Principios del proyecto

Tres principios mandan en cada decisión técnica. Cuando propongas una solución en un PR o un ADR, explicá cómo cumple con los tres.

### 1. Buenas prácticas y patrones

- Código por capas, sin mezclar responsabilidades: vistas y serializers, servicios con las reglas de negocio, modelos y acceso a datos.
- Configuración por variables de entorno, nunca escrita en el código: claves, rutas, URLs, credenciales, intervalos.
- Todo cambio de esquema va por una migración. Nadie toca tablas a mano.
- Tests obligatorios para la lógica de plata y de conversión de datos: totales, redondeos, caja, impuestos, importaciones.
- Linters y formato automático en todo el código.

### 2. Escalable desde el día uno

- Diseñado para varios locales y varias terminales, aunque hoy haya uno solo.
- Todo dato de negocio lleva su local (`tenant_id`).
- API sin estado, consultas paginadas e índices en las tablas grandes.

### 3. Actualizable a distancia

- El servidor de cada local se actualiza solo, con vuelta atrás si algo falla, y reporta su versión, su última sincronización y sus errores.
- La web y la API se despliegan solas.
- La configuración de cada local (por ejemplo, cada cuánto sincroniza) se cambia desde la nube, sin tocar la PC del local.

## Reglas de dinero

- Toda operación de plata corre dentro de `transaction.atomic()`, junto con su registro de auditoría y su fila de outbox.
- Los importes son `Decimal`, nunca `float`.
- El precio se guarda en el detalle de la venta: un cambio de precio posterior no modifica lo ya vendido.
- Nada se borra: se anula con motivo, y queda quién y cuándo.

## Flujo de trabajo

1. Cada cambio sale de una issue de Jira. La rama empieza con su clave, así el PR aparece dentro de la issue: `XXX-57-walking-skeleton` (XXX es la clave del proyecto en Jira).
2. Commits chicos con [Conventional Commits](https://www.conventionalcommits.org/): `feat:`, `fix:`, `docs:`, `test:`, `refactor:`, `chore:`, `ci:`.
3. Pull request contra `main`, con la plantilla completa. El título sigue Conventional Commits y termina con la clave de Jira entre paréntesis: `ci: add CI pipeline (FLK-5)`. Se mergea con squash, así que ese título es el mensaje que queda en `main`: revisalo antes de mergear.
4. **Nadie mergea su propio PR.** La otra persona revisa y aprueba.
5. CI tiene que estar en verde para mergear.

## Entornos

| Entorno | Se despliega |
|---|---|
| Desarrollo | En la PC de cada uno |
| Pruebas | Solo, al mergear a `main` |
| Producción | Desde un tag `vX.Y.Z`, con aprobación manual |
