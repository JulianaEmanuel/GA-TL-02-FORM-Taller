# Reparto de trabajo — GA-TL-02

[Volver al README del repositorio](README.md).

**Grupo:** EHPT · **Semana 5:** 23 al 29 de noviembre de 2026.

**Sistema:** Gestión de Reservas de Laboratorio (CLAB).

Este repositorio combina el trabajo por microservicio con un informe grupal organizado por secciones. La raíz ya corresponde al taller; no se añade otra carpeta `GA-TL-02/` dentro de ella. Si se integra en el fork del PFC, esta estructura puede ubicarse bajo `GA-TL-02/`.

## 1. Responsabilidad principal por microservicio

| Integrante | Microservicio | Carpeta de implementación | Diseño del servicio |
| --- | --- | --- | --- |
| Juliana Romina Emanuel Pino | Reservas y Agenda | `src/microservicio_reservas/` | `docs/informe/microservicios/reservas.tex` |
| Humberto Aldair Herrera Barco | Identidad y Académico | `src/microservicio_identidad/` | `docs/informe/microservicios/identidad.tex` |
| Belinda Betzabeth Toaquiza Zambrano | Laboratorios y Recursos | `src/microservicio_laboratorios/` | `docs/informe/microservicios/laboratorios.tex` |
| Nixon Antonio Piguave Montaño | Información y Avisos | `src/microservicio_avisos/` | `docs/informe/microservicios/avisos.tex` |

Cada integrante diseña su servicio de forma completa: alcance y responsabilidad única, entidades y base de datos propia, componentes y dependencias, endpoints, interacciones síncronas, eventos producidos y consumidos, RF cubiertos y justificación de decisiones. También aporta el bosquejo fechado, su diagrama parcial y la información de su fila en la tabla de servicios.

Los nombres e identificadores comunes se mantienen en [el catálogo](docs/catalogo-microservicios.md). Las carpetas `src/` conservan sus nombres y actualmente contienen marcadores `.gitkeep`; esta reorganización no añade implementaciones.

## 2. Coordinación de aspectos compartidos

| Coordinador | Integración grupal |
| --- | --- |
| Juliana | Catálogo común, coherencia de límites y propiedad de datos; introducción |
| Humberto | API Gateway, contratos y comunicación sincrónica; conclusiones con aportes de todos |
| Nixon | Eventos y comunicación asincrónica; integración del bosquejo grupal y del C4 nivel 3 |
| Belinda | Coherencia de componentes, consolidación de la tabla y verificación de cobertura funcional |

Coordinar significa reunir y comprobar los aportes de los cuatro servicios. No sustituye la responsabilidad de cada estudiante sobre el diseño completo de su microservicio. Los contratos entre dos servicios se acuerdan entre sus responsables.

## 3. Informe por secciones

Todos aportan a las secciones técnicas desde su propio microservicio. Cada coordinador edita el archivo transversal para mantener un único texto coherente y evitar conflictos.

| Sección | Archivo dentro de `docs/informe/` | Coordinación |
| --- | --- | --- |
| Portada | `secciones/portada.tex` | Equipo |
| 1. Introducción | `secciones/introduccion.tex` | Juliana |
| 2. Diseño de la arquitectura | `secciones/arquitectura.tex` | Nixon y Belinda |
| 2.1. Microservicios y propiedad de datos | `secciones/microservicios-datos.tex` | Juliana |
| 2.2. Componentes internos | `secciones/componentes-internos.tex` | Belinda; incluye los cuatro archivos de `microservicios/` |
| 2.3. API Gateway | `secciones/api-gateway.tex` | Humberto |
| 2.4. Comunicación sincrónica | `secciones/comunicacion-sincronica.tex` | Humberto |
| 2.5. Comunicación asincrónica | `secciones/comunicacion-asincronica.tex` | Nixon |
| 2.6. Diagrama C4 integrado | Dentro de `secciones/arquitectura.tex` | Nixon |
| 3. Tabla de servicios y trazabilidad | `tablas/tabla-servicios.tex` | Belinda; aportes de todos |
| 4. Conclusiones | `secciones/conclusiones.tex` | Humberto; aportes de todos |
| Referencias bibliográficas | `referencias.bib` | Todos |
| Anexos y evidencias | `secciones/anexos.tex` | Todos |

Los detalles de componentes se escriben en `microservicios/<servicio>.tex`; las secciones transversales sintetizan el diseño sin repetirlo. Las fuentes se añaden al bloque de cada integrante en `referencias.bib` usando claves únicas.

## 4. Tabla de servicios y flujo de aportes

Cada estudiante completa **todas las columnas de su propio servicio** en `docs/informe/tablas/aportes/aportes-<nombre>.md`: responsabilidad, datos propios, endpoints, eventos y RF cubiertos. Los RF iniciales del repositorio se conservan como punto de partida y requieren verificación.

Belinda integra los cuatro aportes en `tablas/tabla-servicios.tex` y verifica que no queden RF sin asignar ni servicios sin justificación. Los archivos Markdown son insumos manuales; no se convierten automáticamente a LaTeX. La tabla del informe se divide en dos bloques vinculados por el nombre del servicio para que sea legible.

## 5. Estructura integrada

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
    ├── evidencias/                       # Fotos fechadas individuales y grupal
    ├── diagramas/
    │   ├── reservas-crear-reserva.drawio  # Borrador existente conservado
    │   ├── creacion-reserva.png           # Imagen existente conservada
    │   └── parciales/                    # Un diagrama por microservicio
    │       └── README.md
    ├── adr/                             # Carpeta previa conservada
    └── informe/
        ├── main.tex
        ├── referencias.bib
        ├── secciones/                   # Secciones del informe grupal
        ├── microservicios/              # Aportes de componentes por servicio
        │   ├── reservas.tex
        │   ├── identidad.tex
        │   ├── laboratorios.tex
        │   └── avisos.tex
        ├── tablas/
        │   ├── tabla-servicios.tex
        │   └── aportes/
        │       ├── aportes-juliana.md
        │       ├── aportes-humberto.md
        │       ├── aportes-belinda.md
        │       └── aportes-nixon.md
        └── archivo/
            └── retroalimentacion.tex    # Plantilla previa fuera del informe actual
```

Todos los `.tex` de la entrega están enlazados por `\input{}` desde `main.tex`, directa o indirectamente. Las imágenes pendientes usan marcadores condicionales, de modo que no bloquean la compilación. Los diagramas y fotos pendientes no se crean como archivos vacíos.

El contenido existente de Reservas, la referencia bibliográfica, la portada y los RF se conservan. Los cuatro archivos por servicio antes situados directamente en `docs/informe/` se trasladaron a `docs/informe/microservicios/`. La plantilla previa de retroalimentación se conserva en `archivo/`, sin incluirla en la entrega actual.

## 6. Entregables previstos

- Fotos `docs/evidencias/bosquejo-{juliana,humberto,belinda,nixon,grupal}.jpg`, con fecha visible.
- Cuatro diagramas parciales editables, uno por microservicio; nombres y responsables en `docs/diagramas/parciales/README.md`.
- C4 integrado: `docs/diagramas/c4-nivel-3.drawio` y `docs/diagramas/c4-nivel-3.pdf`.
- Informe grupal LaTeX y PDF con tabla, decisiones y justificaciones tecnológicas.
- Commits individuales de cada integrante para registrar sus aportes.
- PDF de una página para el SGA, elaborado aparte y fuera de la carpeta del taller.

No se incorpora la revisión de diseño ni nuevos ADR como requisito de esta entrega. La integración al repositorio original es opcional; la aceptación del fork como entrega queda pendiente de confirmar con el docente.
