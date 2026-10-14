---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-14
tags: [pwa, captura, stitch]
---

# Flujo de captura (post-Stitch)

## Descripción
La PWA utiliza el rediseño Stitch (PR #50) con shell `h-dvh`. Las vistas son: **escanear → analizando → revisión → actas → resumen**.

## Características
- TopBar solo en revisión.
- BottomNav presente.
- Cero notificaciones flotantes (feedback en UI persistente).
- Pipeline de 6 etapas: DETECTANDO → RECORTANDO → REALZANDO → CALIDAD → OCR → mapeo.
- 4 intentos; contingencia automática al intento 4 → `EN_REVISION_HUMANA`.
- Recorte: re-ejecuta el pipeline, no consume intento.

## Fuentes
- `web-scanner/apps/digitalizador-e14/docs/design/DESIGN.md`
- `web-scanner/apps/digitalizador-e14/docs/design/screens/`
- SPECs en `apps/digitalizador-e14/docs/`.
