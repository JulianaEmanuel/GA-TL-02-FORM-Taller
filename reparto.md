# Reparto de trabajo — GA-TL-02

[Volver al README](README.md).

**Grupo:** EHPT · **Semana 5:** 23 al 29 de noviembre de 2026.
**Sistema:** Gestión de Reservas de Laboratorio (CLAB).

## 1. Cada estudiante sigue a cargo de un microservicio completo

| Integrante | Microservicio | Implementación | Desarrollo de apoyo dentro de `docs/informe/` |
| --- | --- | --- | --- |
| Juliana Romina Emanuel Pino | Reservas y Agenda | `src/microservicio_reservas/` | `microservicios/reservas.tex` |
| Humberto Aldair Herrera Barco | Identidad y Académico | `src/microservicio_identidad/` | `microservicios/identidad.tex` |
| Belinda Betzabeth Toaquiza Zambrano | Laboratorios y Recursos | `src/microservicio_laboratorios/` | `microservicios/laboratorios.tex` |
| Nixon Antonio Piguave Montaño | Información y Avisos | `src/microservicio_avisos/` | `microservicios/avisos.tex` |

Cada integrante desarrolla alcance y SRP, datos propios y base independiente, componentes, endpoints, canales sincrónicos, eventos producidos/consumidos, RF atendidos y justificación según RNF. También aporta el bosquejo fechado y su diagrama parcial. Los nombres comunes están en [el catálogo](docs/catalogo-microservicios.md).

Las responsabilidades por servicio se mantienen. Las coordinaciones de las siguientes secciones consisten en reunir, sintetizar y comprobar los aportes de todos. Los contratos entre servicios se acuerdan entre sus responsables.

## 2. Dos documentos: entrega breve y desarrollo de apoyo

- **`docs/informe/main.tex` → `main.pdf`:** informe breve de entrega. Objetivo editorial: 2–3 páginas legibles de contenido; las referencias pueden ocupar una cuarta página si es necesario. La rúbrica no fija un número exacto. Incluye contexto, decisiones, tabla de servicios, tabla de retroalimentación, cierre y referencias citadas.
- **`docs/informe/main-detallado.tex` → `main-detallado.pdf`:** material de apoyo para el equipo. Incluye portada, los desarrollos completos de los cuatro servicios y anexos. No sustituye el informe breve.
- **`docs/diagramas/`:** C4 integrado editable y exportación independiente. No insertar una imagen grande del C4 dentro de `main.tex`.
- **`docs/evidencias/`:** bosquejos fechados; el informe breve indica su ubicación y no reproduce las fotografías.

`main.tex` solo organiza las inclusiones. Cada coordinador escribe en el archivo de su sección. Los detalles extensos permanecen en `microservicios/`; no añadir sus `\input{}` al informe breve. La portada y los anexos solo se incluyen en el documento de apoyo.

**Estado actual:** es un borrador. Los campos «Por consolidar» señalan información pendiente; no son contenido final de entrega. Los resúmenes de Reservas y Laboratorios se obtuvieron de los aportes existentes. Sus responsables deben validarlos antes de la entrega.

## 3. Qué escribe cada compañero en el informe breve

Todas las rutas de esta tabla parten de `docs/informe/`. Las extensiones son orientativas; priorizar que las tablas se lean y que se cumpla la rúbrica.

| Parte y archivo | Coordinación | Qué debe quedar escrito | Extensión orientativa |
| --- | --- | --- | --- |
| Encabezado — `secciones/encabezado.tex` | Equipo | Título, integrantes y grupo en la primera página. Retirar el aviso de borrador únicamente cuando se hayan cerrado los pendientes. | Un bloque compacto |
| Contexto — `secciones/introduccion.tex` | Juliana | Indicar el sistema, los C4 de niveles 1 y 2 utilizados y dónde consultar diagrama y evidencia. Sin teoría extensa. | Un párrafo, 50–70 palabras |
| Arquitectura — `secciones/arquitectura.tex` | Nixon y Belinda | Ordenar las cinco síntesis de las filas siguientes y comprobar que coinciden con el C4. Este archivo no incluye desarrollos completos. | Solo composición |
| Límites y datos — `secciones/microservicios-datos.tex` | Juliana; aportes de los cuatro | Explicar SRP, base propia por servicio y ausencia de bases compartidas. Las entidades concretas van en la tabla, sin repetirlas aquí. | Hasta 50 palabras |
| Componentes — `secciones/componentes-internos.tex` | Belinda; aportes de los cuatro | Resumir la organización interna y las dependencias relevantes. No transcribir cada componente y endpoint: deben verse en el C4 y en el desarrollo de apoyo. | Hasta 50 palabras |
| Gateway — `secciones/api-gateway.tex` | Humberto | Entrada única web/móvil, enrutamiento, autenticación y limitación de solicitudes. Concretar el mecanismo elegido y el comportamiento frente a errores. | Hasta 50 palabras |
| Canales sincrónicos — `secciones/comunicacion-sincronica.tex` | Humberto; validación de los responsables de ambos extremos | Identificar quién llama a quién, REST o gRPC, el motivo ligado al ID real de un RNF y qué ocurre ante fallos. Usar PE-U1 si se justifica gRPC. | Hasta 80 palabras |
| Eventos y consistencia — `secciones/comunicacion-asincronica.tex` | Nixon; validación de productores y consumidores | Productor/consumidor, eventos, RabbitMQ o Kafka, justificación según RNF y manejo de reintentos, duplicados y consistencia. | Hasta 90 palabras |
| Tabla de servicios — `tablas/tabla-servicios.tex` | Belinda consolida; cada estudiante completa su servicio | Responsabilidad, datos, endpoints/eventos y RF concretos. Mantener nombres coherentes entre ambos bloques. | Aproximadamente una página |
| Retroalimentación — `tablas/retroalimentacion.tex` | Belinda | Mantener el registro de que no se realizó revisión docente, sin inventar observaciones ni dejarlo como tarea pendiente. | Una fila de estado |
| Cierre — `secciones/conclusiones.tex` | Humberto, con aportes de todos | Resumir el resultado y sus límites. Al terminar, reemplazar el texto de trabajo pendiente por las conclusiones verificadas. | Hasta 40 palabras |
| Referencias — `referencias.bib` | Todos | Fuentes utilizadas, con claves únicas y citas junto a la decisión que respaldan. El PDF solo imprime las citadas. Las referencias pueden ocupar una cuarta página si es necesario. | Las necesarias para respaldar el diseño |

No eliminar una justificación necesaria para alcanzar un número de páginas. Primero quitar repeticiones; no reducir la letra de las tablas hasta hacerlas ilegibles.

## 4. Flujo de trabajo de cada integrante

1. Completar su desarrollo en `microservicios/<servicio>.tex`, incluyendo el tratamiento de fallos y la consistencia de datos.
2. Completar **todas** las columnas de su servicio en `tablas/aportes/aportes-<nombre>.md`. Estos Markdown son insumos manuales, no se convierten automáticamente a LaTeX.
3. Preparar el diagrama parcial en `docs/diagramas/parciales/` y acordar con el otro responsable cada contrato entre servicios.
4. Comunicar al coordinador de cada sección la síntesis, los IDs de RF/RNF y las fuentes que sustentan su propuesta. Evitar que varias personas reescriban a la vez una sección transversal.
5. El coordinador incorpora la síntesis al informe; Belinda integra la tabla y Nixon integra el C4. Cada responsable revisa que su servicio esté representado correctamente.
6. Compilar el informe breve y comprobar que ninguna sección haya incorporado el desarrollo extenso por error. Registrar los aportes con commits individuales.

**Atención a Reservas:** el contenido más reciente que estaba en `secciones/reservas.tex` se consolidó en `microservicios/reservas.tex`. La ruta anterior solo delega al archivo vigente; no mantener dos versiones.

**Acuerdo pendiente entre Reservas y Laboratorios:** delimitar los bloqueos de agenda y los bloqueos operativos, su propiedad y cómo se propagan. Los borradores mencionan ambos; antes de entregar se debe verificar que la tabla y el C4 no atribuyan el mismo dato a dos servicios como propietarios.

## 5. Estado de la retroalimentación

Para esta entrega no se realizará revisión docente, según lo indicado por el equipo. La tabla `docs/informe/tablas/retroalimentacion.tex` conserva el registro de que no hubo observaciones docentes, en texto normal. No se deja la revisión ni la creación de ADR derivados de ella como trabajo pendiente.

La referencia bibliográfica sobre ADR se conserva como sustento de la documentación de decisiones; no implica que se haya realizado una revisión o que se hayan creado nuevos ADR.

## 6. Tabla y trazabilidad: revisión de Belinda y del equipo

La tabla del informe usa dos bloques relacionados por el mismo nombre de servicio para conservar legibilidad. Entre ambos deben cubrir las seis columnas de la rúbrica: microservicio, responsabilidad, datos propios, endpoints, eventos y RF.

Comprobar que todos los RF del PFC estén asignados y que cada servicio atienda al menos uno. La asignación inicial del catálogo se debe verificar contra la entrega 1. Para RF-14, cada servicio registra sus cambios e Información y Avisos reúne la vista central. Los RNF deben identificarse con sus IDs reales al justificar los mecanismos; no inventarlos.

## 7. Estructura del trabajo

```text
.
├── README.md
├── reparto.md
├── src/
│   ├── microservicio_reservas/
│   ├── microservicio_identidad/
│   ├── microservicio_laboratorios/
│   └── microservicio_avisos/
└── docs/
    ├── catalogo-microservicios.md
    ├── evidencias/
    ├── diagramas/
    │   ├── reservas-crear-reserva.drawio
    │   ├── creacion-reserva.png
    │   └── parciales/
    ├── adr/
    └── informe/
        ├── main.tex                      # Entrega breve
        ├── main-detallado.tex            # Apoyo del equipo
        ├── preambulo.tex                 # Configuración común
        ├── referencias.bib
        ├── secciones/                    # Síntesis; portada/anexos solo para apoyo
        ├── microservicios/               # Desarrollos completos de los cuatro
        └── tablas/
            ├── tabla-servicios.tex
            ├── retroalimentacion.tex    # Recuperada e incluida en main.tex
            └── aportes/
                ├── aportes-juliana.md
                ├── aportes-humberto.md
                ├── aportes-belinda.md
                └── aportes-nixon.md
```

Los cuatro servicios de `src/` mantienen sus nombres. Los diagramas y fotos existentes se conservan. Los archivos finales del C4 se incorporarán al cerrar el diseño; no crear editables o imágenes vacíos para simular entregables.

## 8. Entrega según la rúbrica compartida

- C4 nivel 3 integrado editable (`.drawio`) y exportación PDF o PNG en `docs/diagramas/`, con bases independientes, Gateway, componentes y canales identificados como sincrónicos/asincrónicos y sus tecnologías.
- LaTeX breve compilado a PDF con tabla de servicios, tabla de retroalimentación y justificaciones relevantes.
- Fotografía del bosquejo inicial **grupal** hecho a mano, con fecha visible. Los bosquejos individuales pueden conservarse como apoyo. La evidencia de Belinda ya utiliza la extensión `.jpeg`; respetar ese nombre.
- Versión final en el **repositorio público del PFC utilizado en la entrega 1**, como exige la rúbrica. Este repositorio del taller sirve para preparar el trabajo; publicar aquí no sustituye esa integración final.

La rúbrica compartida no exige un segundo PDF de una página para SGA. Si el docente lo solicita por otra vía, prepararlo como entregable adicional, sin confundirlo con el informe breve.

## 9. Pendientes de archivos de entrega

Estos avisos se controlan aquí para mantener limpio el contexto de ambos PDF. Actualizar la lista al incorporar cada archivo.

- [ ] **Nixon, con validación de todos:** incorporar el C4 nivel 3 integrado editable en `docs/diagramas/c4-nivel-3.drawio`.
- [ ] **Nixon:** incorporar su exportación en `docs/diagramas/c4-nivel-3.pdf` o `docs/diagramas/c4-nivel-3.png`.
- [ ] **Equipo; Nixon integra la evidencia:** incorporar la fotografía del bosquejo grupal con fecha visible en `docs/evidencias/bosquejo-grupal.jpg`, `.jpeg` o `.png`.

Los diagramas parciales existentes no sustituyen el C4 integrado. En el documento detallado, los anexos siguen señalando las fotografías que faltan.
