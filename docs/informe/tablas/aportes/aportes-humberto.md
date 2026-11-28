# Aportes de Humberto Herrera

Microservicio a cargo: **Identidad y Académico**. Completar todas las dimensiones del servicio, acordando las interfaces con los demás responsables.

| Campo | Aporte |
| --- | --- |
| Microservicio | Identidad y Académico |
| Responsabilidad | Autenticar a los usuarios institucionales, emitir el JWT, gestionar roles y permisos, administrar las solicitudes de acceso y mantener la información académica (carreras, asignaturas, horarios y periodos) con la que se vinculan las reservas. |
| Datos propios | Base PostgreSQL propia. Identidad: Usuario, SesionActiva, Rol, Permiso, Modulo, UsuarioRol, RolPermiso, SolicitudAcceso. Académico: Carrera, Asignatura, HorarioAcademico, PeriodoAcademico, Estudiante, Matricula, InscripcionAsignatura. Organización: Sede y Facultad (por confirmar con el equipo). Además: registro de auditoría local y evento pendiente. |
| Endpoints y operaciones | `POST /auth/login`, `POST /auth/logout`, `GET /auth/mis-roles`; `GET/POST /usuarios`, `PATCH /usuarios/{id}/estado`; `POST /solicitudes`, `PATCH /solicitudes/{id}`; `GET/POST /roles`, `PUT /roles/{id}/permisos`; `GET/POST` sobre `/carreras`, `/asignaturas`, `/horarios` y `/periodos`. Consulta interna: `GET /usuarios/{id}/roles` (Laboratorios y Recursos, validación de encargado). |
| Eventos producidos y consumidos | Produce por RabbitMQ: `UsuarioDesactivado`, `RolUsuarioModificado`, `PeriodoAcademicoCerrado` y registros de auditoría (RF-14). Consume: ninguno. |
| RF cubiertos (asignación inicial por verificar) | RF-01, RF-02 y RF-16 |

## Contratos con otros servicios

| Servicio | Contrato | Tipo | Estado |
| --- | --- | --- | --- |
| API Gateway | Valida firma y vigencia del JWT; envía `usuario`, `rol` y `permisos` a los servicios. | REST sincrónico | Propuesto |
| Reservas y Agenda (Juliana) | Obtiene la identidad y los permisos del docente desde el token; no consulta a Identidad en cada operación. | Vía Gateway | Por validar |
| Laboratorios y Recursos (Belinda) | `GET /usuarios/{id}/roles` para validar al encargado antes de guardar la asignación. | REST sincrónico | Por validar |
| Información y Avisos (Nixon) | Consume los eventos de Identidad para avisos y la consulta central de auditoría. | RabbitMQ asincrónico | Por validar |

## Decisiones y justificaciones

- **Base propia:** las entidades salen de los esquemas `usuarios`, `academico` y `organizacion` del monolito y pasan a una base exclusiva \cite{laigner2021datamanagement}. `Auditoria` deja de estar centralizada en este servicio.
- **JWT validado en el Gateway:** el monolito ya usa JWT sin sesión (`usuario`, `rol`, `permisos`, vigencia de 1 hora). Validarlo en el Gateway evita consultar a Identidad en cada solicitud \cite{haindl2024interservice,waseem2022decision}.
- **Fallos:** si Identidad cae, los tokens ya emitidos siguen siendo válidos; fallan los nuevos inicios de sesión y la asignación de encargados.
- **Hallazgo del monolito:** en `SecurityConfig` la mayoría de rutas están en `permitAll` (incluidas `/roles/**`, `/carreras/**` y `/auditoria/**`); centralizar la autenticación en el Gateway lo corrige.
- **Pendiente de equipo:** unificar el estilo de rutas (`/api/...` en Reservas, sin prefijo en Laboratorios).

## Referencias aportadas (`referencias.bib`, bloque de Humberto)

- `haindl2024interservice`: Gateway, JWT y seguridad entre servicios.
- `velepucha2023survey`: principios, patrones y migración desde monolitos (introducción).
- `waseem2022decision`: autorización en el borde y tokens de acceso e identidad.
- `laigner2021datamanagement`: base de datos propia por servicio (útil para todo el equipo).

Cada servicio registra sus cambios para RF-14; Información y Avisos reúne la vista central.

Belinda consolida los aportes en `../tabla-servicios.tex`. Este Markdown es un insumo de trabajo; no se incluye automáticamente en LaTeX.