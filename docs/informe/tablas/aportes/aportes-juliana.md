# Aportes de Juliana Emanuel

Microservicio a cargo: **Reservas y Agenda**. Completar todas las dimensiones del servicio, acordando las interfaces con los demás responsables.

| Campo | Aporte |
| --- | --- |
| Microservicio | Reservas y Agenda |
| Responsabilidad | Gestionar la ocupación de los laboratorios por franja y registrar asistencia. |
| Datos propios | Reservas, cancelaciones, bloqueos y registros de asistencia en la base de Agenda. |
| Endpoints y operaciones | `POST /api/reservas` (crear), `GET /api/reservas/disponibilidad` (consultar franjas), `GET /api/reservas/mias` (consultar reservas propias), `POST /api/reservas/{id}/cancelacion` (cancelar), `POST /api/agenda/bloqueos` (bloquear) y `POST /api/reservas/{id}/asistencia` (registrar asistencia). |
| Eventos producidos y consumidos | Propuesta de Información y Avisos: `ReservaConfirmada`, `ReservaCancelada`, `ConflictoReservaDetectado`, `AsistenciaRegistrada` y `BloqueoAgendaCambiado`. Queda por validar el contrato de bloqueos con Laboratorios. |
| RF cubiertos (asignación inicial por verificar) | RF-04 a RF-06, RF-11 y RF-12 |

Registrar también los contratos, decisiones y justificaciones que necesiten las secciones transversales. Cada servicio registra sus cambios para RF-14; Información y Avisos reúne la vista central.

Belinda consolida los aportes en `../tabla-servicios.tex`. Este Markdown es un insumo de trabajo; no se incluye automáticamente en LaTeX.

Para coordinar con Laboratorios: la propuesta de CLAB confirma primero el bloqueo en Agenda y publica después el laboratorio como fuera de servicio. Revisar con Belinda el contrato y la respuesta ante fallos.
