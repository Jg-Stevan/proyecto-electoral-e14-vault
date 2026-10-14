---
id: TASK-002
titulo: "Sincronizar el cerebro con la realidad del repo web-scanner"
estado: backlog
fase: -
ultimo_agente: planificador
ultima_actualizacion: 2026-10-14
---

# TAREA — Sincronizar el cerebro con la realidad del repo web-scanner

## Objetivo
Actualizar las notas de estado para que digan la verdad a la fecha de ejecución: PRs mergeados, lote en curso, próximos pasos.

## Contexto mínimo
- El vault (ae0c247) dice que #39/#43 están pendientes. GitHub (la fuente de verdad) muestra ya mergeados: #43, #48, #49 (lote 3) y #51 (QA v1.3).
- El lote 4 (rama `perf/lote4-gracia-enhance`) está en PR, APROBADO por auditoría, pendiente de merge del dueño.
- Diagnósticos 2026-10-14 (SM-A135M, diag 0807/0809): lote 4 sin regresiones; editor-1 −29 %, filtros −23 %, long tasks −28 %. Capturas 2-3 dominadas por la llegada de la foto (pipe ~15 s), no por la cola del worker.
- **Verificar SIEMPRE contra GitHub antes de escribir**: estas cifras pueden estar desactualizadas cuando ejecutes la tarea.

## Alcance
**Incluye:**
- Actualizar las 2 notas de la whitelist con el estado real verificado.
**NO incluye:**
- Reescribir notas antiguas, tocar notas de `00`–`08`, ni opinar sobre decisiones ya tomadas.

## Whitelist de archivos (cerrada)
- `09 INGENIERÍA Y AGENTES/Estado del repositorio web-scanner.md`
- `09 INGENIERÍA Y AGENTES/Índice de Pull Requests.md`
- `tasks/backlog/TASK-002-sincronizar-estado-repo.md` (bitácora)

## Inmutables (diff CERO)
- Todo lo demás del vault.

## Pasos
1. Abrir la lista de PRs de web-scanner y el historial de `main` en GitHub.
2. Corregir `Índice de Pull Requests.md` (estados reales, con fecha de verificación).
3. Corregir `Estado del repositorio web-scanner.md` (main actual, lote en curso, próximo paso).
4. PR con la plantilla de tarea y entrada en `worklog.md`.

## Gates (tarea de docs)
- Cada PR citado con su estado verificado en GitHub el mismo día de la edición.
- Cero cambios fuera de la whitelist.

## Criterios de aceptación
- [ ] Ninguna nota afirma un estado que GitHub contradiga.

## Cómo probar
1. Leer las notas editadas y contrastar cada afirmación contra GitHub.

## Pendientes / dudas abiertas
-

---
## Bitácora de la tarea
- 2026-10-14 · planificador · Tarea creada al detectar el desajuste durante la evaluación del vault.
