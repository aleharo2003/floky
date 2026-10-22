# Diagramas del sistema

**34 diagramas editables, con SVG y PNG para adjuntar a las stories.** El Markdown numerado es la fuente; `fuentes/`, `svg/`, `png/`, `manifest.json` y `galeria.html` se generan.

Abrir [la galería](galeria.html) para recorrerlos. La [guía didáctica](guia-para-aprender-y-ensenar.md) propone cómo usarlos para aprender y enseñar.

## Empezar por estos cuatro

1. [ARQ-02: qué corre en el local y qué en la nube](01-arquitectura.md#arq-02). Es la decisión que más cambia respecto de la arquitectura anterior.
2. [DOM-02: del pedido al comprobante](02-dominio.md#dom-02). Las palabras del negocio, antes que las tablas.
3. [TEC-01: cobrar sin esperar a ARCA](05-secuencias-tecnicas.md#tec-01). El camino donde la velocidad es vital.
4. [TEC-04: sincronización por outbox](05-secuencias-tecnicas.md#tec-04). Cómo llegan los datos a la nube cada X y al cerrar caja.

## Qué explica cada documento

| Documento | Pregunta que responde | Diagramas |
|---|---|---|
| [01-arquitectura.md](01-arquitectura.md) | ¿Quién usa el sistema, qué corre en el local y qué en la nube? | ARQ-01, ARQ-02, ARQ-03, ARQ-04, ARQ-05 |
| [02-dominio.md](02-dominio.md) | ¿Qué es una venta, un comprobante, un turno? ¿De quién es cada dato? | DOM-01, DOM-02, DOM-03 |
| [03-clases.md](03-clases.md) | ¿Qué responsabilidad tiene cada concepto? | CLA-01, CLA-02 |
| [04-datos.md](04-datos.md) | ¿Qué tablas hay en el local y en la nube, y cómo se aíslan los locales? | DAT-01, DAT-02 |
| [05-secuencias-tecnicas.md](05-secuencias-tecnicas.md) | ¿En qué orden colaboran las piezas, dónde están las transacciones? | TEC-01, TEC-02, TEC-03, TEC-04, TEC-05, TEC-06 |
| [06-flujos-funcionales.md](06-flujos-funcionales.md) | ¿Qué hace cada persona y qué pasa ante un error? | FUN-01, FUN-02, FUN-03, FUN-04, FUN-05, FUN-06, FUN-07 |
| [07-estados.md](07-estados.md) | ¿Qué cambios de estado son válidos? | EST-01, EST-02, EST-03, EST-04 |
| [08-operacion.md](08-operacion.md) | ¿Cómo se migra desde el sistema anterior, se respalda y se vigila? | OPS-01, OPS-02, OPS-03 |
| [09-version-2.md](09-version-2.md) | ¿Cómo encajan el menú QR y el stock de bebidas? | FUT-01, FUT-02 |

## Mapa de stories

Una asociación no significa que la story esté diseñada por completo: algunas tienen un flujo específico y otras solo un modelo de apoyo. El detalle está en cada documento.

| Story | Diagramas |
|---|---|
| HU-100 | [ARQ-03](01-arquitectura.md#arq-03), [DAT-01](04-datos.md#dat-01) |
| HU-103 | [ARQ-02](01-arquitectura.md#arq-02) |
| HU-104 | [ARQ-03](01-arquitectura.md#arq-03) |
| HU-110 | [ARQ-01](01-arquitectura.md#arq-01), [FUN-01](06-flujos-funcionales.md#fun-01) |
| HU-112 | [DOM-03](02-dominio.md#dom-03) |
| HU-113 | [FUN-01](06-flujos-funcionales.md#fun-01) |
| HU-120 | [DOM-03](02-dominio.md#dom-03), [CLA-01](03-clases.md#cla-01) |
| HU-123 | [OPS-01](08-operacion.md#ops-01) |
| HU-130 | [FUN-02](06-flujos-funcionales.md#fun-02), [EST-02](07-estados.md#est-02) |
| HU-131 | [CLA-01](03-clases.md#cla-01), [FUN-02](06-flujos-funcionales.md#fun-02), [EST-02](07-estados.md#est-02) |
| HU-132 | [DOM-01](02-dominio.md#dom-01), [FUN-02](06-flujos-funcionales.md#fun-02) |
| HU-133 | [FUN-02](06-flujos-funcionales.md#fun-02) |
| HU-134 | [FUN-02](06-flujos-funcionales.md#fun-02) |
| HU-135 | [DOM-02](02-dominio.md#dom-02), [CLA-01](03-clases.md#cla-01), [FUN-02](06-flujos-funcionales.md#fun-02) |
| HU-136 | [FUN-03](06-flujos-funcionales.md#fun-03), [EST-02](07-estados.md#est-02) |
| HU-137 | [EST-02](07-estados.md#est-02) |
| HU-140 | [ARQ-01](01-arquitectura.md#arq-01), [DOM-01](02-dominio.md#dom-01), [DOM-02](02-dominio.md#dom-02), [CLA-01](03-clases.md#cla-01), [DAT-01](04-datos.md#dat-01), [TEC-01](05-secuencias-tecnicas.md#tec-01), [FUN-03](06-flujos-funcionales.md#fun-03), [EST-02](07-estados.md#est-02) |
| HU-142 | [DOM-02](02-dominio.md#dom-02), [CLA-01](03-clases.md#cla-01), [FUN-05](06-flujos-funcionales.md#fun-05) |
| HU-150 | [CLA-02](03-clases.md#cla-02), [FUN-01](06-flujos-funcionales.md#fun-01), [EST-03](07-estados.md#est-03) |
| HU-152 | [CLA-02](03-clases.md#cla-02), [FUN-04](06-flujos-funcionales.md#fun-04), [EST-03](07-estados.md#est-03) |
| HU-153 | [FUN-04](06-flujos-funcionales.md#fun-04) |
| HU-154 | [EST-03](07-estados.md#est-03) |
| HU-161 | [TEC-02](05-secuencias-tecnicas.md#tec-02), [OPS-03](08-operacion.md#ops-03) |
| HU-162 | [ARQ-01](01-arquitectura.md#arq-01), [DOM-01](02-dominio.md#dom-01), [DOM-02](02-dominio.md#dom-02), [CLA-01](03-clases.md#cla-01), [TEC-02](05-secuencias-tecnicas.md#tec-02), [FUN-03](06-flujos-funcionales.md#fun-03), [EST-01](07-estados.md#est-01) |
| HU-163 | [TEC-02](05-secuencias-tecnicas.md#tec-02) |
| HU-164 | [TEC-03](05-secuencias-tecnicas.md#tec-03), [FUN-03](06-flujos-funcionales.md#fun-03), [EST-01](07-estados.md#est-01) |
| HU-165 | [DOM-02](02-dominio.md#dom-02), [FUN-05](06-flujos-funcionales.md#fun-05), [EST-01](07-estados.md#est-01) |
| HU-166 | [TEC-02](05-secuencias-tecnicas.md#tec-02) |
| HU-168 | [TEC-03](05-secuencias-tecnicas.md#tec-03), [EST-01](07-estados.md#est-01) |
| HU-171 | [TEC-01](05-secuencias-tecnicas.md#tec-01) |
| HU-172 | [FUN-03](06-flujos-funcionales.md#fun-03) |
| HU-181 | [ARQ-01](01-arquitectura.md#arq-01) |
| HU-182 | [CLA-02](03-clases.md#cla-02) |
| HU-194 | [OPS-01](08-operacion.md#ops-01) |
| HU-200 | [ARQ-01](01-arquitectura.md#arq-01), [ARQ-04](01-arquitectura.md#arq-04), [FUN-07](06-flujos-funcionales.md#fun-07) |
| HU-201 | [FUN-07](06-flujos-funcionales.md#fun-07) |
| HU-202 | [FUN-07](06-flujos-funcionales.md#fun-07) |
| HU-203 | [FUN-07](06-flujos-funcionales.md#fun-07) |
| HU-210 | [ARQ-04](01-arquitectura.md#arq-04), [CLA-02](03-clases.md#cla-02), [DAT-02](04-datos.md#dat-02) |
| HU-211 | [ARQ-01](01-arquitectura.md#arq-01), [DOM-01](02-dominio.md#dom-01), [DOM-03](02-dominio.md#dom-03), [FUN-06](06-flujos-funcionales.md#fun-06) |
| HU-212 | [CLA-02](03-clases.md#cla-02), [FUN-06](06-flujos-funcionales.md#fun-06) |
| HU-214 | [FUN-06](06-flujos-funcionales.md#fun-06) |
| HU-220 | [CLA-02](03-clases.md#cla-02) |
| HU-223 | [DOM-02](02-dominio.md#dom-02), [CLA-02](03-clases.md#cla-02), [EST-03](07-estados.md#est-03) |
| HU-224 | [FUN-04](06-flujos-funcionales.md#fun-04) |
| HU-225 | [CLA-02](03-clases.md#cla-02) |
| HU-226 | [DOM-02](02-dominio.md#dom-02), [FUN-03](06-flujos-funcionales.md#fun-03), [EST-01](07-estados.md#est-01) |
| HU-230 | [TEC-05](05-secuencias-tecnicas.md#tec-05) |
| HU-231 | [ARQ-05](01-arquitectura.md#arq-05) |
| HU-232 | [ARQ-02](01-arquitectura.md#arq-02), [ARQ-03](01-arquitectura.md#arq-03) |
| HU-233 | [TEC-05](05-secuencias-tecnicas.md#tec-05) |
| HU-234 | [ARQ-05](01-arquitectura.md#arq-05), [TEC-05](05-secuencias-tecnicas.md#tec-05) |
| HU-235 | [ARQ-05](01-arquitectura.md#arq-05), [TEC-05](05-secuencias-tecnicas.md#tec-05) |
| HU-236 | [ARQ-05](01-arquitectura.md#arq-05) |
| HU-240 | [ARQ-04](01-arquitectura.md#arq-04), [DAT-02](04-datos.md#dat-02) |
| HU-241 | [ARQ-04](01-arquitectura.md#arq-04), [DAT-02](04-datos.md#dat-02) |
| HU-242 | [FUN-07](06-flujos-funcionales.md#fun-07) |
| HU-243 | [ARQ-04](01-arquitectura.md#arq-04), [DOM-03](02-dominio.md#dom-03), [DAT-02](04-datos.md#dat-02) |
| HU-244 | [DAT-01](04-datos.md#dat-01) |
| HU-245 | [OPS-03](08-operacion.md#ops-03) |
| HU-246 | [OPS-02](08-operacion.md#ops-02) |
| HU-250 | [ARQ-02](01-arquitectura.md#arq-02), [OPS-01](08-operacion.md#ops-01) |
| HU-251 | [ARQ-02](01-arquitectura.md#arq-02) |
| HU-252 | [ARQ-02](01-arquitectura.md#arq-02), [TEC-06](05-secuencias-tecnicas.md#tec-06) |
| HU-253 | [OPS-02](08-operacion.md#ops-02) |
| HU-254 | [TEC-06](05-secuencias-tecnicas.md#tec-06), [OPS-02](08-operacion.md#ops-02) |
| HU-255 | [DOM-03](02-dominio.md#dom-03), [TEC-04](05-secuencias-tecnicas.md#tec-04), [TEC-06](05-secuencias-tecnicas.md#tec-06), [OPS-03](08-operacion.md#ops-03) |
| HU-260 | [DOM-03](02-dominio.md#dom-03), [DAT-01](04-datos.md#dat-01), [TEC-04](05-secuencias-tecnicas.md#tec-04), [EST-04](07-estados.md#est-04) |
| HU-261 | [DOM-01](02-dominio.md#dom-01), [TEC-04](05-secuencias-tecnicas.md#tec-04), [EST-04](07-estados.md#est-04) |
| HU-262 | [TEC-04](05-secuencias-tecnicas.md#tec-04), [FUN-04](06-flujos-funcionales.md#fun-04), [EST-03](07-estados.md#est-03) |
| HU-263 | [DAT-02](04-datos.md#dat-02), [TEC-04](05-secuencias-tecnicas.md#tec-04), [EST-04](07-estados.md#est-04) |
| HU-264 | [DAT-02](04-datos.md#dat-02), [TEC-04](05-secuencias-tecnicas.md#tec-04) |
| HU-265 | [EST-04](07-estados.md#est-04), [OPS-03](08-operacion.md#ops-03) |
| HU-266 | [OPS-03](08-operacion.md#ops-03) |
| HU-270 | [OPS-01](08-operacion.md#ops-01) |
| HU-271 | [TEC-01](05-secuencias-tecnicas.md#tec-01), [TEC-05](05-secuencias-tecnicas.md#tec-05) |
| HU-280 | [OPS-01](08-operacion.md#ops-01) |
| HU-281 | [OPS-01](08-operacion.md#ops-01) |
| HU-300 | [FUT-01](09-version-2.md#fut-01) |
| HU-302 | [FUT-01](09-version-2.md#fut-01) |
| HU-310 | [DOM-01](02-dominio.md#dom-01), [FUT-01](09-version-2.md#fut-01) |
| HU-311 | [FUT-01](09-version-2.md#fut-01) |
| HU-320 | [DOM-01](02-dominio.md#dom-01), [FUT-02](09-version-2.md#fut-02) |
| HU-321 | [FUT-02](09-version-2.md#fut-02) |
| HU-322 | [FUT-02](09-version-2.md#fut-02) |
| HU-323 | [FUT-02](09-version-2.md#fut-02) |
| HU-324 | [FUT-02](09-version-2.md#fut-02) |
| HU-330 | [DOM-01](02-dominio.md#dom-01) |

## Qué es vigente y qué es propuesta

- Los ADR-0001 a ADR-0009 están en propuesta. Ver el [índice](../adr/README.md) para el estado de cada uno.
- Los diagramas muestran diseño objetivo. Ninguno representa código implementado todavía.

## Regenerar

Editar el bloque Mermaid del documento numerado, conservando el comentario `<!-- diagram: ID -->`. Después, desde `herramientas/`:

```text
npm install
npx playwright install chromium
npm run exportar
```

Ver [herramientas/README.md](herramientas/README.md). Antes de commitear, revisar la imagen: el exportador valida sintaxis, no si una flecha está bien puesta.
