> Si eres un agente de IA, empieza por [`AGENTS.md`](./AGENTS.md).
# 🧠 Proyecto Electoral E-14 — Cerebro Digital

> **Vault de Obsidian** para la digitalización, transmisión y auditoría de actas **E-14** en consulados del exterior (Presidenciales 2026, segunda vuelta).

---

## 📦 Qué es esto

Un **segundo cerebro** construido con [Obsidian](https://obsidian.md/) que organiza todo el conocimiento del proyecto electoral E-14: contexto del negocio, arquitectura, requisitos, los dos productos (PWA Digitalizador + Panel de Supervisión), el design system y **toda la ingeniería real** del repositorio [web-scanner](https://github.com/Jg-Stevan/web-scanner).

No es código. Es **el modelo mental** para entender y mantener el código.

---

## 🚀 Cómo usarlo

1. **Instala [Obsidian](https://obsidian.md/)** (gratis).
2. Clona este repo o descárgalo como ZIP.
3. En Obsidian: *Open folder as vault* → selecciona la carpeta `PROYECTO ELECTORAL`.
4. Abre **`00 INICIO / Introducción al Cerebro Digital** para aprender a usarlo.
5. Luego abre **00 INICIO / Inicio - Proyecto Electoral** para ver el mapa.

---

## 🗺️ Estructura

| Carpeta | Qué contiene |
|---|---|
| `00 INICIO` | Mapas, guías, pendientes, el resumen de cambios |
| `01 CONTEXTO ELECTORAL` | Cómo funciona el negocio electoral (el "por qué") |
| `02 ARQUITECTURA Y REQUISITOS` | ERS, reglas de negocio, flujo de validación |
| `03 PWA DIGITALIZADOR` | Producto móvil: estados e interfaz |
| `04 SISTEMA DE SUPERVISIÓN` | Producto web: panel, anomalías, SLA, auditoría |
| `05 DISEÑO UI` | Design System (Precision Monitor) |
| `06 RECURSOS` | PDF de actas E-14 reales de ejemplo |
| `07 DIARIO` | Notas diarias (rutina de trabajo) |
| `08 PLANTILLAS` | Plantillas reutilizables (decisión, idea, reunión, diario) |
| `09 INGENIERÍA Y AGENTES` | Motor de captura, lógica de aprobación, PRs, backlog |

---

## 📑 Los 3 documentos maestros

Si solo vas a leer 3 notas, lee estas:

1. **`00 INICIO / Introducción al Cerebro Digital`** — cómo usar el vault como segundo cerebro
2. **`00 INICIO / Resumen de Cambios Implementados`** — qué se construyó y qué cambió
3. **`09 INGENIERÍA Y AGENTES / Inicio - Ingeniería y Agentes`** — la puerta de entrada a la ingeniería
4. **[[Contrato PWA → Supervisión]]** — lectura obligada para el siguiente módulo

---

## 🎯 El proyecto en 30 segundos

| Pregunta | Respuesta |
|---|---|
| **¿Qué?** | Sistema para digitalizar, validar y transmitir actas E-14 del exterior |
| **¿Dónde?** | Consulados y embajadas (7 días, cierre diario 16:00 h local) |
| **¿Qué actas?** | Delegados (alta) y Transmisión (secundaria). Claveros no entra |
| **¿Con qué?** | PWA Digitalizador (móvil, offline-first) + Panel de Supervisión |
| **¿Volumen?** | 1.489 mesas (lun-sáb), hasta 2.181 el domingo pico |
| **¿Escenario?** | Presidenciales 2026, segunda vuelta, exterior (depto 88) |

---

## 🔗 Enlaces

- **Repositorio de código:** [web-scanner](https://github.com/Jg-Stevan/web-scanner)
- **App E-14 desplegada:** [jg-stevan.github.io/web-scanner](https://jg-stevan.github.io/web-scanner/)
- **Laboratorio:** [jg-stevan.github.io/web-scanner/lab/](https://jg-stevan.github.io/web-scanner/lab/)
- **Fuente de verdad por-aplicación:** `apps/digitalizador-e14/docs/` (DECISIONS, ROADMAP, SPECs, design)

---

## 📝 Licencia

Proyecto privado de uso interno. El contenido de este vault es documentación de trabajo.

---

*Última actualización: 2026-10-14*
