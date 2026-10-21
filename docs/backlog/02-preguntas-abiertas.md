# Preguntas abiertas

Cada pregunta dice a quién hay que hacérsela y qué bloquea. Cuando se resuelve, la respuesta va al ADR o a la story que corresponda y la fila se borra de acá en el mismo PR.

## Con el dueño de cada local

| ID | Pregunta | Bloquea |
|---|---|---|
| P-01 | ¿Quiere cambiar precios o la carta desde el celular, fuera del local? Si sí, la carta pasa a escribirse en la nube y bajar al local. | HU-120, ADR-0005 |
| P-02 | ¿Cada cuánto quiere ver los datos actualizados desde afuera (el valor de X)? ¿Le alcanza con "hasta el último cierre" o necesita ver el turno en curso? | HU-261, HU-183 |
| P-03 | ¿Tiene o piensa tener más de un local con el mismo CUIT? ¿Cada uno con su punto de venta? | HU-240, ADR-0004 |
| P-04 | Además de dueño, cajero, mozo y contador, ¿hace falta un encargado con permisos intermedios? | HU-111 |
| P-05 | ¿Trabaja con mesas numeradas y sectores, o solo con mozos? | HU-122, HU-130 |
| P-06 | ¿Se imprime la comanda en cocina o en barra? ¿En qué impresora? | HU-172, HU-173 |
| P-07 | ¿Qué medios de pago usa (débito, crédito, QR, transferencia)? ¿Cierra el posnet y la billetera de QR en cada turno? | HU-141, HU-224 |
| P-08 | En el sistema anterior, ¿qué operaciones cuentan como venta? (invitaciones, consumos internos, anulaciones, estados de las comandas) | HU-226, HU-280 |
| P-11 | ¿La impresora está conectada por USB o por cable de red? ¿Tiene cajón de dinero conectado? | HU-170, HU-171, ADR-0007 |
| P-12 | ¿Qué PC tiene el local? Windows (versión), memoria, disco, si tiene UPS y si queda prendida. | HU-250, ADR-0003 |
| P-14 | ¿Quién renueva el certificado de ARCA y cuándo vence? | HU-161 |
| P-15 | ¿Puede darnos una copia actual de la base del sistema anterior? Hace falta para migrar y para medir la línea base. | HU-270, HU-280 |
| P-16 | ¿Recibe pedidos por plataformas de delivery? ¿Cómo entran hoy? | Alcance futuro |
| P-18 | ¿El control de stock de bebidas es una prioridad para v2? | E32 |

## Con el contador de cada local

| ID | Pregunta | Bloquea |
|---|---|---|
| P-09 | ¿El local emite solo Factura B o también A? | HU-167 |
| P-10 | Si se corta internet, ¿se puede entregar un ticket provisorio y emitir la factura con CAE al volver la conexión, o hay que usar CAEA (RG 5782)? | HU-164, HU-168, ADR-0006 |
| P-17 | ¿Qué ventas se pueden "finalizar sin comprobante"? | HU-226 |

## Del equipo

| ID | Pregunta | Bloquea |
|---|---|---|
| P-13 | Salidas de caja y Mis finanzas: ¿qué regla para no cargar dos veces el mismo pago? Propuesta: Mis finanzas muestra las salidas de caja sincronizadas como movimientos de solo lectura. | HU-151, HU-211 |
| P-19 | Herramienta de tareas en segundo plano para Django en Windows (Django-Q2, un comando en bucle u otra). Se decide en el esqueleto. | HU-232, ADR-0002 |
| P-20 | Cuando el repo pase a privado: ¿pagamos GitHub Pro o Team para no perder los environments, y Code Security para no perder CodeQL, o alcanza con Semgrep CE y otro mecanismo de aprobación? | HU-234, HU-235, ADR-0008, ADR-0009 |
| P-21 | Mis finanzas permite borrar movimientos (HU-211), pero las reglas de dinero de `CONTRIBUTING.md` dicen que nada se borra. ¿Mis finanzas es una excepción (borrado con auditoría) o se cambia a anular con motivo? | HU-211, HU-214 |
