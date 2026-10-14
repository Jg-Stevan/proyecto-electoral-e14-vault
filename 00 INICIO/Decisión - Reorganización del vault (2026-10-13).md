---
proyecto: Proyecto Electoral E-14
tipo: decision
estado: aprobado
actualizado: 2026-10-13
tags: [decision, vault]
---

# Decisión — Reorganización del vault (2026-10-13)

**Fecha:** 2026-10-13
**Estado:** aprobada

## 1. La decisión

> Se reorganiza el vault con **una sola capa de carpetas numeradas**, se añaden **propiedades** a todas las notas, se repara la red de **enlaces** y el material dudoso pasa a **cuarentena** en lugar de borrarse.

## 2. Contexto

El vault tenía 19 notas mezcladas con adjuntos de diseño, carpetas de nombres automáticos, material de otro escenario electoral y 229 MB de archivos sin nombre legible. Se usaba como almacén de archivos, no como cerebro digital. Objetivo declarado: **aprender a usar Obsidian como segundo cerebro** y organizar las ideas del proyecto.

## 3. Opciones evaluadas

| Opción | Ventajas | Desventajas |
|---|---|---|
| **A. Dejarlo como estaba** | Cero riesgo | El problema crecía; nada era recuperable por búsqueda |
| **B. Reorganización total con backup + cuarentena** | Recuperable, mejora inmediata, base para el método | Requiere aprender las convenciones nuevas |
| **C. Borrar lo "obsoleto" directamente** | Rápido | Pérdida irreversible de material quizá útil |

## 4. Por qué elegimos esta

La opción B da el beneficio sin el riesgo: **backup verificado por hash antes de tocar nada** y **cuarentena revisable** en vez de borrado. Nada desaparece sin decisión humana.

## 5. Consecuencias

- **Qué cambia:**
  - Carpetas: `00 INICIO` … `08 PLANTILLAS` + `99 REVISAR - candidatos a eliminar`
  - Las 18 notas del proyecto tienen propiedades (`tipo`, `estado`, `tags`, `actualizado`)
  - Los 8 enlaces a capturas de diseño fueron reescritos a las rutas nuevas
  - 5 notas nuevas: mapa, guía de Obsidian, convenciones, tablero y contradicciones
  - 4 plantillas y notas diarias configuradas
- **Qué se movió a cuarentena:** el escenario de Congreso 2026, el design system duplicado, 2 ZIP de código y 218 MB de archivos sin nombre
- **Qué se borró:** solo 13 `desktop.ini` (metadatos de Windows) y 14 carpetas vacías
- **Notas afectadas:** todas (ver `00 INICIO/Registro de migración (2026-10-13).csv`)

## 6. Cuándo revisar esta decisión

- Cuando el equipo crezca y haga falta decidir si el vault se comparte (Obsidian Sync / Git)
- Si el número de carpetas de primer nivel supera 12 → replantear

## Evidencia

- Backup: `C:\Users\JG\Documents\BACKUPS_OBSIDIAN\PROYECTO-ELECTORAL_2026-10-13_1818.zip` (+ inventario SHA-256)
- Verificación: 85 archivos originales contabilizados (68 intactos por hash + 17 modificados a propósito), **0 enlaces rotos**
