# Cómo leer y usar el backlog

**Estado: borrador para revisar antes de cargar a Jira.**

## Archivos

| Archivo | Para qué sirve |
|---|---|
| `00-como-leer-el-backlog.md` | Este archivo: convenciones, DoR, DoD, releases y cómo usamos Jira |
| `01-epicas-y-stories.md` | Las épicas y stories con sus criterios de aceptación |
| `02-preguntas-abiertas.md` | Lo que todavía no sabemos y qué stories bloquea |
| `jira-import.csv` | El mismo backlog, listo para importar en Jira |

La fuente de verdad del texto de cada story es `01-epicas-y-stories.md`. Si una story cambia en Jira durante el refinamiento, se actualiza también acá en un PR.

## Convenciones

- **IDs en los documentos:** `E10` a `E33` para épicas y `HU-100` a `HU-330` para historias de usuario. Los diagramas y los ADRs usan estos IDs.
- **Claves en Jira:** el proyecto se llama `FLK` y Jira numera solo (`FLK-1`, `FLK-2`…). El título de cada issue empieza con su `HU-`, así se la encuentra en los documentos: `FLK-57` · "HU-233. Integración continua en cada pull request".
- **Ramas y PRs** usan la clave de Jira, no la `HU-`: `FLK-57-ci-pipeline`. Así GitHub los muestra dentro de la issue.
- **Prioridad:** MoSCoW (Must, Should, Could). En Jira: Must = High, Should = Medium, Could = Low.
- **Versión:** si una story no dice nada, es del MVP. Las de `v1.x` y `v2` lo indican.
- **Estimación:** en story points, en la planning, entre los dos. El backlog no trae horas a propósito.
- **Diagramas:** los IDs (ARQ, DOM, CLA, DAT, TEC, FUN, EST, OPS, FUT) apuntan a `../diagramas/`.
- **Criterios de aceptación:** en formato Dado / Cuando / Entonces. Cada uno se convierte en un test.

## Definition of Ready (cuándo una story puede entrar al sprint)

1. Los dos entienden igual los criterios de aceptación.
2. No tiene preguntas abiertas que cambien su alcance, o el riesgo está acotado por un spike con tiempo fijo.
3. Si toca más de un componente, tiene diagrama asociado.
4. Está estimada y entra en un sprint. Si no entra, se parte.

## Definition of Done (cuándo está terminada)

1. Entró a `main` por un pull request revisado por la otra persona, con CI en verde.
2. Cada criterio de aceptación tiene un test automático: unitario para reglas y dinero, de integración para API más base, end to end para los flujos críticos (cobrar, facturar, cerrar caja, sincronizar).
3. Los límites de rol y de local tienen test (el contador recibe 403 fuera de facturación; un local no ve datos de otro).
4. Toda operación de plata corre en `transaction.atomic()` junto con su auditoría y su fila de outbox.
5. Está desplegada en pruebas (nube y servidor local de pruebas) y pasó el smoke test.
6. Si cambió una responsabilidad, una regla o un flujo, se actualizó el diagrama o el ADR.
7. Si toca la venta, no empeora el presupuesto de rendimiento (HU-271).

## Releases propuestos

| Release | Contenido | Por qué en este orden |
|---|---|---|
| R0. Esqueleto | E23 completa, HU-270 (medir el sistema anterior) | Que el camino a pruebas funcione solo antes de escribir negocio. La línea base de velocidad se mide mientras el sistema anterior sigue en uso |
| R1. Mis finanzas online | E24, E21, HU-227 | Todo en la nube, sin caja ni ARCA. El dueño deja los cuadernos de papel en pocas semanas, y el equipo practica tenants, permisos, CI/CD y auditoría con poco riesgo |
| R2. Vender en el local | E25, E10 a E15, E17, E22, HU-271 | El servidor local y la parte donde la velocidad es vital. Se prueba en paralelo con el sistema anterior, sin comprobantes reales |
| R3. Facturar | E16 en homologación, después producción | Depende de respuestas del contador (P-10) y del certificado |
| R4. Ver desde afuera | E26, E18, E20 | Sincronización, panel del dueño y vista del contador |
| R5. Corte | E28, E19 | Un solo sistema emite comprobantes reales |
| v2 | E30 a E33 | Multilenguaje, menú QR, stock de bebidas, pedidos desde celulares |

## Cómo usamos Jira

**Tablero:** To Do → In Progress → Blocked → In Review → Done.

| Columna | Significa |
|---|---|
| To Do | Entró al sprint y nadie la empezó |
| In Progress | Alguien la está haciendo. Una persona, una story a la vez |
| Blocked | No puede avanzar por algo externo. **Al moverla, se deja un comentario con el motivo y quién la puede destrabar.** Se repasa en cada daily |
| In Review | El PR está abierto y espera la revisión de la otra persona |
| Done | Cumple la Definition of Done |

**Ceremonias (sprints de 2 semanas):**
- **Refinamiento**, antes de cada planning: se revisan las próximas stories contra la Definition of Ready.
- **Planning:** se estima con planning poker y se arma el sprint con lo que entra.
- **Daily**, 10 minutos frente al tablero: qué se movió y qué está trabado.
- **Review y retro** al cerrar el sprint. De la retro sale al menos un cambio concreto, que se carga como issue del sprint siguiente.

## Tips para usar este backlog

- **Estimen juntos.** Si uno dice 3 y el otro 13, lo valioso es la charla: alguno vio un riesgo que el otro no.
- **Cortar en vertical.** Una story atraviesa todas las capas aunque haga poco. "Crear la tabla de productos" no es una story; "buscar un producto por código y verlo en la comanda" sí.
- **Un spike tiene tiempo fijo.** Se decide antes cuánto se le da (por ejemplo, dos días) y al terminar se decide con lo aprendido.
- **Nadie mergea su propio PR.** El que revisa pregunta "¿por qué así?" y el que escribió tiene que poder explicarlo.
- **Si Blocked se llena seguido**, el problema no es el tablero: están entrando stories al sprint sin cumplir la Definition of Ready.
