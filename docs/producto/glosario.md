# Glosario

Si una palabra significa una cosa en una reunión y otra en el código, tarde o temprano aparece un error. Este glosario fija el significado. Si falta un término, se agrega en el mismo PR que lo empieza a usar.

## Negocio

| Término | Significado | En el código |
|---|---|---|
| Local | Un comercio que opera el sistema, con su dirección, su punto de venta de ARCA y su personal | `Tenant` |
| Salón | Las mesas del local, agrupadas en sectores | `floor` |
| Mesa | Lugar físico donde se sienta un cliente. Puede estar libre u ocupada | `Table` |
| Mostrador | Venta sin mesa | `counter` |
| Comanda | Lo que se pidió en una mesa o en mostrador. Tiene ítems | `Order` |
| Precuenta | Detalle impreso de lo consumido antes de cobrar. No es comprobante fiscal | `PreBill` |
| Cobro | El pago de una comanda, con uno o varios medios de pago | `Payment` |
| Turno de caja | Período entre la apertura y el cierre de la caja de un cajero | `CashShift` |
| Arqueo | Comparación entre lo que el sistema espera en caja y lo que se cuenta al cerrar | `CashCount` |
| Mis finanzas | Ingresos y egresos que el dueño anota fuera de la caja (alquiler, sueldos, proveedores). Reemplaza a los cuadernos de papel. Solo la ve el dueño | `finances` |
| Bitácora | Registro de eventos del turno (quién abrió, cargó, anuló o cobró cada cosa) | `EventLog` |

## Fiscal (ARCA)

| Término | Significado |
|---|---|
| ARCA | Agencia de Recaudación y Control Aduanero, ex AFIP |
| WSAA | Web service de autenticación de ARCA. Entrega un ticket de acceso que dura unas horas |
| WSFEv1 | Web service de factura electrónica. Se le pide el CAE de cada comprobante |
| CAE | Código de Autorización Electrónico. Sin CAE, el comprobante no es válido |
| CAEA | CAE anticipado. Desde la RG 5782/2025 se usa solo ante contingencias, con puntos de venta propios |
| Punto de venta | Número que ARCA asigna para emitir comprobantes. Cada local tiene el suyo |
| Factura B | Comprobante a consumidor final o monotributista |
| Factura A | Comprobante a un responsable inscripto, con CUIT |
| Nota de crédito | Comprobante que anula total o parcialmente una factura ya autorizada |
| Condición frente al IVA del receptor | Dato obligatorio en cada pedido de CAE desde el 01/12/2026 (RG 5616/2024) |
| QR fiscal | Código que todo comprobante impreso lleva con los datos de la autorización, según la especificación de ARCA |

## Arquitectura

| Término | Significado |
|---|---|
| Tenant | Cliente aislado dentro del sistema. Acá, un local |
| Membresía | Relación entre una persona, un local y un rol. Una persona puede tener varias |
| Servidor local | El sistema corriendo en la PC del local, con su propia base |
| Nube | El sistema corriendo en internet: panel del dueño, vista del contador, finanzas y réplica de los datos de cada local |
| Local first | La operación se hace contra el servidor local; la nube recibe una copia |
| Outbox | Tabla del servidor local donde cada cambio queda anotado, en la misma transacción, para enviarse después a la nube |
| Idempotente | Que repetir una operación da el mismo resultado que hacerla una vez. Clave para reintentar sin duplicar |
| ADR | Architecture Decision Record: documento corto que registra una decisión, sus opciones y sus consecuencias |
