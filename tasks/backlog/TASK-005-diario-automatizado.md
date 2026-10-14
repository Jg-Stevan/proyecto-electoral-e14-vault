---
id: TASK-005
titulo: "Diario automatizado: entrada por cada tarea done"
estado: backlog
fase: -
ultimo_agente: planificador
ultima_actualizacion: 2026-10-14
---

# TAREA — Diario automatizado

## Objetivo
Definir y ensayar la rutina: cada tarea que pasa a `done` genera una entrada en `07 DIARIO/` usando la plantilla de diario de `08 PLANTILLAS/`.

## Contexto mínimo
- `agent-docs/continuidad.md` §5 ya exige avisar antes de actualizar el diario.
- Regla #1 de `AGENTS.md`: no crear carpetas nuevas; se usan la carpeta y la plantilla existentes.

## Alcance
**Incluye:**
- Definir el formato mínimo de la entrada (fecha · tarea · resultado · decisión si aplica) y ensayarlo con la próxima tarea que pase a `done`.
**NO incluye:**
- Automatización por script (eso sería una tarea nueva con su propia spec).

## Whitelist de archivos (cerrada)
- `tasks/backlog/TASK-005-diario-automatizado.md` (bitácora)
- `07 DIARIO/<nota nueva del día>` (avisando antes, regla de continuidad §5)
- `agent-docs/decisiones/DEC-###-diario-rutina.md` (si el dueño formaliza la rutina)

## Inmutables (diff CERO)
- Todo lo demás del vault, incluida `08 PLANTILLAS/`.

## Pasos
1. Elegir la próxima tarea `done` como piloto.
2. Redactar la entrada con la plantilla de `08 PLANTILLAS/`.
3. Avisar al dueño antes de guardar.
4. Evaluar si la rutina sirve; si sí, proponerla como decisión (DEC-###).

## Gates
- La nota diaria usa la plantilla existente sin inventar secciones nuevas.

## Criterios de aceptación
- [ ] Una tarea `done` tiene su entrada en `07 DIARIO/` y el dueño la encontró útil.

## Cómo probar
1. Abrir `07 DIARIO/` y leer la entrada piloto.

## Pendientes / dudas abiertas
-

---
## Bitácora de la tarea
- 2026-10-14 · planificador · Tarea creada en el backlog inicial del kit.
