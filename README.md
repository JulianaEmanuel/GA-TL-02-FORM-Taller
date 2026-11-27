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

El archivo principal es `docs/informe/main.tex`. Se requiere una distribución LaTeX con pdfLaTeX, Biber y los paquetes `fontenc`, `babel` con español, `geometry`, `tabularx`, `array`, `graphicx`, `hyperref`, `biblatex` y el estilo IEEE.

Desde la raíz del proyecto, ejecuta en PowerShell:

```powershell
Set-Location docs/informe
pdflatex -interaction=nonstopmode -halt-on-error main.tex
biber main
pdflatex -interaction=nonstopmode -halt-on-error main.tex
pdflatex -interaction=nonstopmode -halt-on-error main.tex
```

El resultado se genera en `docs/informe/main.pdf`.
