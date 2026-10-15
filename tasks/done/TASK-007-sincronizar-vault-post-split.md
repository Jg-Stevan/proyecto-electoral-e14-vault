---
id: TASK-007
titulo: "Sincronizar vault post-split y librería npm"
estado: done
fase: sync infraestructura post-split (kit INSTRUCCIONES-vault-sync-v1)
ultimo_agente: implementador
ultima_actualizacion: 2026-10-15
---

# TAREA — Sincronizar vault post-split y librería npm

## Objetivo
Actualizar las notas de infraestructura del vault a la realidad de 2 repos + librería npm 0.1.0.

## Contexto mínimo
Tras el split de 2026-10-15 el monorepo original dejó de existir: el código vive en [web-scanner](https://github.com/Jg-Stevan/web-scanner) (workspace `apps/scanner-lab` + `packages/scanner-core`) y [digielect14](https://github.com/Jg-Stevan/digielect14) (app en la raíz, consume `@jg-stevan/scanner-core@^0.1.0` desde npm); la historia completa quedó archivada en [web-scanner-monorepo-viejo](https://github.com/Jg-Stevan/web-scanner-monorepo-viejo) (NO borrar). Kit ejecutado: `INSTRUCCIONES-vault-sync-v1.md`. Supersede a TASK-006 (el HEAD b8cfb01 que describía ya no existe).

## Alcance
**Incluye:**
- 6 notas del scope cerrado: Estado del repositorio web-scanner, Arquitectura del motor (scanner-core), AGENTS.md (solo §1), Índice de Pull Requests, Tablero de próximos pasos, Backlog y decisiones pendientes
- Registros internos: TASK-006 → done (superseded), esta tarea, entrada nueva en worklog.md

**NO incluye:**
- Notas de producto/negocio (`01`–`08`) ni `.obsidian/` (NO TOCAR, regla dura del kit)
- Crear carpetas nuevas o reorganizar el vault

## Whitelist de archivos (cerrada)
- `AGENTS.md`
- `09 INGENIERÍA Y AGENTES/Estado del repositorio web-scanner.md`
- `09 INGENIERÍA Y AGENTES/09.A Motor de captura/Arquitectura del motor (scanner-core).md`
- `09 INGENIERÍA Y AGENTES/Índice de Pull Requests.md`
- `09 INGENIERÍA Y AGENTES/Backlog y decisiones pendientes.md`
- `00 INICIO/Tablero de próximos pasos.md`
- `tasks/in-progress/TASK-007-sincronizar-vault-post-split.md` → `tasks/done/`
- `tasks/done/TASK-006-sincronizar-vault-post-stitch.md`
- `worklog.md`

## Inmutables (diff CERO)
- Carpetas `01 CONTEXTO ELECTORAL` … `08 PLANTILLAS` y `.obsidian/`
- §6 baseline de rendimiento de «Estado del repositorio web-scanner»
- §2+ de «Arquitectura del motor» (módulos, constitución, regla de oro #0, datos)

## Pasos
1. W0: clonar el vault y leer los archivos obligatorios (AGENTS.md, Convenciones, continuidad, TASK-006, TEMPLATE, worklog y las 6 notas del scope).
2. W0: correr los spot-checks del kit (HEADs de los 3 repos, demos de Pages, npm dist-tags).
3. W1: editar las 6 notas del scope con los textos exactos del kit.
4. W2: mover TASK-006 a `tasks/done/` con justificación; crear TASK-007; añadir entrada Task ID 3 al worklog.
5. W3: verificar `git diff --stat` contra whitelist, wiki-links y frontmatter; commit directo a main y push (el vault no tiene CI).

## Gates
- El vault no tiene CI (TASK-003 «ci-del-vault» sigue en backlog): gates manuales = diff contra whitelist con cero archivos 01–08, wiki-links apuntando a notas existentes, `actualizado: 2026-10-15` en toda nota tocada.

## Criterios de aceptación
- [x] Los 6 archivos del scope contienen los textos del kit.
- [x] Spot-checks verificados antes de editar (HEADs `a9b6b83` / `fbac5de` / `14d8f04`, demos vivas, npm `0.1.0`).
- [x] PRs #53–#59 reconstruidos SOLO desde `git log --merges --format="%h %s" -15` del archivo; lo no verificable se omite y se anota.
- [x] Diff CERO en `01`–`08` y `.obsidian/`.
- [x] TASK-006 cerrada con justificación y worklog con Task ID consecutivo (3).

## Cómo probar
1. `git diff HEAD~1 --stat` → solo archivos de la whitelist.
2. Abrir [[Estado del repositorio web-scanner]] y [[Índice de Pull Requests]] §1.bis y comprobar la realidad post-split.

## Pendientes / dudas abiertas
- Pendientes del dueño documentados en [[Tablero de próximos pasos]] §Infraestructura post-split (unpublish de `0.0.0-stage`, cierre del archivo, `.env.example`, Vercel Previews, branch protection).

---
## Bitácora de la tarea
- 2026-10-15 · implementador · Kit INSTRUCCIONES-vault-sync-v1 iniciado (W0): clonado, lecturas obligatorias y spot-checks verificados.
- 2026-10-15 · implementador · W1–W3 completados: 6 notas actualizadas, TASK-006 superseded, worklog Task ID 3, commit directo a main. Tarea cerrada.
