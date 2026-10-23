# ADR-0008. Entornos, hosting de bajo costo y CI/CD

- **Estado:** aceptada el 22/10/2026 (PR #1). Los precios y límites de los planes gratuitos cambian seguido: se verifican el día que se contrata.

## Contexto

Queremos un entorno de **desarrollo** para cada uno, uno de **pruebas** y uno de **producción**, con despliegue automático, el menor costo posible (mejor si es gratis) y la posibilidad de crecer después. Con local first ([ADR-0001](0001-local-first.md)) la nube no es crítica para vender, así que arrancar con planes gratuitos tiene poco riesgo.

## Entornos

| Entorno | Servidor local | Nube | ARCA | Datos |
|---|---|---|---|---|
| Desarrollo | Docker Compose en la PC de cada uno | Docker Compose en la misma PC | Doble del web service u homologación | Seed inventado |
| Pruebas | Instalador del canal "pruebas" en una PC o VM con Windows | Proyecto propio, se despliega solo al mergear a `main` | Homologación | Inventados o anonimizados |
| Producción | Instalador del canal "estable" en cada local | Proyecto propio, se promueve con aprobación | Producción | Reales |

Ni desarrollo ni pruebas apuntan a datos reales o a ARCA producción.

## Hosting propuesto para la nube (verificado en septiembre y octubre de 2026)

| Pieza | Opción gratuita | Límites a tener en cuenta | Plan B pago |
|---|---|---|---|
| API Django (contenedor) | Google Cloud Run | 2 millones de requests y 180.000 vCPU-segundos por mes gratis; escala a cero; **pide cuenta de facturación** (poner alerta de presupuesto en USD 1) | Render o Railway, plan chico |
| PostgreSQL | Neon Free | 1 GB por proyecto, 100 CU-hora por mes, se suspende a los 5 minutos sin uso, sin restauración a un punto en el tiempo | Neon Launch o Supabase Pro |
| Front (portal, contador, finanzas) | Cloudflare Pages | Uso comercial permitido; 500 builds por mes | No hace falta |
| Backups de la nube | GitHub Actions nocturno con `pg_dump` cifrado a Cloudflare R2 | Verificar el límite gratis de R2 el día que se configure | |

Descartados para producción: **Vercel Hobby** (solo uso personal y no comercial) y **Render Free** (se duerme a los 15 minutos, la base vence a los 30 días y su propia documentación dice que no es para producción).

> Ojo con Supabase Free: pausa el proyecto tras una semana sin actividad. Con sincronización cada hora no pasaría, pero su base de 500 MB es más chica que la de Neon.

## Repositorio público

En el plan Free de GitHub, los environments con aprobación manual solo funcionan en repositorios públicos. Por eso este repo arranca público, con estas reglas: sin secretos, sin certificados y sin datos reales de clientes (ver `CONTRIBUTING.md`). Cuando pase a privado hay que decidir si se paga GitHub Pro o Team, o si la aprobación a producción se hace con otro mecanismo.

## CI/CD (GitHub Actions)

```text
PR abierto        → formato, lint, tipos, tests (pytest con PostgreSQL, vitest), build,
                    SAST, auditoría de dependencias, búsqueda de secretos
merge a main      → imagen de la API + migraciones + front → pruebas
                    instalador Windows → canal "pruebas"
                    smoke test
tag vX.Y.Z        → aprobación manual (environment "production")
                    mismo artefacto → producción, instalador → canal "estable"
nocturno          → backup de la nube, tests contra ARCA homologación
```

## Consecuencias

- Costo de la nube con pocos locales: **cero** dentro de los planes gratuitos. Riesgo: un pico o un error de configuración en Cloud Run se cobra; por eso la alerta de presupuesto.
- Con varios locales, o cuando haga falta restaurar a un punto en el tiempo, se pasa la base a un plan pago. Es un cambio de configuración, no de código.
- Mis finanzas vive solo en la nube: hasta tener un plan con restauración a un punto en el tiempo, el backup nocturno con una restauración probada es obligatorio.

## Fuentes

- [SnapDeploy, estado de los planes gratuitos (septiembre 2026)](https://snapdeploy.dev/state-of-free-hosting)
- [AgentDeals, plan Free de Neon (verificado en octubre 2026)](https://agentdeals.dev/vendor/neon)
- [Flavio Copes, planes gratuitos lado a lado (septiembre 2026)](https://flaviocopes.com/hosting-free-tiers/)
- [Automation Atlas, límites de Supabase Free 2026](https://automationatlas.io/answers/supabase-free-tier-limits-2026/)
- [GitHub Docs, environments y planes](https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/manage-environments)
