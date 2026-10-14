# Continuidad entre sesiones

> Objetivo: que cualquier agente pueda retomar el trabajo si se reinicia la ventana de contexto, se cierra la sesión o cambia el agente.
> (Versión 1.1 — añade el `worklog.md`, probado en 69 tareas reales.)

## 1. Principio

**Todo lo importante debe estar en el repo, no en la conversación.**
Si no está en el repo, no existe para el siguiente agente.

## 2. Los dos registros (convivencia)

1. **`tasks/`** — el estado de CADA tarea (una nota por tarea, con frontmatter). Es la cola de trabajo.
2. **`worklog.md`** (raíz del repo) — el LOG CRONOLÓGICO de todo lo que hacen los agentes. **Append-only**: nunca se edita ni borra lo anterior; cada entrada nueva se agrega al final.

`worklog.md` es el respaldo cuando una tarea quedó a medias y su nota no se alcanzó a actualizar: siempre se puede reconstruir desde el log.

## 3. Formato de una entrada de worklog (fija, no negociable)

```md
---
Task ID: <número consecutivo>
Agent: <planificador | implementador | auditor>
Task: <qué te pidieron>

Work Log:
- <paso concreto 1>
- <paso concreto 2>

Stage Summary:
- <resultados clave / decisiones / artefactos>
```

## 4. Al iniciar una sesión

El agente debe leer, en este orden:

1. `AGENTS.md`
2. `agent-docs/como-trabajo.md`
3. `agent-docs/flujo-agentes.md`
4. `agent-docs/continuidad.md` (este archivo)
5. `tasks/in-progress/` → qué está en curso
6. `tasks/backlog/` → qué sigue
7. `agent-docs/decisiones/` → decisiones ya tomadas
8. `worklog.md` (últimas 2–3 entradas) → qué pasó en la sesión anterior

Con eso debe poder responder: ¿Qué proyecto es? · ¿Qué rol tengo? · ¿Qué se está haciendo ahora? · ¿Qué falta? · ¿Qué reglas aplican?

## 5. Al cerrar una sesión

Antes de terminar, el agente debe:

- [ ] Mover la tarea a `tasks/in-progress/` o `tasks/done/` según estado.
- [ ] Actualizar el `.md` de la tarea con: estado actual, qué se hizo, qué falta, bloqueos, próximo paso concreto.
- [ ] **Añadir su entrada a `worklog.md`** (formato de la sección 3).
- [ ] Registrar decisiones nuevas en `agent-docs/decisiones/`.
- [ ] Avisarme si va a actualizar el diario del cerebro digital.

## 6. Formato de una tarea en curso

```md
---
id: TASK-###
titulo: ""
estado: backlog | in-progress | review | done
fase: <qué fase>
ultimo_agente: <planificador | implementador | auditor>
ultima_actualizacion: 2026-01-01
---

## Estado actual
...

## Qué se hizo
...

## Qué falta
...

## Bloqueos
...

## Próximo paso concreto
...

## Archivos relevantes
...
```

## 7. Reglas de continuidad

- **Nunca** dejar trabajo a medias sin registrar (tarea + worklog).
- **Nunca** asumir que el siguiente agente recuerda la conversación.
- **Siempre** escribir como si el siguiente agente fuera nuevo.
- **Siempre** enlazar a archivos concretos del repo.
- Si hay duda abierta, escribirla en la tarea, no en el chat.

## 8. Señales de que hay que parar y documentar

- La sesión se está alargando mucho.
- El contexto se está llenando.
- Hay decisiones nuevas sin registrar.
- Hay cambios sin commit.
- Hay dudas sin resolver.

En cualquiera de estos casos: parar → actualizar la tarea → registrar decisiones → añadir entrada al worklog → avisarme → cerrar limpio.

## 9. Cómo retomar

Al retomar, el agente debe poder decir:

> «Estoy retomando TASK-###. Estado: X. Se hizo A y B. Falta C. Próximo paso: D. ¿Continúo?»

Si no puede decir eso, falta documentación.
