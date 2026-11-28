# ADR-003 Propuesta de eventos durables para Información y Avisos

- Fecha: 27 de noviembre de 2026.
- Estado: **propuesto**.
- Alcance: propagación de cambios hacia reportes, avisos y vista central de auditoría.
- Antecedentes: ADR-001 y ADR-002 del PFC. Verificar que el número 003 siga disponible al integrar.

## Contexto

RF-13 necesita reportes por carrera y periodo, RF-15 avisos internos y RF-14 consulta central de acciones. RNF-06 exige que diez reservas válidas se confirmen y persistan con Avisos detenido. RNF-07 exige auditoría en el servicio de origen incluso durante esa caída. RNF-05 impide depender de nube pública para los flujos evaluados. El ADR-002 propone sondeo REST de cambios durables; los borradores del taller ya contemplan RabbitMQ, todavía sin un contrato integrado.

## Alternativas

| Opción | Evaluación para CLAB |
| --- | --- |
| Consulta incremental REST | Mantiene la tecnología de E1 y permite procesamiento diferido, pero exige cursores, paginación, retención y recuperación en cada origen. Sigue siendo una alternativa válida si el equipo no acepta operar un broker. |
| RabbitMQ | Enrutamiento por evento y colas por consumidor; confirmaciones, acuses y colas de errores permiten diseñar recuperación. Añade infraestructura y supervisión. Se ajusta a distribución de avisos y actualización de proyecciones. |
| Kafka | Retención y relectura de un registro distribuido resultan útiles para muchos consumidores y grandes historiales. No se documentó en CLAB una carga ni un requisito de reproducción que justifique esa elección frente a la topología propuesta. No se afirma que sea más lento o siempre más complejo. |

## Decisión propuesta

1. Usar RabbitMQ local con AMQP sobre TLS para propagar eventos confirmados de Reservas, Laboratorios e Identidad a Información y Avisos. Mantener REST/JSON sobre HTTPS en las operaciones interactivas y de autorización.
2. Cada origen guarda **dato de negocio + auditoría local + outbox** en una sola transacción PostgreSQL. Un publicador lee después la outbox, envía mensaje persistente a un exchange durable, solicita publisher confirms y usa `mandatory` para detectar ausencia de rutas. Marca entregado solo con confirmación y sin devolución de enrutamiento.
3. Declarar exchange `clab.eventos.v1` de tipo topic y tres colas durables independientes: `avisos.proyecciones.v1`, `avisos.notificaciones.v1` y `avisos.auditoria.v1`. No compartir una cola entre funciones que deban recibir cada una el mismo evento.
4. El consumidor guarda el identificador procesado y sus efectos en la misma transacción de su base. Confirma con ACK solo después del commit. Los duplicados se reconocen por `(consumidor,eventId)`. Si se pierde el ACK, la reentrega no duplica efectos.
5. Usar mensajes con instantánea completa y versión por agregado. Las proyecciones ignoran versiones anteriores; auditoría conserva todas las acciones únicas y los avisos no se descartan solo por llegar fuera de orden. No existe orden global garantizado.
6. Reintentar errores transitorios con espera acotada; tras cinco intentos, copiar a una cola de errores con confirmación antes de dar ACK al original. Si la copia falla, no se da ACK. Mensajes inválidos van a revisión sin bucles de reencolado. La recuperación usa el mismo `eventId`.
7. Los reportes muestran fecha de actualización y estado de sincronización por origen. La caída de un consumidor o del broker retrasa proyecciones; los cambios locales continúan mientras haya espacio en outbox. Supervisar backlog, errores y capacidad. No se promete continuidad ilimitada con disco lleno.

Los acuses del broker no esperan que Avisos procese el mensaje: por eso el canal es asincrónico respecto del flujo de negocio.

## Límites que se conservan

La mensajería no autoriza operaciones ni decide la ocupación. RNF-03 conserva comprobación vigente en Identidad y fallo cerrado. Reservas es la única autoridad de reservas y bloqueos efectivos. El cierre operativo mantiene el protocolo I13 de ADR-001; un evento retrasado nunca es el mecanismo que impide confirmar una franja bloqueada.

No hay transacciones SQL, claves foráneas ni lecturas directas entre bases de distintos servicios. Información y Avisos posee copias de consulta y sus estados de lectura, no la verdad transaccional de reservas o usuarios.

## Consecuencias

Se desacopla la disponibilidad de los reportes del camino de confirmación (RNF-06), se conserva auditoría local (RNF-07) y se permite despliegue institucional (RNF-05). La latencia y disponibilidad de agenda siguen sujetas a RNF-01/RNF-02 y a sus pruebas: RabbitMQ por sí solo no demuestra esas metas.

La entrega es al menos una vez; se diseñan efectos idempotentes. El respaldo y la reconstrucción de proyecciones requieren instantáneas consistentes por origen con secuencia de corte y registro durable retenido. Un único nodo RabbitMQ no aporta alta disponibilidad; para tolerancia de nodos se debe evaluar un despliegue con quorum y recursos suficientes.

## Validación antes de aceptar

Ejecutar los escenarios de `docs/contratos/eventos-nixon.md`: caída de Avisos y broker, duplicados, pérdida del ACK, versiones fuera de orden, mensaje inválido, recuperación desde instantánea y acceso sin permiso. Acordar payloads, retención, tamaño de outbox y objetivo de frescura con los otros responsables. No se reportan estos ensayos como ejecutados.

## Referencias

- [RabbitMQ confirma publicación y consumo](https://www.rabbitmq.com/docs/confirms).
- [RabbitMQ colas de errores](https://www.rabbitmq.com/docs/dlx).
- [RabbitMQ quorum queues](https://www.rabbitmq.com/docs/quorum-queues).
- [ADR-002 antecedente](https://github.com/Danxxx777/clab-pfc-7a/blob/62c286a3064271df0119e3d14d17981a2520680a/docs/adr/ADR-002-mecanismo-comunicacion.md).
