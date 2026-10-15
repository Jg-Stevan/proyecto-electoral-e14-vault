# AGENTS.md — Proyecto Electoral E-14 (vault)

> **Si eres un agente de IA, empieza por aquí.** Lee este archivo completo antes de proponer o ejecutar cualquier cosa.

## 1. Qué es este repositorio

- Es el **cerebro digital** del proyecto: un vault de Obsidian con el conocimiento del proyecto electoral E-14 (digitalización, transmisión y auditoría de actas E-14 en consulados del exterior).
- **No contiene código de la aplicación.** Contiene el modelo mental: contexto electoral, arquitectura, requisitos, productos, diseño e ingeniería real.
- El **código real** vive en 2 repos: [github.com/Jg-Stevan/web-scanner](https://github.com/Jg-Stevan/web-scanner) (motor `packages/scanner-core` → npm `@jg-stevan/scanner-core` + laboratorio `apps/scanner-lab`) y [github.com/Jg-Stevan/digielect14](https://github.com/Jg-Stevan/digielect14) (app electoral, consume el motor desde npm). El monorepo original, con la historia completa (PRs, tags, ramas), quedó archivado en [github.com/Jg-Stevan/web-scanner-monorepo-viejo](https://github.com/Jg-Stevan/web-scanner-monorepo-viejo) — NO borrar.

## 2. Orden de lectura obligatorio

1. Este archivo (`AGENTS.md`)
2. `agent-docs/como-trabajo.md` — cómo trabaja el dueño
3. `agent-docs/flujo-agentes.md` — los 3 roles y quién hace qué
4. `agent-docs/continuidad.md` — cómo retomar si se reinicia la sesión
5. `tasks/in-progress/` → qué está en curso
6. `tasks/backlog/` → qué sigue
7. `agent-docs/decisiones/` → decisiones ya tomadas
8. Las reglas profundas de ingeniería viven en `09 INGENIERÍA Y AGENTES/Reglas de trabajo con agentes.md` y `Método de trabajo (spec, implementa, audita).md` — **aplican siempre** que la tarea toque el repo de código.

Al terminar la lectura debes poder responder: ¿qué proyecto es? · ¿qué rol tengo? · ¿qué está en curso? · ¿qué falta? · ¿qué reglas aplican?

## 3. Los 3 roles (identifícate primero)

| Rol | Qué hace | Qué NO hace |
|---|---|---|
| **Planificador** (modelo inteligente) | Piensa con el dueño, genera el `.md` de tarea (`TASK-###-slug.md`) listo para pasar al Implementador | No toca ningún repositorio, ni commits, ni PRs |
| **Implementador** (agente de código) | Ejecuta el `.md` al pie de la letra, corre los gates, abre PR draft | No reinterpreta la tarea, no decide alcance, no mergea |
| **Auditor** (orquestador) | Audita el diff del PR: whitelist de archivos, inmutables, gates, veredicto escrito | No implementa, no mergea; su veredicto alimenta la decisión del dueño |

**El dueño aprueba todo merge.** Nadie mergea sin su aprobación explícita en el PR.

## 4. Reglas no negociables

1. ❌ **No crear carpetas nuevas** sin proponerlo en el PR y que el dueño lo apruebe.
2. ❌ **No reorganizar** el vault (`00`–`09`) sin propuesta previa.
3. ❌ **No tocar temas electorales con opinión.** El sistema electoral se respeta como es; nos adaptamos a él.
4. ❌ **No inventar requisitos.** Si falta contexto, detente y pregunta.
5. ❌ **No mergear** sin aprobación explícita del dueño.
6. ❌ En tareas de código: **no salirse de la whitelist de archivos** de la tarea y **no tocar los inmutables**.
7. ✅ Toda tarea deja registro: el `.md` de la tarea actualizado + entrada en `worklog.md` + decisión nueva en `agent-docs/decisiones/` si aplica.

## 5. Gates (tareas de código, obligatorios)

```bash
bun install && bun run core:check && bun run app:check && bun run lint   # los 3 = 0 errores
```

- El PR incluye el **diff contra main** comparado contra la whitelist de la tarea.
- Los **inmutables globales** (ver cada tarea) deben dar diff CERO.
- Sin gates verdes no hay auditoría; sin auditoría no hay QA física; sin QA física no hay merge.

## 6. Cómo se cierra una tarea

El Implementador entrega en el PR:

1. Resumen de lo que hizo.
2. Checklist de cómo probarlo el dueño (con protocolo físico si aplica).
3. Archivos tocados (diff exacto).
4. Próximos pasos o pendientes.
5. Actualización del `.md` de la tarea (estado `review`).
6. Entrada nueva en `worklog.md` (ver `agent-docs/continuidad.md`).

## 7. Mapa del vault

| Carpeta | Qué contiene |
|---|---|
| `00 INICIO` | Mapas, convenciones, tablero, decisiones de organización |
| `01 CONTEXTO ELECTORAL` | El negocio: proceso E-14, código de barras, volúmenes |
| `02 ARQUITECTURA Y REQUISITOS` | ERS, flujo de validación y aprobación |
| `03 PWA DIGITALIZADOR` | Producto móvil: estados e interfaz |
| `04 SISTEMA DE SUPERVISIÓN` | Producto web: panel, anomalías, SLA, auditoría |
| `05 DISEÑO UI` | Design System (Precision Monitor) |
| `06 RECURSOS` | Actas E-14 de ejemplo (PDF) |
| `07 DIARIO` | Notas diarias del dueño |
| `08 PLANTILLAS` | Plantillas reutilizables |
| `09 INGENIERÍA Y AGENTES` | Motor, lógica de aprobación, estado del repo, índice de PRs, backlog |
| `agent-docs/` | Cómo se trabaja (este kit) y decisiones |
| `tasks/` | Cola de trabajo: backlog / in-progress / done |

## 8. Si algo no cuadra

Detente, dilo y pregunta. Es lo que más valora el dueño. «Prefiero que me avises antes de seguir a que adivines mal.»
