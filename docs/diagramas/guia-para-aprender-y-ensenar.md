# Usar los diagramas para aprender y enseñar

El proyecto también es una escuela: se construye un sistema real para un cliente y, en el camino, se practican las buenas prácticas. Esta guía propone cómo usar los diagramas para eso.

## Una pregunta por tipo de diagrama

| Tipo | Pregunta | Error típico |
|---|---|---|
| Contexto (ARQ-01) | ¿Para quién existe? | Empezar por frameworks |
| Contenedores (ARQ-02) | ¿Qué se ejecuta por separado y dónde? | Confundir "una app" con "un proceso" |
| Dominio (DOM) | ¿Qué palabras usa el negocio? | Usar "venta" para tres cosas distintas |
| Clases (CLA) | ¿Quién es responsable de cada regla? | Creer que ya existen esas clases |
| Datos (DAT) | ¿Qué se guarda y cómo se relaciona? | Diseñar tablas antes de entender el dominio |
| Secuencia (TEC) | ¿En qué orden y dónde está la transacción? | Responder al usuario antes del COMMIT |
| Flujo (FUN) | ¿Qué decide la persona y qué pasa si falla? | Dibujar solo el camino feliz |
| Estados (EST) | ¿Qué cambios son válidos? | Dejar que cualquier código cambie un estado |

## Cinco recorridos con ejercicio

| Recorrido | Diagramas | Ejercicio |
|---|---|---|
| Seguir un cobro | DOM-02, TEC-01, EST-02 | Señalar en qué momento exacto el cajero ve "cobrado" y qué ya está guardado en ese momento. Cortar la luz un segundo después: ¿qué se pierde? |
| Facturar sin duplicar | TEC-02, TEC-03, EST-01 | Simular que ARCA aprobó pero la respuesta no llegó. Recorrer TEC-03 y explicar por qué no se emite una segunda factura |
| Llevar los datos a la nube | DOM-03, TEC-04, EST-04 | Mandar el mismo lote dos veces y desordenado. Mostrar con los datos que la nube queda igual |
| Aislar locales | ARQ-04, DAT-02 | Dos locales y un contador con membresía en uno. Escribir el test que pide una venta del otro local y espera 404 |
| Llegar a producción | ARQ-05, TEC-05, TEC-06 | Hacer un cambio chico y llevarlo hasta el servidor de pruebas sin tocar nada a mano. Anotar cada paso que no fue automático |

## Una clase de 30 minutos con el mismo caso

Caso ficticio: mesa 4, dos comensales, un café (código 12) y una medialuna (código 31). Pagan $ 5.000 en efectivo con un billete de $ 10.000. No hay internet.

1. Mostrar FUN-02 y FUN-03. Pedir que marquen cada decisión.
2. Seguir TEC-01: ¿qué filas se escriben en la transacción del cobro? (venta, pago, evento, auditoría, trabajo fiscal, trabajo de impresión, outbox).
3. Ubicar la venta en EST-01: queda "Pendiente de CAE". ¿Qué recibe el cliente? (ticket provisorio).
4. Vuelve internet. Recorrer TEC-02 y TEC-04: ¿qué pasa primero, la factura o el envío a la nube? ¿Importa el orden?
5. Convertir lo visto en tres criterios Dado / Cuando / Entonces y compararlos con HU-140 y HU-164.

## Adjuntar un diagrama a una story

Texto para pegar en Jira:

```text
Diagrama: [ID y título]
Fuente: docs/diagramas/[documento]#[id]
Imagen adjunta: [ID].png
Escenarios que revisamos: camino feliz, error y reintento.
Pendientes que afectan esta story: [P-nn o ninguno].
Evidencia al terminar: [test o recorrido que lo demuestra].
```

## Mantenerlos vivos

Al cerrar una story, comparar el dibujo con el código y los tests. Si cambió una responsabilidad, una regla o un camino observable, se corrige el diagrama en el mismo PR. Un diagrama desactualizado enseña mal, que es peor que no tener diagrama.
