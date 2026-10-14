---
proyecto: Proyecto Electoral E-14
tipo: mapa
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, moc, mapa]
---

# ⚙️ Inicio — Ingeniería y Agentes

> **La puerta de entrada a toda la ingeniería del proyecto.**
> El proyecto ya está construido y desplegado: [web-scanner](https://github.com/Jg-Stevan/web-scanner) → [app E-14](https://jg-stevan.github.io/web-scanner/) · [laboratorio](https://jg-stevan.github.io/web-scanner/lab/).
> Aquí no está el código; está **el modelo mental** para entenderlo y mantenerlo.

---

## 🎯 El proyecto en 30 segundos (versión ingeniería)

| Pregunta | Respuesta |
|---|---|
| **¿Qué se construyó?** | Un **monorepo** con un motor headless + un laboratorio + la app electoral |
| **¿Dónde vive la lógica de aprobación?** | En **código**, no en los specs (por eso el vault no la tenía) |
| **¿La regla suprema?** | *"El rendimiento se gana con memoria, CPU y CSS — NUNCA degradando el dato de imagen"* |
| **¿Estado real?** | Funcionando, con PRs pendientes y un rediseño visual por integrar |

---

## 🧠 El modelo mental (lee esto primero)

| Nota | Qué resuelve |
|---|---|
| 🧠 [[Modelo del Acta E-14]] | La anatomía real del formulario y DIVIPOL. **La base de todo.** |
| 🎯 [[Doctrina del score y evaluarGate]] | **La respuesta a las contradicciones del vault**: precedencia + bandas + pisos |
| 🔍 [[Clasificación DIVIPOL]] | Cómo pasa de "una foto" a "ACTA de la MESA 001, El Cairo" |
| 🛡️ [[Guard de completitud (borde)]] | "La geometría sospecha, la tinta condena" — el veto del paso 1 |
| 🎚️ [[Estados, intentos y contingencias]] | 5 estados, 4 intentos, rescate por recorte |

---

## 📷 Motor de captura (`09.A Motor de captura`)

| Nota | Tema |
|---|---|
| [[Arquitectura del motor (scanner-core)]] | El monorepo, los módulos, la constitución, la regla de oro #0 |
| [[Cámara, autocaptura y burst]] | Dual pipeline, F-LENS, F-ZSL, la saga del burst (v1→v5), el caso iOS |
| [[Memoria, render y tiers]] | Fuente única v11, presupuesto de memoria, LOTE 2 (preview escalonado) |
| [[Trampas de iOS y WebKit]] | Todo lo que funciona en Chrome y rompe en iPhone |
| [[Telemetría, diagnóstico y baseline]] | Marks del pipeline, sondas en `window`, la baseline 2026-10-12 |

---

## ⚖️ Lógica de aprobación (`09.B Lógica de aprobación`)

| Nota | Tema |
|---|---|
| [[Clasificación DIVIPOL]] | Niveles AUTO/SUGERIDA/MANUAL, regla 4b, el caso El Cairo |
| [[Doctrina del score y evaluarGate]] | Precedencia, fórmula del score, los dos pisos, las bandas |
| [[Guard de completitud (borde)]] | El veto, el techo 6.4, §9.12 "papel sin votos" |
| [[Estados, intentos y contingencias]] | 5 estados, política de intentos, recorte como rescate |
| [[El puente - scanner-core-bridge]] | Dónde vive la lógica, el pipeline de 5 etapas, filtros y export |

---

## 🔗 El puente entre las dos líneas

```
Motor de captura (scanner-core)                 Lógica de aprobación (e14)
┌────────────────────────────────┐              ┌──────────────────────────────┐
│ image-processor  detector      │              │ clasificador.ts              │
│ sensor-profiler   quality       │   bridge →   │ completitud.ts               │
│ frame-loop        ocr           │ ──────────> │ scanner-core-bridge.ts ⭐     │
│ pdf-export        store         │              │ divipol.ts  store  types      │
└────────────────────────────────┘              └──────────────────────────────┘
   (headless, congelado)                           (evaluarGate vive aquí)
```

> El bridge es el punto exacto donde **el motor entrega la imagen** y **el juez decide el destino del acta**. Ver [[El puente - scanner-core-bridge]].

---

## 🗂️ Gestión del repositorio

| Nota | Para qué |
|---|---|
| [[Estado del repositorio web-scanner]] | En qué rama/versión está cada cosa, qué se desplegó |
| [[Índice de Pull Requests]] | Mapa de PRs (abiertos, aprobados, mergeeados, revertidos) |
| [[Backlog y decisiones pendientes]] | **La lista viva de lo que falta** — abre siempre esto primero |
| [[Método de trabajo (spec, implementa, audita)]] | El flujo: spec → código → auditoría E2E |
| [[Reglas de trabajo con agentes]] | Cómo delegar en agentes de IA sin perder control |

---

## 🚦 Por dónde empezar según tu rol

| Si vas a… | Lee primero |
|---|---|
| **Entender el negocio electoral** | [[Modelo del Acta E-14]] → [[Que datos contiene un Acta E-14]] |
| **Resolver un bug de calidad** | [[Doctrina del score y evaluarGate]] → [[Guard de completitud (borde)]] |
| **Resolver un bug de identificación** | [[Clasificación DIVIPOL]] → [[Modelo del Acta E-14]] |
| **Optimizar el rendimiento** | [[Telemetría, diagnóstico y baseline]] → [[Memoria, render y tiers]] |
| **Arreglar algo en iPhone** | [[Trampas de iOS y WebKit]] → [[Cámara, autocaptura y burst]] |
| **Saber qué hacer hoy** | [[Backlog y decisiones pendientes]] → [[Tablero de próximos pasos]] |
| **Empezar con el método** | [[Método de trabajo (spec, implementa, audita)]] → [[Reglas de trabajo con agentes]] |

---

## ⚠️ Las 3 reglas que no se rompen

1. **El lab es la fuente de verdad** (regla de oro #0). Lo que existe y funciona en `scanner-lab` se **copia literalmente** al e14, con `// Fuente: …`. Nunca se reinventa. (Ver [[Arquitectura del motor (scanner-core)]] §4.)

2. **La telemetría no se renombra ni se borra**. Los E2E de auditoría dependen de los nombres de marks fijos. (Ver [[Telemetría, diagnóstico y baseline]] §1.)

3. **El juez objetivo está fuera del repo** (`analizador-diag-v4.js`, node puro, umbrales fijos y públicos). *"Mismo JSON → mismo veredicto."* (Ver [[Telemetría, diagnóstico y baseline]] §3.)

---

## 📊 Estado actual (alto nivel)

- ✅ **Motor de captura** funcional en gama alta (A56, iPhone) y **piso aceptado en gama baja (A13 ≈18 s/captura)**.
- ✅ **Lógica de aprobación** consolidada (precedencia + bandas + pisos).
- 🟡 **Rediseño Stitch** pendiente de rebase sobre `main` con auditoría E2E.
- 🔴 **PR #47** (revert del guard-borde) abierto — riesgo mayor, 30 segundos en cerrarlo.
- 🟠 **LOTE 2** (preview escalonado) spec entregada, esperando merge de #39 y #43.

Detalle completo: [[Estado del repositorio web-scanner]] y [[Backlog y decisiones pendientes]].

---

**Ver también:** [[Inicio - Proyecto Electoral]] · [[Contradicciones detectadas]] · [[Lecciones y trampas]]
