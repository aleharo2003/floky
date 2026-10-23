# ADR-0006. Factura electrónica desde el servidor local y qué hacer sin internet

- **Estado:** aceptada el 22/10/2026 (PR #1). El punto 5 ("sin internet") se definió el 22/10/2026: CAEA, con ticket provisorio como último recurso.

## Contexto

- Los locales a los que apunta el sistema ya facturan electrónico con WSFEv1 de ARCA. Como el sistema se ofrece a distintos negocios, tiene que emitir Factura A, B y C, con sus notas de crédito y débito.
- Desde el **01/12/2026**, ARCA rechaza los pedidos de CAE que no informan la condición frente al IVA del receptor (RG 5616/2024). Hay que mandarla siempre, con "consumidor final" por defecto.
- Requisito: sin internet **se vende igual**, y la factura con CAE sale al volver la conexión.
- La **RG 5782/2025**, vigente desde el 01/06/2026, deja el **CAEA** solo para contingencias. Se pide por web service para períodos quincenales (del 1 al 15 y del 16 a fin de mes), hasta 5 días antes; usa **puntos de venta específicos** con el mismo domicilio; y lo emitido se informa hasta el octavo día corrido posterior al fin del período.

## Decisión

1. **El servidor local habla con ARCA** (WSAA para el ticket de acceso, WSFEv1 para el CAE). Si lo hiciera la nube, el CAE llegaría recién después de sincronizar, horas más tarde.
2. **El cobro nunca espera a ARCA.** El cobro se confirma en la base local y deja un trabajo fiscal en una cola; un worker local lo procesa.
3. **Antes de cualquier reintento** se consulta a ARCA el último número autorizado y el comprobante en duda (`FECompUltimoAutorizado`, `FECompConsultar`). Así, un corte en medio de la respuesta no termina en dos facturas ni en un número salteado.
4. **El certificado y la clave privada viven en el servidor local**, cifrados con DPAPI de Windows y con permisos solo para la cuenta del servicio. Nunca se suben a la nube ni al repositorio.
5. **Sin internet: CAEA.** Mientras hay conexión, el servidor local pide por adelantado el CAEA de cada quincena y lo guarda. Si se corta internet, la factura se emite con ese CAEA, en su punto de venta específico, y se imprime con el QR (`tipoCodAut` "A"). Al volver la conexión, el worker informa a ARCA lo emitido. El cajero no elige nada: el sistema detecta el corte y le muestra "emitida en contingencia".
6. **Último recurso: ticket provisorio.** Si no hay un CAEA vigente (por ejemplo, porque no se pudo pedir a tiempo), la venta queda "pendiente de CAE" y se entrega un ticket que dice "no válido como factura". Al volver la conexión se emite con CAE.

## Riesgo a validar antes de producción

El ticket provisorio del punto 6 puede no cumplir con la obligación de entregar el comprobante en el momento de la venta. Por eso es el último recurso y el sistema tiene que alertar para que no pase: avisa si falta pedir el CAEA de la próxima quincena y si quedan comprobantes con CAEA sin informar antes del plazo. Conviene que el contador de cada cliente conozca este circuito. Esto es una nota de ingeniería, no asesoramiento impositivo.

## Consecuencias

- Los tests usan un doble del web service en unitarios e integración. Contra el ambiente de homologación de ARCA corre un job aparte, no en cada PR.
- El QR sigue la especificación de ARCA: JSON versión 1 en Base64, en `https://www.arca.gob.ar/fe/qr/?p=...`, con `tipoCodAut` "E" para CAE y "A" para CAEA.
- Cada local necesita un punto de venta habilitado para CAEA, además del de CAE, y un proceso automático quincenal de pedido e informe (HU-169).
- La configuración fiscal es por local (tenant): cada uno con su CUIT, punto de venta y certificado.

## Fuentes

- [ARCA, especificación del QR](https://arca.gob.ar/fe/qr/documentos/QRespecificaciones.pdf)
- [RG 5782/2025, texto publicado por el Consejo Profesional de Salta](https://www.consejosalta.org.ar/wp-content/uploads/ARCA-5782.pdf)
- [RG 5616: rechazo desde el 01/12/2026, iProfesional](https://www.iprofesional.com/impuestos/464104-nuevo-requisito-arca-puede-trabar-emision-facturas)
