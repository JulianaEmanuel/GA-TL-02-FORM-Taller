# Aportes de Humberto Herrera

Microservicio a cargo: **Identidad y Académico**. Completar todas las dimensiones del servicio, acordando las interfaces con los demás responsables.

| Campo                                           | Aporte                                                                                                                                                                                                                                                                 |
| ----------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Microservicio                                   | Identidad y Académico                                                                                                                                                                                                                                                  |
| Responsabilidad                                 | Autenticar usuarios, gestionar roles y permisos, y mantener la información académica (carreras, asignaturas, horarios, periodos).                                                                                                                                      |
| Datos propios                                   | Usuario, Rol, Permiso, RolPermiso, UsuarioRol, Modulo, SesionActiva, SolicitudAcceso; Carrera, Asignatura, HorarioAcademico, PeriodoAcademico, Estudiante, Matricula, InscripcionAsignatura. Incluye o no Sede y Facultad, según lo que acuerden. No pongas Auditoria. |
| Endpoints y operaciones                         | POST /api/auth/login, /logout; /api/usuarios, /api/roles, /api/permisos; /api/carreras, /api/asignaturas, /api/horarios, /api/periodos; además, el endpoint que necesita Belinda para validar al encargado (ver la tabla siguiente).                                   |
| Eventos producidos y consumidos                 | Produce: usuario desactivado, rol o permiso modificado y periodo académico cerrado, para Avisos y para la auditoría central (RF-14). Consume: ninguno. Valida estos eventos con Nixon.                                                                                 |
| RF cubiertos (asignación inicial por verificar) | RF-01, RF-02 y RF-16.                                                                                                                                                                                                                                                  |

Registrar también los contratos, decisiones y justificaciones que necesiten las secciones transversales. Cada servicio registra sus cambios para RF-14; Información y Avisos reúne la vista central.

Belinda consolida los aportes en `../tabla-servicios.tex`. Este Markdown es un insumo de trabajo; no se incluye automáticamente en LaTeX.
