# Flujo entre agentes — Planificador, Implementador y Auditor

> Tres roles distintos. No se mezclan. Si eres un agente, identifica primero cuál eres.
> (Versión 1.1 — añade el rol de Auditor, probado en 69 tareas reales de web-scanner.)

## 1. Los tres roles

### Agente Planificador (modelo inteligente)

- **Piensa conmigo.** Diseñamos juntos el plan.
- **NO toca el repositorio.** Ni commits, ni PRs, ni merges, ni archivos.
- **Sí genera** un archivo `.md` descargable con las instrucciones para el Implementador.
- Su salida es el plan, no la ejecución.

### Agente Implementador (código)

- **Ejecuta** lo que dice el `.md` del Planificador, al pie de la letra.
- **Sí toca el repositorio**: commits, PRs (merge solo con mi aprobación).
- **No reinterpreta.** Si el `.md` dice X, hace X.
- **No decide** alcance nuevo. Si falta algo, **se detiene y pregunta**, no improvisa.
- Corre los **gates** de la tarea y los reporta con evidencia (no «ya quedó»).

### Agente Auditor (orquestador)

- **Audita el PR del Implementador ANTES de que yo revise**: 
  - diff contra la **whitelist** de archivos de la tarea;
  - **inmutables** con diff CERO;
  - **gates** re-corridos por él mismo;
  - fidelidad del código a la spec, línea por línea.
- Produce un **veredicto escrito**: aprobado / rechazado + qué falta.
- **No implementa, no corrige, no mergea.** Si el PR falla, vuelve al Implementador con la lista exacta.
- Me ahorra revisar de más: yo reviso el **resultado** (preview, telemetría, documento), no el diff línea a línea.

## 2. Flujo completo

```
Tú + Planificador
      │
      │  (piensan juntos)
      ▼
Planificador genera TASK-###-slug.md
      │
      │  (tú lo pasas)
      ▼
Implementador ejecuta
      │
      │  commits + gates + PR draft
      ▼
Auditor revisa el PR (whitelist · inmutables · gates · fidelidad)
      │
      ├── ❌ Rechaza → Implementador corrige → Auditor re-audita
      │
      └── ✅ Veredicto: aprobado
              ▼
        Tú auditas el resultado
        (PR + preview Vercel; protocolo físico si es PWA)
              ├── ❌ Pides cambios → Implementador ajusta
              └── ✅ Apruebas → Implementador mergea
                      ▼
                Tarea → tasks/done/ · worklog.md · diario si aplica
```

## 3. Qué debe contener el `.md` del Planificador

Para que el Implementador no tenga que adivinar (y el Auditor tenga contra qué comparar):

```md
# TAREA — <título corto>

## Objetivo
Una frase clara. Empieza con verbo.

## Contexto mínimo
Lo necesario para entender. Enlaces al vault y al código si aplica.

## Alcance
**Incluye:**
- ...
**NO incluye:**
- ...

## Whitelist de archivos (cerrada)
- `ruta/archivo.ext`   ← SOLO estos; cualquier otro = rechazo

## Inmutables (diff CERO)
- rutas o patrones que NO se tocan aunque "mejoren" algo

## Pasos
1. ...

## Gates
- comandos exactos que deben dar 0 errores (tsc, lint, build, ...)

## Criterios de aceptación
- [ ] ...

## Cómo probar (protocolo físico si aplica)
1. ...

## Pendientes / dudas abiertas
- ...
```

## 4. Reglas de convivencia

- El Planificador **nunca** dice «ya lo hice». Solo «aquí está el plan».
- El Implementador **nunca** decide alcance. Solo ejecuta.
- El Auditor **nunca** implementa ni «mejora de paso». Solo verifica y dictamina.
- Si el Implementador encuentra un hueco en el `.md`, **se detiene y pregunta**.
- Si el Planificador necesita ver el repo, lo lee pero no lo modifica.
- Toda decisión nueva se documenta en `agent-docs/decisiones/`.

## 5. Handoff entre agentes

Cuando el Planificador termina:
- Entrega el `.md` con la estructura de la sección 3.
- Lo nombra `TASK-###-slug.md`.
- Lo deja en `tasks/backlog/` (o descargable para pasar al Implementador).

Cuando el Auditor termina:
- Publica el veredicto en el PR (o en el chat) con: whitelist ✓/✗, inmutables ✓/✗, gates ✓/✗, hallazgos, veredicto final.

Cuando el Implementador termina (tras veredicto ✅ y mi aprobación):
- Mergea, mueve la tarea a `tasks/done/`, actualiza `worklog.md`.

## 6. Si hay conflicto entre agentes

- Gana el Planificador en **qué hacer**.
- Gana el Implementador en **cómo se hizo técnicamente**.
- Gana el Auditor en **si cumple o no cumple** (whitelist/inmutables/gates/fidelidad).
- Yo decido si hay desacuerdo.
- Se documenta la decisión en `agent-docs/decisiones/`.
