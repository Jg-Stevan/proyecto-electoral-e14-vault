---
proyecto: Proyecto Electoral E-14
tipo: contrato
estado: propuesta
actualizado: 2026-10-14
tags: [pwa, supervision, contrato, integracion]
---

# Contrato PWA → Supervisión

## §1 Qué emite hoy la PWA (salida real, verificada)
- Estado final de cada acta: ÓPTIMA (≥8.0), ADVERTENCIA (6.5–7.9), RECHAZADA (<6.5) + bandera  (contingencia intento 4).
- Motivos de rechazo: ILEGIBLE / GENERICO / INCOMPLETA / BAJO.
- Historial de intentos por acta (score + razón por intento; el recorte no consume intento).
- Datos DIVIPOL (AUTO/SUGERIDA/MANUAL).
- Resultado del guard de completitud (veto) y techo aplicado.

## §2 Qué consume cada módulo del panel
- Ver [[Panel de Control y Supervisión Global]], [[Modal de Auditoría - Revisión de Acta]], [[Módulo de Revisión de Anomalías]], [[Centro de Notificaciones (SLA)]], [[Módulo de Carga Masiva de Actas]].

## §3 Reglas de la interfaz
- "La geometría sospecha, la tinta condena": el panel no recalifica por imagen, solo por contenido y anomalías formales.
-  `EN_REVISION_HUMANA` por intento 4 es contingencia esperada.
- ADVERTENCIA viaja con marca de revisión para auditoría posterior.
- Feedback en UI persistente (sin modales).

## §4 Gaps abiertos
- [ ] Sincronización offline → servidor.
- [ ] Formato exacto del payload.
- [ ] Quién asigna SLA.
- [ ] Visualización del historial por intento.

