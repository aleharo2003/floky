# ADR-0006. Factura electrónica desde el servidor local y qué hacer sin internet

- **Estado:** aceptada el 22/10/2026 (PR #1), salvo el punto 5 ("sin internet"), que sigue en propuesta hasta que responda el contador (P-10).

## Contexto

- Los locales a los que apunta el sistema ya facturan electrónico con WSFEv1 de ARCA, en general Factura B a consumidor final.
- Desde el **01/12/2026**, ARCA rechaza los pedidos de CAE que no informan la condición frente al IVA del receptor (RG 5616/2024). Hay que mandarla siempre, con "consumidor final" por defecto.
- Requisito: sin internet **se vende igual**, y la factura con CAE sale al volver la conexión.
- La **RG 5782/2025**, vigente desde el 01/06/2026, deja el **CAEA** solo para contingencias. Se pide por web service para períodos quincenales (del 1 al 15 y del 16 a fin de mes), hasta 5 días antes; usa **puntos de venta específicos** con el mismo domicilio; y lo emitido se informa hasta el octavo día corrido posterior al fin del período.

## Decisión

1. **El servidor local habla con ARCA** (WSAA para el ticket de acceso, WSFEv1 para el CAE). Si lo hiciera la nube, el CAE llegaría recién después de sincronizar, horas más tarde.
2. **El cobro nunca espera a ARCA.** El cobro se confirma en la base local y deja un trabajo fiscal en una cola; un worker local lo procesa.
3. **Antes de cualquier reintento** se consulta a ARCA el último número autorizado y el comprobante en duda (`FECompUltimoAutorizado`, `FECompConsultar`). Así, un corte en medio de la respuesta no termina en dos facturas ni en un número salteado.
4. **El certificado y la clave privada viven en el servidor local**, cifrados con DPAPI de Windows y con permisos solo para la cuenta del servicio. Nunca se suben a la nube ni al repositorio.
5. **Sin internet:** la venta queda "pendiente de CAE" y se entrega un **ticket provisorio** que dice "no válido como factura". Al volver la conexión, el worker obtiene el CAE y se imprime o envía la factura.

## Riesgo a validar antes de producción

Entregar un ticket sin CAE y emitir la factura después puede no cumplir con la obligación de entregar el comprobante en el momento de la venta a consumidor final. La alternativa que prevé la norma para cortes es el **CAEA**, que queda como spike en el backlog. Lo define el contador de cada cliente. Esto es una nota de ingeniería, no asesoramiento impositivo.

## Consecuencias

- Los tests usan un doble del web service en unitarios e integración. Contra el ambiente de homologación de ARCA corre un job aparte, no en cada PR.
- El QR sigue la especificación de ARCA: JSON versión 1 en Base64, en `https://www.arca.gob.ar/fe/qr/?p=...`, con `tipoCodAut` "E" para CAE y "A" para CAEA.
- Si se adopta CAEA, hace falta un segundo punto de venta habilitado y un proceso quincenal de pedido e informe.
- La configuración fiscal es por local (tenant): cada uno con su CUIT, punto de venta y certificado.

## Fuentes

- [ARCA, especificación del QR](https://arca.gob.ar/fe/qr/documentos/QRespecificaciones.pdf)
- [RG 5782/2025, texto publicado por el Consejo Profesional de Salta](https://www.consejosalta.org.ar/wp-content/uploads/ARCA-5782.pdf)
- [RG 5616: rechazo desde el 01/12/2026, iProfesional](https://www.iprofesional.com/impuestos/464104-nuevo-requisito-arca-puede-trabar-emision-facturas)
