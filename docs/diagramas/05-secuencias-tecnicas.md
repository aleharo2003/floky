# Secuencias técnicas

**Estado: diseño objetivo.** Las secuencias muestran el orden de las llamadas y dónde está cada transacción.

<a id="tec-01"></a>

## TEC-01. Cobrar e imprimir sin esperar a ARCA

**Propósito:** el camino crítico de la velocidad. El cajero ve "cobrado" apenas la venta está guardada en la base local. **Stories:** HU-140, HU-171, HU-271.

<!-- diagram: TEC-01 -->
```mermaid
sequenceDiagram
    participant C as Cajero
    participant W as Caja React
    participant D as Django local
    participant DB as PostgreSQL local
    participant K as Worker local
    participant H as Impresora ESC/POS
    C->>W: confirma pagos (F6 y Enter)
    W->>D: POST cobrar, con clave de idempotencia
    D->>D: permisos, turno abierto, recalcula total
    D->>DB: BEGIN
    D->>DB: venta, pagos, evento, auditoría
    D->>DB: trabajo fiscal y trabajo de impresión
    D->>DB: fila en sync_outbox
    D->>DB: COMMIT
    D-->>W: venta cobrada, estado fiscal pendiente
    W-->>C: mesa libre, puede seguir atendiendo
    K->>DB: toma trabajo de impresión
    K->>H: ticket ESC/POS
    alt impresora responde
        K->>DB: impreso
    else sin papel o apagada
        K->>DB: fallido, aviso en pantalla
    end
    Note over D,DB: nada de este camino sale de la red del local
```

**Archivos TEC-01:** [SVG](svg/TEC-01.svg) · [PNG](png/TEC-01.png) · [Mermaid](fuentes/TEC-01.mmd).

**Lectura:** la respuesta al cajero llega después del COMMIT y antes de cualquier trabajo lento. La impresión y ARCA corren después, en el worker. Si se repite la misma solicitud con la misma clave, se devuelve la venta ya creada.

<a id="tec-02"></a>

## TEC-02. Emitir la factura con CAE

**Propósito:** cómo el worker local pide el CAE y qué guarda. **Stories:** HU-161, HU-162, HU-163, HU-166.

<!-- diagram: TEC-02 -->
```mermaid
sequenceDiagram
    participant K as Worker fiscal local
    participant DB as PostgreSQL local
    participant AA as ARCA WSAA
    participant AF as ARCA WSFEv1
    K->>DB: toma el próximo trabajo fiscal con bloqueo de fila
    K->>K: ticket de acceso vigente en caché
    opt ticket vencido
        K->>AA: LoginCms firmado con el certificado del local
        AA-->>K: token y sign, válidos por horas
    end
    K->>AF: FECompUltimoAutorizado
    AF-->>K: último número
    K->>DB: reserva número siguiente
    K->>AF: FECAESolicitar con CondicionIVAReceptorId
    alt aprobado
        AF-->>K: CAE y vencimiento
        K->>DB: BEGIN
        K->>DB: comprobante autorizado, venta facturada
        K->>DB: trabajo de impresión de la factura con QR
        K->>DB: evento, auditoría y outbox
        K->>DB: COMMIT
    else rechazado
        AF-->>K: errores y observaciones
        K->>DB: venta pendiente con motivo
    else sin respuesta
        K->>DB: en duda, ver TEC-03
    end
```

**Archivos TEC-02:** [SVG](svg/TEC-02.svg) · [PNG](png/TEC-02.png) · [Mermaid](fuentes/TEC-02.mmd).

**Lectura:** el certificado nunca sale del servidor local. El neto, IVA y total que se envían salen de la venta guardada y se comparan al centavo con la respuesta. La condición frente al IVA del receptor va siempre (RG 5616).

<a id="tec-03"></a>

## TEC-03. Reintento seguro: nunca dos facturas, nunca un número salteado

**Propósito:** el caso difícil. Se cortó internet justo después de pedir el CAE: no sabemos si ARCA lo autorizó. **Stories:** HU-164, HU-168.

<!-- diagram: TEC-03 -->
```mermaid
sequenceDiagram
    participant K as Worker fiscal local
    participant DB as PostgreSQL local
    participant AF as ARCA WSFEv1
    Note over K,DB: trabajo en duda, número N reservado
    loop hasta que haya conexión, con espera creciente
        K->>AF: FECompConsultar tipo, punto de venta, N
    end
    alt ARCA tiene el comprobante N
        AF-->>K: CAE de N
        K->>DB: guarda ese CAE, no vuelve a pedir
    else ARCA no lo tiene
        AF-->>K: no existe
        K->>AF: FECompUltimoAutorizado
        AF-->>K: último es N-1
        K->>AF: FECAESolicitar con N
        AF-->>K: CAE
        K->>DB: comprobante autorizado
    end
    Note over K,AF: sin internet por horas, las ventas quedan pendientes y el cliente recibe ticket provisorio
```

**Archivos TEC-03:** [SVG](svg/TEC-03.svg) · [PNG](png/TEC-03.png) · [Mermaid](fuentes/TEC-03.mmd).

**Lectura:** primero se pregunta, después se pide. Así un reintento nunca duplica. La alternativa CAEA (RG 5782) para cortes largos se evalúa en el spike HU-168; depende del contador (P-10).

<a id="tec-04"></a>

## TEC-04. Sincronización por outbox

**Propósito:** cómo llegan los datos a la nube cada X y al cerrar caja. **Stories:** HU-260, HU-261, HU-262, HU-263, HU-264, HU-255.

<!-- diagram: TEC-04 -->
```mermaid
sequenceDiagram
    participant K as Worker local
    participant DB as PostgreSQL local
    participant API as Django nube
    participant PN as PostgreSQL nube
    Note over K: cada X minutos, al cerrar caja o con Sincronizar ahora
    K->>DB: lee hasta N filas sin confirmar, por secuencia
    K->>API: POST lote, credencial del equipo, salud del servidor
    API->>API: valida equipo y local
    API->>PN: BEGIN
    loop cada evento del lote
        API->>PN: upsert si la versión es mayor
    end
    API->>PN: sync_batch con aplicados e ignorados
    API->>PN: COMMIT
    API-->>K: secuencia aplicada y configuración vigente
    K->>DB: marca confirmado hasta esa secuencia
    K->>K: aplica configuración nueva si cambió
    alt error de red o 5xx
        K->>K: reintenta con espera creciente, nada se borra
    end
```

**Archivos TEC-04:** [SVG](svg/TEC-04.svg) · [PNG](png/TEC-04.png) · [Mermaid](fuentes/TEC-04.mmd).

**Lectura:** el lote se aplica entero o no se aplica. Repetir un lote no cambia nada (upsert por versión). La configuración remota viaja en la respuesta: la nube nunca llama al local.

<a id="tec-05"></a>

## TEC-05. Del pull request a producción

**Propósito:** el camino de un cambio, con sus controles. **Stories:** HU-230, HU-233, HU-234, HU-235, HU-271.

<!-- diagram: TEC-05 -->
```mermaid
sequenceDiagram
    participant Dev as Desarrollador
    participant GH as GitHub
    participant CI as GitHub Actions
    participant Rev as Revisor
    participant Pru as Pruebas
    participant Prod as Producción
    Dev->>GH: abre pull request
    GH->>CI: formato, lint, tipos, tests, build, auditoría, secretos
    CI-->>GH: verde o rojo
    Rev->>GH: revisa y aprueba
    Dev->>GH: merge a main
    GH->>CI: imagen de la API, front, instalador
    CI->>Pru: migraciones, despliegue nube y canal pruebas
    CI->>Pru: smoke test
    Dev->>GH: tag vX.Y.Z
    GH->>CI: pipeline de producción
    CI-->>Rev: pide aprobación del environment produccion
    Rev->>CI: aprueba
    CI->>Prod: mismo artefacto, canal estable
```

**Archivos TEC-05:** [SVG](svg/TEC-05.svg) · [PNG](png/TEC-05.png) · [Mermaid](fuentes/TEC-05.mmd).

**Lectura:** nadie mergea su propio PR; producción recibe exactamente lo que se probó. El servidor del local toma la versión nueva del canal estable recién al cerrar la caja (TEC-06).

<a id="tec-06"></a>

## TEC-06. Vincular el servidor local y actualizarlo a distancia

**Propósito:** el principio 3 de `CONTRIBUTING.md`, actualizable a distancia, en una secuencia. **Stories:** HU-252, HU-254, HU-255.

<!-- diagram: TEC-06 -->
```mermaid
sequenceDiagram
    participant O as Dueño
    participant P as Portal
    participant API as Django nube
    participant S as Servidor local
    participant U as Actualizador local
    O->>P: generar código de vinculación
    P->>API: crear código de un solo uso
    API-->>O: código, vence en minutos
    O->>S: ingresa el código en la instalación
    S->>API: canjea código
    API-->>S: credencial del equipo
    S->>S: guarda credencial con DPAPI
    Note over S,API: más adelante, en cada sincronización
    API-->>S: versión disponible en su canal
    S->>U: hay versión nueva
    U->>U: espera caja cerrada
    U->>U: descarga, verifica firma, backup
    U->>S: detiene, migra, inicia
    alt chequeo de salud bien
        U->>API: versión nueva instalada
    else falla
        U->>S: vuelve a la versión anterior
        U->>API: actualización fallida, con el error
    end
```

**Archivos TEC-06:** [SVG](svg/TEC-06.svg) · [PNG](png/TEC-06.png) · [Mermaid](fuentes/TEC-06.mmd).

**Lectura:** el código de vinculación es de un solo uso y vence; la credencial que queda es propia del equipo y se puede revocar desde el portal. Una actualización nunca corre con la caja abierta.
