# Aporte de Nixon Antonio Piguave Montaño

**Servicio: Información y Avisos. Estado: propuesta de diseño para validación del equipo.**

| Campo | Aporte |
| --- | --- |
| Microservicio | Información y Avisos (`microservicio_avisos`). |
| Responsabilidad y SRP | Producir información derivada para consulta: reportes de uso, avisos internos y vista central de auditoría. Cambia por reglas de presentación, distribución y actualización de resultados de CLAB. No decide permisos, ocupación ni estado técnico. |
| Datos propios | PostgreSQL `clab_avisos` exclusivo: proyecciones de reservas, asistencia, laboratorio, incidencias y académico; notificación y destinatario/lectura; copia central de auditoría; auditoría local, inbox, estado de sincronización y outbox. Identificadores externos sin FK remotas ni acceso SQL a otras bases. |
| Componentes | API REST y control de acceso; consultas de uso/exportación; gestión de avisos; consulta de auditoría; consumidores y proyector; repositorios; publicador de outbox. Spring Boot propuesto, con PostgreSQL y RabbitMQ. |
| Endpoints | `GET /api/reportes/uso?carreraId=&periodoId=`, `GET /api/reportes/uso/exportacion?formato=csv`, `GET /api/notificaciones`, `PATCH /api/notificaciones/{id}/lectura`, `GET /api/auditoria`. Todas las consultas son paginadas y protegidas. Solo el destinatario puede leer/marcar su aviso. |
| Eventos consumidos | De Reservas: `ReservaConfirmada`, `ReservaCancelada`, `ConflictoReservaDetectado`, `AsistenciaRegistrada`, `BloqueoAgendaCambiado`. De Laboratorios: `LaboratorioActualizado`, `IncidenciaActualizada`, `EquipoEstadoActualizado`. De Identidad: `AcademicoActualizado`, `UsuarioDesactivado`, `RolUsuarioModificado`, `PeriodoAcademicoCerrado`. De los cuatro: `AccionAuditada`. |
| Eventos producidos | `AccionAuditada` para las modificaciones propias, por ejemplo marcar un aviso leído. No vuelve a publicar lo consumido para evitar bucles. |
| Canales sincrónicos | Web/móvil → Gateway → API, REST/JSON HTTPS. Avisos → Identidad para autorización vigente. Si Identidad no verifica, `503` sin ejecutar; si no hay permiso, `403`. El diseño concreto del Gateway lo coordina Humberto. |
| Canal asincrónico | Orígenes → RabbitMQ → tres consumidores de Avisos: proyecciones, notificaciones y auditoría. AMQP/TLS, exchange topic durable, colas durables, outbox, confirms, ACK tras commit e inbox por consumidor/evento. |
| RF atendidos | RF-13: filtro por carrera/periodo y consulta/exportación. RF-15: avisos internos por destinatario. RF-14: consulta central; cada origen conserva su auditoría transaccional y Avisos registra sus propias mutaciones. |
| RNF que justifican las decisiones | RNF-06: Reservas continúa con Avisos detenido. RNF-07: auditoría local independiente de la vista central. RNF-05: despliegue institucional. RNF-03: autorización vigente; JWT aislado no basta. RNF-01/02: se elimina la espera de Avisos en agenda, sin afirmar cumplimiento medido. |
| Fallos y consistencia | Caída de Avisos o broker retrasa las copias; origen conserva outbox mientras exista capacidad. Duplicados deduplicados, versiones antiguas no revierten proyección, errores a cola de revisión. Reportes indican su actualización. Base de Avisos caída: `503`, sin fingir datos vacíos. |

## Contratos que debe validar cada responsable

| Responsable | Acuerdo propuesto | Estado real |
| --- | --- | --- |
| Juliana | Eventos de reservas/asistencia y bloqueo efectivo, con instantánea académica y destinatarios. Reservas conserva autoridad exclusiva sobre ocupación. | Propuesto; pendiente de validación. |
| Belinda | Eventos de catálogo/equipos/incidencias. El cierre operativo usa I13 antes de anunciar fuera de servicio; no se impone el bloqueo mediante evento atrasado. | Propuesto; pendiente de validación. |
| Humberto | Eventos de Identidad; REST de autorización vigente y prefijos del Gateway. Sede/Facultad deben acordarse, no duplicarse como datos originales. | Propuesto; pendiente de validación. |
| Todos | Auditoría local + outbox; instantáneas/secuencia de corte, retención y recuperación. | Propuesto; pendiente de validación. |

Detalle en `docs/contratos/eventos-nixon.md`. Integración gráfica en `docs/diagramas/c4-nivel-3.drawio`; contiene una vista general y una página C3 por servicio.

Esta tabla reúne el contenido del servicio para incorporarlo a la tabla grupal.
