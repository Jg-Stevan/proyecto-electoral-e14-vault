---
proyecto: Proyecto Electoral E-14
tipo: mapa
estado: vigente
actualizado: 2026-10-15
tags: [pendientes, tablero]
---

# ✅ Tablero de próximos pasos

> Checklist viva. Marca lo que completes. Vuelve aquí cada semana.
> El detalle de cada punto abierto está en [[Backlog y decisiones pendientes]].

---

## 🔴 Decisiones que bloquean

- [x] ~~Resolver las contradicciones del vault~~ → **resueltas por la implementación** ([[Contradicciones detectadas]])
- [x] ~~Decidir el criterio de calidad~~ → **precedencia + bandas + pisos** ([[Doctrina del score y evaluarGate]])
- [x] ~~Decidir el hardware objetivo~~ → **tiers adaptativos** ([[Memoria, render y tiers]])
- [x] ~~Decidir el escenario electoral~~ → **Presidenciales 2026, 2ª vuelta, exterior**
- [x] ~~🔴 Cerrar el PR #47 (el revert del guard-borde)~~ → nunca se mergeó; guard-borde #44 vigente
- [x] ~~Decidir el merge de PR #39 (burst) y PR #43 (F2)~~ → mergeados
- [x] ~~Decidir el orden de integración: v12b → rebase v13 → re-auditoría~~ → resuelto por la cadena de merges
- [x] ~~Rebasar el rediseño Stitch sobre main con auditoría E2E~~ → mergeado como PR #50 (HEAD b8cfb01)
- [ ] Decidir qué hacer con el estado muerto **ADVERTENCIA**
- [ ] Aprobar (o no) el diseño de **§9.12** ("papel completo sin votos")

---

## 🟡 Orden del vault

- [x] Reorganizar en carpetas numeradas
- [x] Reparar los enlaces a las capturas de diseño
- [x] Añadir propiedades (frontmatter) a las notas
- [x] Crear la sección **09 INGENIERÍA Y AGENTES** (21 notas)
- [x] Marcar las contradicciones resueltas
- [x] **Rellenar el archivo vacío** `Guard de completitud (borde)` → [[Guard de completitud (borde)]]
- [x] **Crear las notas fantasma** referenciadas pero inexistentes: [[Inicio - Ingeniería y Agentes]], [[Modelo del Acta E-14]], [[Backlog y decisiones pendientes]], [[Estado del repositorio web-scanner]], [[Índice de Pull Requests]], [[Método de trabajo (spec, implementa, audita)]], [[Reglas de trabajo con agentes]], [[Lecciones y trampas]], [[Design System - Precision Monitor]]
- [x] **Crear los 3 documentos maestros**: [[Introducción al Cerebro Digital]], [[Resumen de Cambios Implementados]] y el MOC de ingeniería
- [ ] Revisar `99 REVISAR - candidatos a eliminar` y decidir qué se elimina
      - `ELECCIONES CONGRESO 2026.md` → **puedes borrarlo** (confirmado: es otro escenario)
      - El resto: ver el `LEEME` de esa carpeta
- [ ] Decidir si los 2 ZIP de código salen del vault hacia un repositorio
- [ ] Renombrar las carpetas de capturas (`digitalizador_captura_autom_tica_e_14` → nombres legibles)

---

## 🟢 Contenido del proyecto

- [ ] **Emitir la v2.0 del ERS** con el modelo real de decisión (la fuente está en [[Doctrina del score y evaluarGate]])
- [ ] Documentar la **matriz de contingencias** en su propia nota (hoy vive dentro del flujo)
- [ ] Crear una nota por módulo del sistema con su checklist de aceptación
- [ ] Definir los **formatos de exportación** del módulo de informes (PDF / Excel / CSV)
- [ ] Recuperar o descartar el PDF faltante `EL CAIRO D - DOMINGO.pdf`

---

## 🔵 Desarrollo (el trabajo real)

- [x] ~~Cerrar el PR #47 y limpiar la rama `revert-44`~~ → nunca se mergeó; guard-borde #44 vigente
- [x] ~~Mergear **#39** y **#43**~~ → mergeados (desbloquearon LOTE 2 #48)
- [x] ~~Implementar el LOTE 2 (preview escalonado 2048→4032)~~ → mergeado como PR #48
- [ ] Decidir sobre **Plan A** (rebase tras el burst) y **Plan B** (condicionada)
- [ ] Resolver los **35 commits `v13-post-N`** fuera de alcance: QA físico o descartar
- [ ] Correr los **gates físicos G1-G4** de F2 en el A13
- [ ] Recrear las ramas `qa-v11/v12/v12b` si quieres URLs estables de Vercel
- [ ] Pruebas físicas pendientes: cámara 50 MP, HEIC, linterna, auto armada, foto real de El Cairo

---

## 🧠 Aprender Obsidian (tu cerebro digital)

- [x] Leer [[Obsidian 101 - Tu cerebro digital]]
- [ ] Hacer el ejercicio práctico de 15 minutos del final
- [ ] Crear tu primera nota diaria en `07 DIARIO`
- [ ] Probar una plantilla de `08 PLANTILLAS`
- [ ] Activar el plugin **Dataview** y construir un tablero automático por `estado`
- [ ] Ver el grafo y detectar notas huérfanas

---

## 💡 Ideas sueltas

- [ ] Convertir este vault en plantilla reutilizable para futuros proyectos electorales
- [ ] Un "panel de mando" del proyecto hecho con **Canvas**
- [ ] Exportar el ERS a PDF/DOCX para compartir con quien no use Obsidian
- [ ] Unificar los dos mundos: **una nota de "estado del sistema"** que junte motor + aprobación en un solo diagrama

---

## 🔵 Infraestructura post-split (2026-10-15) — pendientes del dueño

- [x] ~~Separar el monorepo en repos~~ → **HECHO**: web-scanner (`a9b6b83`) + digielect14 (`fbac5de`), CI 4/4 verde, demos vivas ([[Estado del repositorio web-scanner]])
- [x] ~~Publicar el motor como librería~~ → **HECHO**: `@jg-stevan/scanner-core@0.1.0` en npm, verificada por un consumidor externo
- [ ] `bunx npm unpublish @jg-stevan/scanner-core@0.0.0-stage` (placeholder vacía del publish de prueba — dentro de las 72h)
- [ ] Cierre del archivo `web-scanner-monorepo-viejo`: Settings → Pages → None + **Archive this repository**
- [ ] Copiar `.env.example` de `apps/scanner-lab` al nuevo web-scanner (quedó en el archivo)
- [ ] Reconectar Vercel Previews (Vercel SOLO previews de PR; Pages = única producción)
- [ ] Branch protection en ambos repos: require PR + check `Quality Gate` + block force push
