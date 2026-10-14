---
id: TASK-001
titulo: "Aplicar el kit de agentes v1.1 al vault"
estado: review
fase: PR abierto / archivos subidos, pendiente de verificación y merge
ultimo_agente: implementador
ultima_actualizacion: 2026-10-14
---

# TAREA — Aplicar el kit de agentes v1.1 al vault

## Objetivo
Instalar la capa agent-readiness del vault: `AGENTS.md` + `agent-docs/` + `tasks/` + `worklog.md` + una línea en `README.md`.

## Contexto mínimo
- Evaluación completa y tabla de deltas en el doc maestro del kit (fuera del repo: `INSTRUCCIONES-vault-e14-kit-agentes-v1.md`).
- Base: `main @ ae0c247` (único commit del vault a 2026-10-14).
- Este trabajo es el **estreno del flujo** Planificador → Implementador → Auditor, con una tarea de riesgo cero.
- Contenido del kit generado por el orquestador (Z) a partir de: la evaluación del vault, el paquete de DeepSeek («Para analizar.md») y el protocolo probado en 69 tareas reales de web-scanner.

## Alcance
**Incluye:**
- Los 14 archivos de la whitelist, subidos tal cual el kit (byte a byte).
- La entrada `Task ID: 1` añadida al final de `worklog.md` (texto listo en el LEEME del kit).
**NO incluye:**
- Tocar `00`–`09`, `.obsidian/`, `.gitignore`, PDFs, ni reorganizar nada.

## Whitelist de archivos (cerrada)
- `AGENTS.md`
- `README.md` (solo línea 1 añadida arriba; resto diff CERO)
- `worklog.md` (solo la entrada Task ID: 1 añadida al final; resto diff CERO)
- `agent-docs/como-trabajo.md`
- `agent-docs/flujo-agentes.md`
- `agent-docs/continuidad.md`
- `agent-docs/decisiones/TEMPLATE.md`
- `tasks/TEMPLATE.md`
- `tasks/in-progress/TASK-001-aplicar-kit.md`
- `tasks/backlog/TASK-002-sincronizar-estado-repo.md`
- `tasks/backlog/TASK-003-ci-del-vault.md`
- `tasks/backlog/TASK-004-revisar-pdfs-actas.md`
- `tasks/backlog/TASK-005-diario-automatizado.md`
- `tasks/done/.gitkeep`

## Inmutables (diff CERO)
- Todo lo que no esté en la whitelist, en particular: `00 INICIO` … `09 INGENIERÍA Y AGENTES`, `.obsidian/`, `.gitignore`, y el contenido previo de `README.md` y `worklog.md`.

## Pasos
1. Copiar el contenido de `repo/` (carpeta del kit) a la raíz del vault respetando las rutas exactas.
2. Añadir la entrada `Task ID: 1` al final de `worklog.md` (texto en el LEEME del kit).
3. Un único commit: `Kit de agentes v1.1 (AGENTS.md + agent-docs + tasks + worklog)`.
4. Rama + PR draft (o subida por web en un solo commit, ver LEEME opción A).
5. Correr las verificaciones de la sección Gates y pegar el resultado en el PR.

## Gates (tarea de docs)
- `git diff --stat main` → SOLO los 14 paths de la whitelist.
- `git diff main -- README.md` → 1 línea añadida, 0 eliminadas.
- `diff -r repo/ <clone>` (contra el kit) → vacío (salvo `worklog.md` por la entrada añadida).
- Cualquier path fuera de la whitelist = rechazo automático.

## Criterios de aceptación
- [ ] `AGENTS.md` se renderiza en GitHub y en Obsidian, con sus enlaces relativos funcionando.
- [ ] `tasks/in-progress/` y `tasks/backlog/` visibles con sus notas.
- [ ] `README.md` tiene el blockquote de agentes arriba del todo.
- [ ] `worklog.md` existe con las entradas Task ID: 0 y 1.

## Cómo probar
1. En GitHub: abrir `AGENTS.md` y hacer clic en los enlaces relativos.
2. En Obsidian: recargar el vault, abrir `tasks/` y `agent-docs/`.

## Pendientes / dudas abiertas
- Al mergear: mover esta nota a `tasks/done/` con `estado: done` y registrar la decisión DEC-001 (adoptar el kit) si el dueño la quiere formalizada.

---
## Bitácora de la tarea
- 2026-10-14 · planificador · Kit generado por el orquestador (Z) tras evaluar el vault (ae0c247) y el paquete DeepSeek.
- 2026-10-14 · implementador · Archivos subidos / PR abierto (completar número de PR).
