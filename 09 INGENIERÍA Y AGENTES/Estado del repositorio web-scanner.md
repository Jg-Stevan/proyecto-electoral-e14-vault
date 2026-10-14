---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, repositorio, estado, git]
---

# 📊 Estado del repositorio web-scanner

> **Dónde está cada cosa, qué se desplegó y en qué rama vive la verdad.**
> Abre esta nota antes de tocar el repo para no pisar trabajo en curso.

---

## 1. El repositorio

| Dato | Valor |
|---|---|
| **Repo** | [github.com/Jg-Stevan/web-scanner](https://github.com/Jg-Stevan/web-scanner) |
| **Estructura** | Monorepo: `packages/scanner-core` + `apps/scanner-lab` + `apps/digitalizador-e14` |
| **App desplegada** | [jg-stevan.github.io/web-scanner](https://jg-stevan.github.io/web-scanner/) (app E-14) |
| **Laboratorio** | [jg-stevan.github.io/web-scanner/lab/](https://jg-stevan.github.io/web-scanner/lab/) |
| **Regla suprema** | El lab es la fuente de verdad (regla de oro #0, ver [[Arquitectura del motor (scanner-core)]]) |

---

## 2. Ramas
- `main`: Rama vigente, integra el rediseño Stitch (HEAD `b8cfb01`).

## 3. Índice de Pull Requests Mergeados
| PR | Tema | Estado |
| :--- | :--- | :--- |
| #16 | ... | ✅ |
| #17 | ... | ✅ |
| #38 | ... | ✅ |
| #39 | ... | ✅ |
| #43 | ... | ✅ |
| #44 | Guard completitud (VIGENTE) | ✅ |
| #45 | Zoom inline | ✅ |
| #48 | LOTE 2 preview | ✅ |
| #49 | LOTE 3 blob-first | ✅ |
| #50 | Rediseño Stitch | ✅ |
| #51 | qa-v13 modo rápido | ✅ |
| #52 | LOTE 4 gracia-enhance | ✅ |

## 5. Lo desplegado
- Rediseño Stitch: ✅ integrado en main.
- LOTE 2 (preview): ✅ integrado.
- Notificaciones flotantes: ✅ eliminadas (Stitch).

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
