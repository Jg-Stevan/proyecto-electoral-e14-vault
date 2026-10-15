---
proyecto: Proyecto Electoral E-14
tipo: mapa
estado: vigente
actualizado: 2026-10-13
tags: [moc, inicio]
---

# 🏠 Inicio — Proyecto Electoral E-14

> **Digitalización, transmisión y auditoría de actas E-14 en el exterior (consulados).**
> Esta es la puerta de entrada al vault. Todo está enlazado desde aquí: no busques carpetas, sigue enlaces.

---

## 🎯 El proyecto en 30 segundos

| Pregunta | Respuesta |
|---|---|
| **¿Qué?** | Sistema para digitalizar, validar y transmitir las actas **E-14** del exterior |
| **¿Dónde?** | Consulados y embajadas (7 días de votación, cierre diario 16:00 hora local) |
| **¿Qué actas?** | Solo **Delegados** y **Transmisión**. El ejemplar de **Claveros** NO entra al pipeline digital |
| **¿Con qué?** | **PWA Digitalizador** (móvil, offline-first) + **Panel de Supervisión** (command center) |
| **¿Volumen?** | 1.489 mesas (lun-sáb) y hasta 2.181 el domingo pico |
| **¿Estado?** | ERS consolidado, congelado como "Versión Pre-Desarrollo" → arranque de Sprint 1 |

---

## 🗺️ Mapa del vault

| Carpeta | Qué contiene | Cuándo entrar |
|---|---|---|
| `00 INICIO` | Este mapa, la guía de Obsidian, convenciones y pendientes | Siempre, al abrir el vault |
| `01 CONTEXTO ELECTORAL` | Cómo funciona el E-14, el código de barras, el proceso y los volúmenes | Para entender el **negocio** |
| `02 ARQUITECTURA Y REQUISITOS` | ERS, reglas de negocio, flujo de validación, perfil operativo | Para construir o **decidir** |
| `03 PWA DIGITALIZADOR` | Estados del acta e interfaz gráfica del digitalizador | Para trabajar en el **producto móvil** |
| `04 SISTEMA DE SUPERVISIÓN` | Panel global, anomalías, SLA, carga masiva, auditoría | Para trabajar en el **producto web** |
| `05 DISEÑO UI` | Design system, boceto del formulario y capturas HTML/PNG de cada pantalla | Para **diseñar pantallas** |
| `06 RECURSOS` | Actas E-14 de ejemplo (PDF) | Como **material de referencia** |
| `07 DIARIO` | Notas diarias: decisiones del día, avances, dudas | Rutina diaria |
| `08 PLANTILLAS` | Plantillas reutilizables | Al crear notas repetitivas |
| `09 INGENIERÍA Y AGENTES` | El desarrollo real: motor de captura, lógica de aprobación, agentes, PRs y backlog | Para **construir o entender el código** |

---

## 📌 Notas clave por tema

### 🧩 Contexto electoral (qué estamos digitalizando)
- [[Que datos contiene un Acta E-14]] — anatomía completa del formulario
- [[Estructura y Significado del Código de Barras]] — los 15 dígitos y su lectura
- [[Proceso de Digitalización de las Actas E-14]] — el flujo oficial en Colombia
- [[Digitalización en el Exterior]] — por qué el exterior es distinto (7 días, CNE)
- [[Mesas y volumen electoral 2026]] — cuántas mesas hay y dónde
- [[Volumen de páginas (Presidenciales 2026)]] — cuántas páginas por mesa

### 🏗️ Arquitectura y requisitos
- [[Especificación de Requerimientos (ERS)]] — **documento maestro** del sistema
- [[Flujo de Validación y Aprobación Operativa]] — quién aprueba qué y con qué score
- [[Perfil operativo y arquitectura del sistema]] — usuario, hardware y PWA ligera

### 📱 Producto 1: PWA Digitalizador
- [[Estados del Acta E-14 en la PWA]] — los 5 estados y sus reglas
- [[Interfaz Gráfica (Digitalizador)]] — las 8 pantallas explicadas pantalla por pantalla

### 🖥️ Producto 2: Sistema de Supervisión
- [[Panel de Control y Supervisión Global]] — dashboard macro y KPIs
- [[Módulo de Revisión de Anomalías]] — bandeja de incidentes
- [[Centro de Notificaciones (SLA)]] — control de mora y escalamiento
- [[Módulo de Carga Masiva de Actas]] — contingencia por lotes
- [[Modal de Auditoría - Revisión de Acta]] — decisión final con trazabilidad

### 🎨 Diseño
- [[Design System - Precision Monitor]] — colores, tipografía y componentes (en `05 DISEÑO UI`)
- `05 DISEÑO UI/Boceto - Plantilla guía E-14.jpg` — boceto del formulario usado como guía de encuadre (ver [[Interfaz Gráfica (Digitalizador)]])
- Capturas: `05 DISEÑO UI/Digitalizador/` y `05 DISEÑO UI/Supervisor/` (cada carpeta trae `code.html` + `screen.png`) ⚠️ *No incluidas en este vault — ver [[Backlog y decisiones pendientes]] C-6*

### 📎 Recursos
- `06 RECURSOS/Actas E-14 de ejemplo/` — 4 PDF de actas reales de ejemplo

### ⚙️ Ingeniería y agentes (el trabajo real de desarrollo)
**El proyecto ya está construido y desplegado:** [web-scanner](https://github.com/Jg-Stevan/web-scanner) → [app E-14](https://jg-stevan.github.io/web-scanner/) · [laboratorio](https://jg-stevan.github.io/web-scanner/lab/)

- ⚙️ [[Inicio - Ingeniería y Agentes]] — **puerta de entrada a toda la ingeniería**
- 🧠 [[Modelo del Acta E-14]] — la anatomía real del formulario y DIVIPOL
- 🎯 [[Doctrina del score y evaluarGate]] — **la respuesta a las contradicciones del vault**
- 🔍 [[Clasificación DIVIPOL]] — cómo se identifica un acta
- 🛡️ [[Guard de completitud (borde)]] — "la geometría sospecha, la tinta condena"
- 🎚️ [[Estados, intentos y contingencias]] — 5 estados, 4 intentos, rescate por recorte
- 📷 [[Cámara, autocaptura y burst]] · [[Memoria, render y tiers]] · [[Trampas de iOS y WebKit]]
- 🤖 [[Método de trabajo (spec, implementa, audita)]] · [[Reglas de trabajo con agentes]]
- 📊 [[Estado del repositorio web-scanner]] · [[Índice de Pull Requests]] · [[Backlog y decisiones pendientes]]

---

## ⚠️ Pendientes críticos

- [x] Contradicciones 1, 2 y 3 RESUELTAS por la implementación real → [[Contradicciones detectadas]]
- [x] Escenario confirmado: Presidenciales 2026, segunda vuelta, exterior (depto 88 = consulado)
- [x] ~~Cerrar el PR #47~~ → nunca se mergeó; guard-borde #44 vigente ([[Estado del repositorio web-scanner]])
- [x] ~~Merge #39 y #43~~ → mergeados (desbloquearon LOTE 2 #48)
- [x] ~~Rebasar Stitch~~ → mergeado como PR #50 (HEAD b8cfb01)
- [x] ~~Cuarentena 99 REVISAR~~ → la carpeta no existe en el repo; referencias eliminadas
- [ ] 🔴 **Siguiente frente: módulo de supervisión** → [[Contrato PWA → Supervisión]]

Ver detalle y siguientes pasos en [[Tablero de próximos pasos]].

---

## 🧠 Aprender a usar esto como cerebro digital

- [[Introducción al Cerebro Digital]] ← **empieza por aquí si eres nuevo** (cómo usar el vault como segundo cerebro)
- [[Obsidian 101 - Tu cerebro digital]] ← el manual completo de Obsidian
- [[Convenciones del vault]] ← cómo nombramos y clasificamos
- [[Tablero de próximos pasos]] ← qué hacer ahora
- [[Contradicciones detectadas]] ← lo que hay que aclarar

---

## 📑 Documentos maestros

- [[Resumen de Cambios Implementados]] ← **qué se construyó y qué cambió** (ponte al día en una lectura)
- [[Inicio - Ingeniería y Agentes]] ← la puerta de entrada a toda la ingeniería
- [[Backlog y decisiones pendientes]] ← la lista viva de lo que falta

---

*Última reorganización: 2026-10-13. Backup previo en `C:\Users\JG\Documents\BACKUPS_OBSIDIAN\`.*
