# Documentación

Toda la documentación del sistema vive acá, junto al código, y se revisa en los mismos pull requests.

## Por dónde empezar

1. [Visión del producto](producto/vision.md): qué es, para quién y qué entra en cada versión.
2. [Glosario](producto/glosario.md): las palabras del negocio y de la arquitectura, con el mismo significado para todos.
3. [Decisiones de arquitectura](adr/README.md): por qué el sistema es como es.

## Qué hay en cada carpeta

| Carpeta | Qué hay |
|---|---|
| `producto/` | Visión, alcance y glosario |
| `adr/` | Decisiones de arquitectura (ADR), numeradas y con estado |
| `backlog/` | Épicas, stories y preguntas abiertas (próximo PR) |
| `diagramas/` | Diagramas Mermaid con SVG, PNG y [galería](diagramas/galeria.html) |
| `operacion/` | Runbooks: despliegue, vuelta atrás, backups (cuando existan) |

## Reglas para escribir documentación

- En español. Los nombres de código que aparecen (tablas, variables, apps) van en inglés, como en el código.
- Sin datos reales de clientes ni secretos: el repo es público.
- Si una decisión cambia, no se edita el ADR viejo para que diga otra cosa: se escribe uno nuevo que lo reemplaza y se marca el viejo como "reemplazada por ADR-XXXX".
