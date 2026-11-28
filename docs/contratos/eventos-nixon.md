# Contratos de comunicación y eventos

Todos los URI, nombres de eventos, payloads y parámetros siguientes son **contratos objetivo**. Deben validarlos los responsables de cada extremo. No se afirma que estén implementados en el legado.

## Responsabilidad y propiedad

Información y Avisos produce información derivada para consulta: reportes, avisos y auditoría central. Es un único servicio de lectura y comunicación de resultados de CLAB; sus módulos comparten proyecciones y recuperación. No decide reservas, permisos, estado técnico ni horarios. Separar sus módulos en nuevos servicios se reconsideraría si requirieran escalado, permisos o ciclos de entrega claramente distintos.

Base exclusiva `clab_avisos`, PostgreSQL con credencial propia. Entidades: `ReservaProyectada`, `AsistenciaProyectada`, `LaboratorioProyectado`, `IncidenciaProyectada`, `AcademicoProyectado`, `Notificacion`, `NotificacionDestinatario`, `AuditoriaCentral`, `AuditoriaLocal`, `InboxProcesado`, `EstadoSincronizacion` y `Outbox`. Los identificadores de usuarios, carreras y reservas son referencias externas sin FK remotas. La relación notificación/destinatario y sus claves son locales. No se replica la contraseña, el token ni el conjunto de permisos para autorizar a partir de datos atrasados.

Las proyecciones de reportes conservan los identificadores y etiquetas de carrera/periodo/asignatura del momento de la reserva, su intervalo, laboratorio, estado y datos de asistencia. La base de Identidad conserva los catálogos originales. Una modificación posterior del catálogo no reescribe el significado histórico de las reservas.

## API REST sincrónica del servicio

Prefijo propuesto `/api`, conservado por Gateway sin reescritura en este servicio. El Gateway, sus límites y los prefijos de otros servicios corresponden a Humberto; no se simula un acuerdo sobre ellos.

| Método y ruta | Contrato principal | Permiso y RF |
| --- | --- | --- |
| `GET /api/reportes/uso?carreraId=&periodoId=&desde=&hasta=&cursor=&limite=` | Resumen de horas reservadas por laboratorio y carrera, número de reservas vigentes/canceladas y asistencia. La definición de uso se explicita: reserva vigente y asistencia no equivalen. Intervalos `[desde,hasta)` en UTC; máximo 100 filas por página. Respuesta incluye `generadoEn`, `actualizadoHastaPorOrigen` y `estadoSincronizacion`. | `reportes:leer`; RF-13. |
| `GET /api/reportes/uso/exportacion?...&formato=csv` | Mismos filtros y conjunto de datos que la consulta; CSV UTF-8. Exportación acotada a 10 000 filas, criterio propuesto de implementación. Si se excede, `422` y solicitar menor rango. | `reportes:exportar`; RF-13. |
| `GET /api/notificaciones?estado=&cursor=&limite=` | Solo avisos cuyo destinatario sea el usuario autenticado; no recibe un `usuarioId` arbitrario del cliente. | `avisos:leer-propios`; RF-15. |
| `PATCH /api/notificaciones/{id}/lectura` | Marca leído por el destinatario, de forma idempotente; confirma `leidoEn`. Una notificación ajena devuelve `404`. Guarda auditoría local y outbox. | `avisos:leer-propios`; RF-15 y aporte local RF-14. |
| `GET /api/auditoria?origen=&usuarioId=&desde=&hasta=&cursor=&limite=` | Consulta paginada de copias centrales: operación, usuario, recurso, origen y fecha. No modifica ni borra la auditoría fuente. | `auditoria:leer`; vista RF-14. |

Fechas ISO 8601, UUID, límite y cursor validados; filtros no autorizan acceso por sí solos. Códigos: `400` entrada inválida, `401` sesión inválida/revocada, `403` sin permiso, `404` recurso no visible, `422` exportación fuera de límite, `429` límite del Gateway, `503` dependencia no disponible. Error con `codigo`, `mensaje` y `correlationId`, sin trazas internas. Base de proyección caída: `503`, nunca lista vacía fingida. Proyección todavía sin carga inicial: `503 PROYECCION_NO_INICIALIZADA`. Datos atrasados: `200` con el estado de sincronización explícito, sin prometer frescura.

## Canales sincrónicos relevantes

| ID | Llamante → receptor | Mecanismo y propósito |
| --- | --- | --- |
| S01 | Web/móvil → Gateway | REST/JSON HTTPS; entrada única para consulta y lectura de avisos. |
| S02 | Gateway → Información y Avisos | REST/JSON HTTPS; enruta los cinco endpoints anteriores. |
| S03 | Servicio protegido → Identidad | REST/JSON HTTPS, `POST /internal/autorizaciones/verificar` propuesto. Comprueba token/sesión, cuenta activa, permiso y alcance vigentes; el servicio conserva sus reglas de recurso. El origen y receptor de la llamada se autentican entre sí. No se publica esta ruta a clientes. |
| S04 | Reservas → Laboratorios | REST/JSON HTTPS para catálogo operativo antes de nuevas reservas, conservando I09 del PFC. |
| S05 | Laboratorios → Reservas | REST/JSON HTTPS para barrera operativa idempotente, conservando I13 del PFC. No vuelve a llamar a Laboratorios desde ese manejador. |

S03 desde Avisos usa un timeout total propuesto de 800 ms y sin reintento automático en la petición interactiva. Ese valor es un presupuesto de diseño para Avisos, no una medición PE-U1 ni el presupuesto de agenda. Firma y vencimiento del JWT se verifican en Gateway; el servicio comprueba autorización vigente con Identidad y protege el recurso. La caída de Identidad produce `503` y no se utiliza una copia de permisos para permitir acceso. Para servicios origen, RNF-03 exige esto en confirmación, cancelación y bloqueo. Los consumidores de eventos usan una cuenta técnica con permisos del broker; no dependen de la sesión humana para procesar un cambio ya confirmado y auditado.

## Catálogo de eventos de negocio

Exchange durable `clab.eventos.v1`, tipo `topic`. El publicador usa la clave de la columna correspondiente. Todos los eventos tienen versión de esquema `1`.

| Productor | Tipo | Routing key | Destino dentro de Avisos | Contenido mínimo adicional |
| --- | --- | --- | --- | --- |
| Reservas | `ReservaConfirmada` | `reservas.reserva.confirmada.v1` | Proyecciones y notificaciones | Reserva, laboratorio, docente, carrera, asignatura, periodo, intervalo, estado e instantánea académica. |
| Reservas | `ReservaCancelada` | `reservas.reserva.cancelada.v1` | Proyecciones y notificaciones | Instantánea completa de la reserva cancelada y motivo visible autorizado. |
| Reservas | `ConflictoReservaDetectado` | `reservas.solicitud.conflicto.v1` | Notificaciones | Solicitud, solicitante, intervalo, laboratorio y código de conflicto. Solicitud rechazada no crea reserva; registra resultado y outbox por ID de solicitud. |
| Reservas | `AsistenciaRegistrada` | `reservas.asistencia.registrada.v1` | Proyecciones | ID de asistencia, reserva, participante, presencia/ausencia y versión. |
| Reservas | `BloqueoAgendaCambiado` | `reservas.bloqueo.cambiado.v1` | Proyecciones y notificaciones a afectados explícitos | Instantánea del bloqueo efectivo, estado, laboratorio, intervalo y revisión. Incluye las barreras I13 confirmadas. |
| Laboratorios | `LaboratorioActualizado` | `laboratorios.laboratorio.actualizado.v1` | Proyecciones | ID, nombre, ubicación, capacidad, estado y versión. |
| Laboratorios | `IncidenciaActualizada` | `laboratorios.incidencia.actualizada.v1` | Proyecciones y notificaciones | Incidencia, laboratorio, equipo, estado y destinatarios. |
| Laboratorios | `EquipoEstadoActualizado` | `laboratorios.equipo.estado.v1` | Proyecciones | Equipo, laboratorio, estado y versión. |
| Identidad | `AcademicoActualizado` | `identidad.academico.actualizado.v1` | Proyecciones | Tipo de entidad académica, ID, etiqueta, estado y versión. No reemplaza las instantáneas históricas. |
| Identidad | `UsuarioDesactivado` | `identidad.usuario.desactivado.v1` | Notificaciones | ID de usuario y destinatarios permitidos; sin credenciales. |
| Identidad | `RolUsuarioModificado` | `identidad.usuario.rol.v1` | Notificaciones | ID de usuario y código de cambio; no se usa para autorizar. |
| Identidad | `PeriodoAcademicoCerrado` | `identidad.periodo.cerrado.v1` | Proyecciones y notificaciones | ID de periodo, estado y versión. |
| Cada uno de los cuatro servicios | `AccionAuditada` | `auditoria.<origen>.registrada.v1` | Auditoría central | ID de operación, actor, acción, recurso, fecha y origen. Avisos publica solo sus acciones locales; consumir una copia central no genera otra acción auditada. |

Todas las filas son propuestas; el catálogo conserva los tres nombres de eventos de Identidad presentes en el borrador consultado. Los eventos de bloqueo sugeridos por Laboratorios se sustituyen en esta propuesta por eventos de **bloqueo efectivo emitidos por Reservas**. Laboratorios puede comunicar su trámite operativo, pero no es dueño de la agenda. Esta diferencia debe validarse con Belinda y Juliana.

## Sobre común

```json
{
  "eventId": "1ebf381f-e542-4f99-897d-7dd1eab33112",
  "eventType": "ReservaConfirmada",
  "schemaVersion": 1,
  "source": "microservicio_reservas",
  "occurredAt": "2026-11-27T15:00:00Z",
  "aggregateType": "Reserva",
  "aggregateId": "res-104",
  "aggregateVersion": 1,
  "sourceSequence": 1042,
  "actorId": "usr-9",
  "correlationId": "sol-204",
  "data": {
    "reservaId": "res-104",
    "laboratorioId": "lab-2",
    "docenteId": "usr-9",
    "carreraId": "car-1",
    "asignaturaId": "asi-8",
    "periodoId": "per-2026-2",
    "academicoSnapshot": {"carrera": "Carrera de ejemplo", "periodo": "2026-2"},
    "inicio": "2026-11-30T14:00:00Z",
    "fin": "2026-11-30T16:00:00Z",
    "estado": "CONFIRMADA",
    "destinatarios": ["usr-9"]
  }
}
```

IDs y valores del ejemplo son ficticios y no son evidencia de ejecución. `sourceSequence` es una secuencia durable por origen para recuperación; no se interpreta como orden de llegada global. `aggregateVersion` aumenta por entidad; cada evento proyectable incluye la instantánea completa necesaria para reemplazar la versión anterior. `destinatarios` procede del origen autorizado; Avisos no acepta destinatarios suministrados por un cliente sin validar.

## Colas y recuperación

- `avisos.proyecciones.v1`: enlaces a eventos de reservas, asistencia, bloqueos, laboratorio, incidencias, equipos y académico; actualiza las copias para reportes.
- `avisos.notificaciones.v1`: enlaces a confirmación/cancelación/conflicto/bloqueo/incidencia y a los cambios de Identidad que deban informarse. La unicidad es `(eventId,destinatario,tipoAviso)`.
- `avisos.auditoria.v1`: enlace `auditoria.*.registrada.v1`. Unicidad `(source,operacionId)` más `InboxProcesado` por consumidor.
- Cada cola tiene su cola de errores `*.errores`. El consumidor copia a ella con confirmación y después ACK del original. Reintentos transitorios propuestos: 1, 5, 15 y 60 segundos; quinto fallo a errores. Las demoras se programan fuera de la transacción SQL, sin bloquear una petición del usuario. Si falla el traslado, no se da ACK.
- Marcar procesado, actualizar proyección/aviso/auditoría y guardar el nuevo estado de sincronización ocurre en una misma transacción. Un reinicio antes del commit revierte todo; después del commit y antes del ACK causa reentrega y deduplicación.
- Las proyecciones aplican solo una versión mayor por agregado. Un evento con versión vieja se registra en inbox sin revertir el estado. Cada acción de auditoría y cada aviso se deduplican por identidad del evento, no por la versión más reciente del agregado.
- `actualizadoHastaPorOrigen` refleja un corte comprobado de los eventos relevantes para cada consumidor, no el mayor número visto. Cada cola recibe un subconjunto de tipos: un salto en `sourceSequence` no demuestra por sí solo pérdida. El origen debe conservar y exponer un manifiesto paginado de IDs relevantes por consumidor y corte; Avisos coteja ese manifiesto con su inbox antes de avanzar la marca de agua contigua. Sin reconciliación confirmada, muestra el último evento recibido y estado no verificado, sin afirmar que el origen está completo. Heartbeats identifican cortes nuevos; una ausencia real mantiene el estado degradado y requiere recuperar el evento del registro durable.
- Para carga inicial o pérdida de proyecciones, el origen debe ofrecer una exportación consistente de su propiedad con secuencia de corte. Se restauran instantáneas y luego cambios posteriores retenidos. Auditoría necesita un archivo completo de acciones, no solo la última instantánea del agregado. Conservar mensajes pendientes y registro durable al menos siete días es una política inicial propuesta; no purgar antes de respaldar y confirmar los cursores de los consumidores. Si se supera la retención, hay que hacer reconciliación completa y mostrar la carencia; no afirmar recuperación automática.
- Propuesta operativa: publisher confirms, `mandatory`, almacenamiento persistente, alertas de backlog/disco/errores y credenciales por publicador/consumidor con TLS. Definir RPO, copia de seguridad y restauración con el equipo. Un nodo en desarrollo no tolera perder su disco; tres nodos con quorum se evalúan si el despliegue necesita tolerancia de nodos.

## Justificación por RNF

| Requisito real | Cómo influye en el diseño | Límite de la afirmación |
| --- | --- | --- |
| RNF-01: 95 % de consultas correctas de disponibilidad en menos de 2 s; 50 usuarios, 10 min. | Avisos y sus reportes se sacan del camino de confirmación; no se consulta su proyección para autorizar ni reservar. | No hay medición del cumplimiento. |
| RNF-02: 99 % de sondas correctas antes de 5 s, una por minuto, dos semanas académicas. | Un consumidor caído no debería impedir la consulta de agenda. | Identidad, Gateway, Laboratorios y bases siguen siendo dependencias; no se promete alta disponibilidad. |
| RNF-03: sin operaciones de agenda si falta permiso, hay revocación o Identidad no puede verificar. | REST de autorización vigente y fallo cerrado. | Un JWT válido en Gateway no demuestra permiso vigente. |
| RNF-05: flujos evaluados sin servicios de nube pública. | PostgreSQL y RabbitMQ desplegables en entorno institucional. | Requiere probar despliegue y recursos. |
| RNF-06: diez reservas confirmadas y persistidas con Avisos detenido. | Outbox en Reservas y publicación fuera de su transacción de negocio. | Límite físico: capacidad de outbox y disco. |
| RNF-07: auditoría fuente en todos los cambios exitosos de agenda. | Negocio y auditoría local se guardan juntos, además de outbox. | La vista central es eventual y no sustituye la fuente. |

RNF-04 define los controles de interfaz web/móvil. El Gateway ofrece una entrada común para ambos clientes. REST conserva los contratos HTTP/JSON existentes; la adopción de gRPC se evaluaría con los resultados de PE-U1.

## Escenarios para validar en la implementación futura

| Escenario | Resultado esperado |
| --- | --- |
| Detener Avisos; enviar las diez reservas válidas de RNF-06. | Las diez quedan confirmadas y persistidas en Agenda; tienen auditoría local. Al reanudar Avisos aparecen las diez proyecciones y los avisos únicos. |
| Detener RabbitMQ durante una escritura válida. | El commit local guarda negocio, auditoría y outbox. Al recuperarlo se publica lo pendiente sin repetir el efecto de negocio. |
| Reiniciar consumidor después del commit y antes del ACK. | Se reentrega; inbox impide duplicar proyección, aviso y auditoría. |
| Entregar versión 3 antes de versión 2. | La proyección queda en 3; auditoría conserva ambas acciones únicas y los avisos previstos no se pierden por el filtro de versión. |
| Enviar esquema desconocido o payload inválido. | Va a errores con diagnóstico; no hay bucle de reencolado ni ACK antes de conservar la copia. |
| Quitar permiso o revocar sesión; cortar acceso a Identidad. | Peticiones protegidas denegadas o `503`, según el caso; no se usan permisos atrasados. |
| Consultar aviso de otro destinatario. | `404` y ningún cambio de lectura. |
| Reconstruir desde instantánea y secuencia de corte. | Las proyecciones coinciden con el origen para ese corte; se aplican después los eventos retenidos sin huecos ni duplicados. |

Estos son criterios de prueba propuestos.
