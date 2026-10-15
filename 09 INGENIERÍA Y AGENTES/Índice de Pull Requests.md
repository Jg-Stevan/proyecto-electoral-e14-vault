---
proyecto: Proyecto Electoral E-14
tipo: mapa
estado: vigente
actualizado: 2026-10-15
tags: [ingenieria, pull-requests, indice, git]
---

# 🔀 Índice de Pull Requests

> **El mapa de todos los PRs que importan.** Cada uno con su tema, estado y la nota donde vive el detalle.
> Para saber en qué rama está la verdad, abre [[Estado del repositorio web-scanner]].

---

## 1. Convenciones

| Símbolo | Significado |
|---|---|
| ✅ | Mergeado |
| 🟡 | Aprobado, pendiente de merge |
| 🔴 | Abierto, requiere acción |
| ❌ | Cerrado sin merge / revertido |
| ⚠️ | Revertido por otro PR |

---

## 1.bis. Cierre del monorepo (2026-10-15)

El monorepo original se dividió en 2 repos (ver [[Estado del repositorio web-scanner]]). Últimos PRs del monorepo:

| PR | Tema | Estado |
|---|---|---|
| #60 | Empaquetado npm del core (build a dist/ + publishConfig) | ✅ |
| #61 | Flujo de publicación por staging (`.publish/` — publishConfig.exports es pnpm-only) | ✅ |
| #59 | Preparación de la publicación v1 del core (rama `chore/publicacion-v1`) | ✅ |
| #58 | LOTE 5.5 — editor de alta calidad (rama `perf/lote5-5-editor-alta-calidad`) | ✅ |

PRs #59 y #58 reconstruidos desde `git log --merges --format="%h %s" -15` del archivo [web-scanner-monorepo-viejo](https://github.com/Jg-Stevan/web-scanner-monorepo-viejo). **PRs #53–#57 OMITIDOS**: no aparecen como merge commits en ese log y su tema no es verificable — nada inventado.

**Nueva era:** desde el split cada repo lleva numeración de PRs propia desde #1. Los PRs nuevos se documentan por repo; este índice consolida el historial del monorepo.

---

## 2. PRs mergeados (historial consolidado)

| PR | Tema | Qué resolvió | Nota de origen |
|---|---|---|---|
| **#16** | `contrasteEfectivoDe` (piso 80) + `PISO_IDENTIFICADA = 8.2` + clasificar antes de puntuar | El acta de El Cairo (KIT 399) se identificaba bien pero quedaba a 0,2 del rechazo: **6.7 → 8.5** | [[Doctrina del score y evaluarGate]] §8 |
| **#17** | `PISO_IDENTIFICADA = 10` | *"¿cómo subimos a 10/10?"* — 1 constante, misma tubería | [[Doctrina del score y evaluarGate]] §8 |
| **#38** | Modelo `score = f(OCR + completitud)` | El score por métricas de imagen no distinguía un acta de una bolsa de papas | [[Doctrina del score y evaluarGate]] §8 |

---

## 3. PRs mergeados
| PR | Tema | Estado |
| :--- | :--- | :--- |
| #39 | Burst | ✅ |
| #43 | F2 | ✅ |
| #45 | Zoom inline | ✅ |
| #48 | LOTE 2 preview | ✅ |
| #49 | LOTE 3 blob-first | ✅ |
| #50 | Rediseño Stitch | ✅ |
| #51 | qa-v13 modo rápido | ✅ |
| #52 | LOTE 4 gracia-enhance | ✅ |

| PR | Tema | Estado | Acción |
|---|---|---|---|

> Ver [[Backlog y decisiones pendientes]] B-1.

---

## 5. PRs revertidos
| PR | Tema | Qué pasó | Nota |
| :--- | :--- | :--- | :--- |
| #44 | Guard de borde (original) | VIGENTE en main | [[Guard de completitud (borde)]] |

El burst pasó por 5 versiones; cada una costó un PR y una auditoría:

| Versión | Cambio clave |
|---|---|
| **v1** | Muro de tiempo sin límite global (A13: 16,8-17,2 s) |
| **v2-v3** | Muro de 2000 ms determinista + `performance.now()` monotónico |
| **v4** | Sampler 64×64 de luma, cadencia 750 ms, umbral 2.5 |
| **v5** | `photoGraceMs = 2000` (clamp 0-5000) + sonda de heap |

> Detalle y el hallazgo forense central en [[Cámara, autocaptura y burst]] §7.

---

## 7. Reglas para abrir un PR nuevo

1. **Especifica primero** (ver [[Método de trabajo (spec, implementa, audita)]])
2. **Copia del lab** con `// Fuente: …` si tocas UI (regla de oro #0)
3. **No renombres telemetría** (los E2E dependen de los nombres)
4. **Documenta el cambio** en la nota correspondiente del vault
5. **Añade el PR aquí** con su símbolo de estado

---

## 8. Mapa de dependencias
*Cadena #48→#49→#50→#51/#52 completada.*
        │
        ▼
   #39 (merge)  ┐
                 ├──> LOTE 2 (preview escalonado)  ──> Plan A (rebase)
   #43 (merge)  ┘
        │
        ▼
   Rediseño Stitch (rebase sobre main + auditoría E2E)
```

> El orden de integración (`v12b → rebase v13 → re-auditoría`) está sin decidir (ver [[Backlog y decisiones pendientes]] B-3).

---

**Ver también:** [[Estado del repositorio web-scanner]] · [[Backlog y decisiones pendientes]] · [[Método de trabajo (spec, implementa, audita)]] · [[Reglas de trabajo con agentes]]
