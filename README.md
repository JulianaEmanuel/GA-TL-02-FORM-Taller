# GA-TL-02-FORM-Taller

Taller de diseño de la arquitectura de microservicios del Sistema de Gestión de Reservas de Laboratorio (CLAB).

## Organización del trabajo

La asignación de microservicios, responsabilidades, coordinación, secciones del informe y entregables se encuentra en [reparto.md](reparto.md).

El código se organiza por microservicio en `src/` y la documentación en `docs/`. El [catálogo de microservicios](docs/catalogo-microservicios.md) mantiene los nombres e identificadores comunes.

## Fuente del diseño

El taller toma como punto de partida la arquitectura propuesta para el Sistema de Gestión de Reservas de Laboratorio (CLAB). El C4 de nivel 2 y el ADR-001 sirvieron para identificar los cuatro microservicios y sus responsabilidades. Las historias de usuario de la Entrega 1 sirvieron para relacionarlos con los requisitos funcionales.

- [Repositorio del PFC de CLAB](https://github.com/Danxxx777/clab-pfc-7a)
- [Diagramas C4](https://github.com/Danxxx777/clab-pfc-7a/blob/main/docs/diagramas/c4.md)
- [ADR-001 y propiedad de datos](https://github.com/Danxxx777/clab-pfc-7a/blob/main/docs/adr/ADR-001-estilo-arquitectonico.md)
- [Historias de usuario y requisitos](https://github.com/Danxxx777/clab-pfc-7a/blob/main/docs/Byron.tex#L197)

## Compilación del informe

El archivo de entrega breve es `docs/informe/main.tex`; el desarrollo de apoyo se compila con `docs/informe/main-detallado.tex`. La explicación por sección y el flujo de aportes están en [reparto.md](reparto.md). Se requiere una distribución LaTeX con pdfLaTeX, Biber y los paquetes `fontenc`, `babel` con español, `geometry`, `tabularx`, `array`, `graphicx`, `hyperref`, `biblatex` y el estilo IEEE.

Desde la raíz del proyecto, ejecuta en PowerShell:

```powershell
Set-Location docs/informe
pdflatex -interaction=nonstopmode -halt-on-error main.tex
biber main
pdflatex -interaction=nonstopmode -halt-on-error main.tex
pdflatex -interaction=nonstopmode -halt-on-error main.tex
```

El resultado se genera en `docs/informe/main.pdf`. El objetivo editorial es 2–3 páginas de contenido, sin portada independiente ni anexos fotográficos; las referencias pueden extender el PDF hasta una cuarta página si es necesario. El diagrama integrado y su exportación se entregan por separado en `docs/diagramas/`.

Para compilar el documento de apoyo, repetir la misma secuencia sustituyendo `main.tex` por `main-detallado.tex` y `biber main` por `biber main-detallado`. Los desarrollos completos permanecen en `docs/informe/microservicios/`.

La tabla de retroalimentación está activa en `docs/informe/tablas/retroalimentacion.tex`. La tabla registra que no se realizó revisión docente; esta actividad no se mantiene como pendiente para esta entrega. La entrega final debe integrarse en el repositorio público del PFC usado en la entrega 1.

El informe sigue siendo un borrador: faltan aportes y validaciones del equipo. Compilarlo no implica que los requisitos de entrega estén completos.
