---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-15
tags: [ingenieria, repositorio, estado, git]
---

# 📊 Estado del repositorio web-scanner

> **Dónde está cada cosa, qué se desplegó y en qué rama vive la verdad.**
> Abre esta nota antes de tocar el repo para no pisar trabajo en curso.

---

## 1. Los repos (post-split 2026-10-15)

| Dato | Valor |
|---|---|
| **Repo escáner** | [github.com/Jg-Stevan/web-scanner](https://github.com/Jg-Stevan/web-scanner) — workspace: `apps/scanner-lab` + `packages/scanner-core` |
| **Repo electoral** | [github.com/Jg-Stevan/digielect14](https://github.com/Jg-Stevan/digielect14) — app en la raíz, consume `@jg-stevan/scanner-core@^0.1.0` desde npm |
| **Motor (librería)** | [@jg-stevan/scanner-core@0.1.0](https://www.npmjs.com/package/@jg-stevan/scanner-core) (npm, MIT, tag `core-v0.1.0`) |
| **Demo lab** | [jg-stevan.github.io/web-scanner](https://jg-stevan.github.io/web-scanner/) — **EN LA RAÍZ** (antes `/lab/`) |
| **Demo e14** | [jg-stevan.github.io/digielect14](https://jg-stevan.github.io/digielect14/) |
| **Archivo histórico** | [web-scanner-monorepo-viejo](https://github.com/Jg-Stevan/web-scanner-monorepo-viejo) — historia completa, PRs, tags. NO BORRAR |
| **Regla suprema** | El lab sigue siendo la fuente de verdad (regla de oro #0, ver [[Arquitectura del motor (scanner-core)]]) |

---

## 2. Ramas
- `main` en ambos repos (1 commit inicial cada uno: `a9b6b83` / `fbac5de`). Las ramas históricas no fusionadas (`feat/e14-redesign-stitch`, `plan-a-memoria-render`, etc.) viven SOLO en el archivo `web-scanner-monorepo-viejo`.

## 3. Índice de Pull Requests
PRs del monorepo consolidados en [[Índice de Pull Requests]] (hasta #61, cierre del monorepo). A partir del split cada repo lleva su propia numeración desde #1.

## 5. Lo desplegado
- Demo lab: [jg-stevan.github.io/web-scanner](https://jg-stevan.github.io/web-scanner/) — **en la raíz** (antes `/lab/`). CI 4/4 verde.
- Demo e14: [jg-stevan.github.io/digielect14](https://jg-stevan.github.io/digielect14/). CI 4/4 verde.

| Subsistema | Estado |
|---|---|
| Motor de captura (gama alta) | ✅ A56 y iPhone funcionando |
| Motor de captura (gama baja, A13) | 🟡 Piso aceptado ≈18 s/captura (congestión interna del navegador, sin arreglo de app) |
| Lógica de aprobación | ✅ Precedencia + bandas + pisos consolidada |
| Clasificación DIVIPOL | ✅ Con rescate global (regla 4b) |
| Guard de completitud | ✅ Vigente (pero #47 quiere revertirlo — **cerrar**) |
| Rediseño Stitch (visual) | 🟡 Pendiente de rebase sobre `main` con auditoría E2E |
| LOTE 2 (preview escalonado) | 🟡 Spec entregada, esperando merge de #39 y #43 |
| Export PDF del e14 | ❌ Eliminado por decisión del dueño (el lab lo conserva) |
| Notificaciones flotantes | ❌ Eliminadas ("cero mensajes"); reemplazos pendientes |
| Infraestructura (repos/CI/npm) | ✅ Split completado 2026-10-15: 2 repos + librería npm 0.1.0 (ver [[Índice de Pull Requests]]) |

---

## 6. La baseline de rendimiento (medición 2026-10-12)

| Dispositivo | RAM / heap | CPU bench | Encode JPEG 3200 | Refresh |
|---|---|---|---|---|
| **Galaxy A56** | 8 GB / 1965 MB | 21-25 ms | 371-428 ms | 60 Hz |
| **Galaxy A13** (caso crítico) | 4 GB / 961 MB | 82 ms (~3,5× más lento) | 730 ms | 43 Hz |
| PC (Windows) | 8 GB / 4128 MB | 60 ms | 209 ms | 60 Hz |
| **iPhone** (iOS 26.6 Safari) | — | 7 ms | — | — 🥇 el más rápido |

> Detalle y los 3 hallazgos que orientaron el trabajo en [[Telemetría, diagnóstico y baseline]] §5.

---

## 7. Qué hacer antes de tocar el repo

1. Lee esta nota y [[Backlog y decisiones pendientes]].
2. Verifica que tu cambio **no duplique** trabajo aprobado sin mergeear (#39, #43).
3. Si tocas UI, **copia literal del lab** con `// Fuente: …` (regla de oro #0).
4. Si tocas telemetría, **no renombres ni borres marks** (los E2E dependen de ellos).
5. Documenta la decisión en el diario (`07 DIARIO`) y enlázala aquí.

---

**Ver también:** [[Índice de Pull Requests]] · [[Backlog y decisiones pendientes]] · [[Arquitectura del motor (scanner-core)]] · [[Inicio - Ingeniería y Agentes]]
