---
id: TASK-004
titulo: "Revisar los 2 PDFs de actas reales en 06 RECURSOS (privacidad)"
estado: backlog
fase: -
ultimo_agente: planificador
ultima_actualizacion: 2026-10-14
---

# TAREA — Revisar los 2 PDFs de actas (privacidad)

## Objetivo
Decidir y ejecutar qué hacer con «delegados el cairo.pdf» y «transmision el cairo.pdf» en `06 RECURSOS/Actas E-14 de ejemplo/`: si son actas reales con datos identificables, reemplazarlas por las anonimizadas (E14_XXX_*) o quitarlas del repo.

## Contexto mínimo
- El repo es **PÚBLICO**. La regla del proyecto: «no hay datos sensibles» debe ser verdad, no suposición.
- Los PDFs aparecen en el único commit del vault (ae0c247); pueden contener firmas, nombres o números de mesa reales.
- Es **decisión del dueño**; el agente solo analiza y propone.

## Alcance
**Incluye:**
- Inspección de los 2 PDFs + propuesta escrita (anonimizar / reemplazar por E14_XXX / volver privado el repo) en la bitácora de esta tarea.
**NO incluye:**
- Borrar o modificar PDFs sin aprobación explícita del dueño.

## Whitelist de archivos (cerrada)
- `tasks/backlog/TASK-004-revisar-pdfs-actas.md` (bitácora)
- `06 RECURSOS/Actas E-14 de ejemplo/` — SOLO con aprobación explícita del dueño, tras su decisión

## Inmutables (diff CERO)
- Todo lo demás del vault.

## Pasos
1. Inspeccionar los 2 PDFs: ¿nombres, firmas, cédulas, números de mesa reales?
2. Escribir la propuesta con opciones y riesgos.
3. Esperar la decisión del dueño.
4. Ejecutar lo decidido y abrir PR.

## Gates
- Si se tocan PDFs: el PR lista los archivos exactos añadidos/eliminados.

## Criterios de aceptación
- [ ] Ningún PDF del repo contiene datos identificables de una elección real, o queda registrado que el dueño lo considera aceptable.

## Cómo probar
1. Abrir `06 RECURSOS` y verificar el estado final contra la decisión registrada.

## Pendientes / dudas abiertas
- ¿Los E14_XXX_* anonimizados ya existen como PDF o hay que generarlos?

---
## Bitácora de la tarea
- 2026-10-14 · planificador · Tarea creada en el backlog inicial del kit (hallazgo de la evaluación).
