# Aporte de Belinda — Laboratorios y Recursos

- **Microservicio:** Laboratorios y Recursos.
- **Responsabilidad:** Administrar el catálogo de laboratorios, su equipamiento y las restricciones operativas que afectan el uso de los espacios.
- **Datos propios:** Laboratorios, equipos, tipos de equipo, historial de estados de equipos, bloqueos operativos y sus tipos, asignaciones de encargados, referencias a fotografías y eventos pendientes de publicación. Se almacenan en una base PostgreSQL propia.
- **Endpoints principales:**
  - `GET /laboratorios` y `POST /laboratorios` — consultar y registrar laboratorios.
  - `PATCH /laboratorios/{id}` — actualizar los datos de un laboratorio.
  - `GET /equipos` y `POST /equipos` — consultar y registrar equipos con número de serie único.
  - `PATCH /equipos/{id}/estado` — cambiar el estado de un equipo y guardar la fecha y el usuario responsable.
  - `GET /bloqueos` y `POST /bloqueos` — consultar y registrar bloqueos operativos.
  - `PATCH /bloqueos/{id}` — modificar o levantar un bloqueo.
  - `GET /laboratorios/{id}/condiciones-operativas` — consultar el estado del laboratorio y los bloqueos vigentes para un intervalo.
- **Eventos producidos:** `BloqueoLaboratorioRegistrado`, `BloqueoLaboratorioModificado` y `BloqueoLaboratorioLevantado`. Se publican en RabbitMQ para que Reservas y Agenda reevalúe las reservas afectadas.
- **Eventos consumidos:** Ninguno en el diseño actual.
- **RF atendidos:** RF-03 (catálogo de laboratorios), RF-07 (inventario), RF-08 (estado e historial de equipos) y RF-11 (registro de bloqueos operativos).
- **Aporte al RF-04:** Proporciona el estado del laboratorio y sus bloqueos vigentes. Reservas y Agenda combina esos datos con la ocupación de sus franjas para calcular la disponibilidad.