---
id: TASK-003
titulo: "CI del vault: guardián de estructura y frontmatter"
estado: backlog
fase: -
ultimo_agente: planificador
ultima_actualizacion: 2026-10-14
---

# TAREA — CI del vault (automatización real)

## Objetivo
Añadir un GitHub Action que valide en cada PR: (a) que `00`–`09` no se reorganicen, (b) frontmatter válido en `tasks/**/*.md`, (c) enlaces relativos de markdown no rotos.

## Contexto mínimo
- Es la automatización que un repo de docs puede tener de verdad, y barata de mantener.
- Estados válidos de tarea: `backlog | in-progress | review | done` (ver `agent-docs/continuidad.md` §6).
- Para web-scanner, el paso siguiente natural es un CI que corra `core:check && app:check && lint` en cada PR (tarea aparte, en aquel repo).

## Alcance
**Incluye:**
- `.github/workflows/vault-guard.yml` (job único, ubuntu-latest, checkout + validaciones).
**NO incluye:**
- Cambiar nada del contenido existente del vault.

## Whitelist de archivos (cerrada)
- `.github/workflows/vault-guard.yml`
- `tasks/backlog/TASK-003-ci-del-vault.md` (bitácora)

## Inmutables (diff CERO)
- Todo lo demás del vault.

## Pasos
1. (a) Estructura: `git diff --name-status base...head` → fallar si se renombra o elimina alguna carpeta `00`–`09`.
2. (b) Frontmatter: script inline (node o python) que valide en `tasks/**/*.md`: `id` único en todo el repo, `estado` ∈ {backlog, in-progress, review, done}, campos `titulo` y `ultima_actualizacion` presentes.
3. (c) Enlaces: verificar que los enlaces markdown relativos (`](./ruta)` y `](../ruta)`) apunten a archivos existentes.
4. Probar el action en el propio PR que lo introduce.

## Gates
- El action pasa (verde) en el PR que lo introduce.
- Test de falsos negativos: en rama desechable, un commit que rompa el frontmatter debe poner el CI rojo.

## Criterios de aceptación
- [ ] Un PR que reorganice `00`–`09` → CI rojo.
- [ ] Un `tasks/*.md` con frontmatter inválido → CI rojo.

## Cómo probar
1. Crear rama de prueba que renombre una carpeta y comprobar que el CI falla.

## Pendientes / dudas abiertas
- ¿Añadir también lint de markdown? Opcional, no bloquea.

---
## Bitácora de la tarea
- 2026-10-14 · planificador · Tarea creada en el backlog inicial del kit.
