# Preguntas abiertas

Cada pregunta dice a quién hay que hacérsela y qué bloquea. Cuando se resuelve, la respuesta va al ADR o a la story que corresponda, y la pregunta pasa a la tabla de respondidas con un resumen.

## Con el dueño de cada local

| ID | Pregunta | Bloquea |
|---|---|---|
| P-07 | Usa todos los medios de pago (efectivo, débito, crédito, QR, transferencia). ¿Cierra el posnet y la billetera de QR en cada turno, o una vez por día? De eso depende que la comparación por medio de pago se pueda hacer en cada cierre. | HU-224 |
| P-08 | El sistema anterior marca las comandas con letras (C, F, L, X). ¿Qué significa cada una y cuáles cuentan como venta? Hace falta para migrar el historial y para que los reportes cierren con lo que el dueño ya conoce. | HU-280 |
| P-14 | ¿Cuándo vence el certificado de ARCA que usa hoy? El trámite del nuevo lo hace el equipo, con una delegación del dueño en ARCA (sin pedirle su clave fiscal). | HU-160, HU-161 |

## Con el contador de cada local

| ID | Pregunta | Bloquea |
|---|---|---|
| P-17 | ¿Qué ventas se pueden "finalizar sin comprobante"? | HU-226 |

## Del equipo

| ID | Pregunta | Bloquea |
|---|---|---|
| P-02 | Valor inicial de X (cada cuántas horas se envían los lotes). Propuesta: 1 hora, configurable por local. | HU-261 |
| P-19 | Cómo corren las tareas en segundo plano en el servidor local (ARCA, impresión, sincronización) y en la nube. Hay que comparar todas las alternativas en un ADR antes del esqueleto. | HU-232, ADR-0002 |
| P-20 | Cuando el repo pase a privado: ¿pagamos GitHub Pro o Team para no perder los environments, y Code Security para no perder CodeQL? Se decide ese día, según los ingresos del sistema. | HU-234, HU-235, ADR-0008, ADR-0009 |

## Respondidas

| ID | Pregunta | Respuesta | Dónde quedó |
|---|---|---|---|
| P-01 | ¿El dueño quiere cambiar precios o la carta desde afuera? | Sí | ADR-0010, HU-120 |
| P-02 | ¿Cada cuánto se ven los datos desde afuera? | Lotes cada X horas y siempre al cerrar la caja, más un botón "Enviar ahora" para el cajero. El valor inicial de X sigue abierto | ADR-0005, HU-261, HU-262 |
| P-03 | ¿Varios locales con el mismo CUIT? | No por ahora, pero la arquitectura tiene que permitirlo | ADR-0004, HU-240 |
| P-04 | ¿Hace falta un encargado? | No por ahora. Más adelante, roles configurables | HU-111, HU-114 |
| P-05 | ¿Mesas numeradas o solo mozos? | Depende del local: es una función activable | HU-122, HU-247 |
| P-06 | ¿Dónde se imprime la comanda? | En el primer local, en la única impresora, en la barra junto a la caja | HU-173 |
| P-11 | ¿Cómo está conectada la impresora? | Por USB, se puede pasar a red. Sin cajón de dinero | ADR-0007 |
| P-12 | ¿Qué PC tiene el local? | Windows 10, sin UPS, se apaga al cerrar (puede quedar prendida). Faltan los requisitos mínimos | HU-256 |
| P-13 | Salidas de caja y Mis finanzas, ¿cómo no cargar dos veces? | Las salidas de caja aparecen en Mis finanzas como movimientos de solo lectura | HU-213 |
| P-15 | ¿Puede dar una copia actual de la base? | Sí | HU-270, HU-280 |
| P-16 | ¿Usa delivery? | No, pero se puede ofrecer como función activable | E34, HU-247 |
| P-18 | ¿Stock de bebidas en v2? | Sí, como función activable | E32, HU-247 |
| P-21 | Mis finanzas, ¿borrar o anular? | Anular con motivo, como el resto del dinero | HU-211, HU-214 |
| P-09 | ¿Qué tipos de factura se emiten? | Todos (A, B y C). El sistema se ofrece a distintos negocios | HU-167 |
| P-10 | ¿Qué pasa si se corta internet? | CAEA. Si no hay CAEA disponible, ticket provisorio como último recurso | ADR-0006, HU-169 |
| P-08 (parte) | ¿Qué son las operaciones "invitación" del sistema anterior? | Ventas que se cerraron sin factura. Equivalen a "finalizar sin comprobante" | HU-226, HU-280 |
