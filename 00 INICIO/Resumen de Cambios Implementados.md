---
proyecto: Proyecto Electoral E-14
tipo: mapa
estado: vigente
actualizado: 2026-10-13
tags: [moc, resumen, cambios, implementados]
---

# 📑 Resumen de Cambios Implementados

> **El documento maestro para saber qué se ha construido, qué cambió y dónde está la verdad.**
> Si solo vas a leer una nota del vault para ponerte al día, lee esta.

---

## 1. El proyecto en una tabla

| Pregunta | Respuesta |
|---|---|
| **¿Qué es?** | Sistema para digitalizar, validar y transmitir las actas **E-14** del exterior |
| **¿Dónde?** | Consulados y embajadas (7 días, cierre diario 16:00 h local) |
| **¿Qué actas?** | Solo **Delegados** (alta) y **Transmisión** (secundaria). Claveros no entra |
| **¿Con qué?** | **PWA Digitalizador** (móvil, offline-first) + **Panel de Supervisión** (command center) |
| **¿Volumen?** | 1.489 mesas (lun-sáb) y hasta 2.181 el domingo pico |
| **¿Escenario?** | Presidenciales 2026, **segunda vuelta**, exterior (depto 88 = consulados) |
| **¿Estado?** | Construido y desplegado: [web-scanner](https://github.com/Jg-Stevan/web-scanner) → [app E-14](https://jg-stevan.github.io/web-scanner/) · [laboratorio](https://jg-stevan.github.io/web-scanner/lab/) |

---

## 2. Los 3 cambios de paradigma (lo que de verdad se implementó)

### 2.1 · Criterio de calidad: de "binario" a "precedencia + bandas + pisos"
- **Antes (conflicto del vault):** el ERS decía binario (`≥9` pasa), pero el flujo y los estados decían 3 bandas.
- **Ahora (implementado):** una **precedencia de 3 pasos** con umbrales concretos.

```
1. COMPLETITUD (veto)    → acta cortada: INCOMPLETA + techo 6.4
2. SEÑAL (techo)         → sin contenido de acta: techo 4.9
3. SCORE (gradúa)        → (sharpness×0.4 + brightness×0.3 + contEff×0.3)/10
   Techos por level: fair 7.9 · poor 6.4
   Pisos: contraste legible 80 · identificada 10  (Math.max puro)
Bandas: ≥8.0 OPTIMA · 6.5-7.9 ADVERTENCIA · <6.5 RECHAZADA
```

> **Detalles:** [[Doctrina del score y evaluarGate]]. El ERS RN-02 estaba desactualizado; **falta emitir la v2.0**.

### 2.2 · Hardware: de "rango fijo" a "tiers adaptativos"
- **Antes:** el vault decía 2-4 GB (perfil) o 4-8 GB (ERS RNF-02). Ninguno correcto.
- **Ahora:** tiers medidos con datos reales, **tope único 4032 px** no negociable.

| Dispositivo | Tier | Resolución |
|---|---|---|
| Galaxy A13 (4 GB) | `low` (`deviceMemory ≤ 4`) | **3200 px** (solo downscale, jamás recorte) |
| Galaxy A56 (8 GB) | `high` | **4032 px** |
| iPhone | sin `deviceMemory` | 4032 (fuente única desde v11) |

> **Detalles:** [[Memoria, render y tiers]].

### 2.3 · Identificación: de "ACTA NO RECONOCIDA" a clasificación DIVIPOL con rescate
- **Antes:** si el OCR fallaba, el acta quedaba en "CLASIFICACIÓN PENDIENTE".
- **Ahora:** clasificador con trigramas + coeficiente de Dice, 3 niveles (AUTO ≥0.82 / SUGERIDA 0.60-0.81 / MANUAL <0.60) y **rescate global por nombre** contra los 14.438 puestos.

> **Detalles:** [[Clasificación DIVIPOL]] (regla 4b, el caso El Cairo).

---

## 3. Cambios por subsistema (el trabajo de código real)

### 3.1 · Motor de captura (`scanner-core`)
| Cambio | Qué resolvió |
|---|---|
| **Tope único 4032 px** | Estar justo bajo el límite del canvas de iOS (~16,7 MP). *"Un tope mayor rompe iPhone"* |
| **F-LENS v4** (sondeo secuencial + regex anti gran angular) | El bug de producción: los Android capturaban con la gran angular |
| **F-ZSL** (Best-Shot de 8 frames, 80-450 ms antes del toque) | Anti tap-shock (Zero Shutter Lag) |
| **Autocaptura invariante al FPS (v9)** | Ventana temporal de 1200 ms en vez de 6 muestras (no disparaba a ≥24 fps) |
| **Burst con presupuesto de tiempo** (saga v1→v5) | El A13 tardaba 16,8-17,2 s; `photoGraceMs = 2000` recuperó 4032 px |
| **Fuente única v11** (PNG 4032 en todos los tiers) | *"el editor se ve peor que la pantalla completa"* → resuelto |
| **v12 "editor primero"** | Probe barato → `addCapturePage` inmediato → decode pesado a los 2,5 s |
| **v12b `capturePending`** | Cámara oculta durante la ventana del disparo (E2E 16/16 con UA Android) |
| **LOTE 2 (F2-E, spec entregada)** | Preview escalonado 2048→4032 contra el warp de 38,4 s en el A13 |

> **Detalles:** [[Arquitectura del motor (scanner-core)]], [[Cámara, autocaptura y burst]], [[Memoria, render y tiers]].

### 3.2 · Lógica de aprobación (`digitalizador-e14`)
| Cambio | Qué resolvió |
|---|---|
| **`evaluarGate` con historial** | `score + ocrTexto + clasificacion + intento + maxIntentos` |
| **4 intentos** (antes 2) | *"Más de 3 veces" del dueño = 4 capturas / 3 repeticiones |
| **Contingencia automática al intento 4** | `EN_REVISION_HUMANA` + fila en historial + toast + badge |
| **Recorte como mecanismo de rescate** | Re-ejecuta el pipeline completo; **no consume intento**; **pasa por el guard** |
| **`PISO_IDENTIFICADA = 10`** | "el OCR manda sobre el score": acta identificada = digitalización plena = 10.0 |
| **`contrasteEfectivoDe` (piso 80)** | Una foto de documento tiene contraste global bajo por diseño → no castigarla |
| **Panel de corrección en cascada** | `dep → mun/país → zona → puesto → mesa` con sugerencia precargada |
| **Regla 4b** (rescate global por nombre) | El Cairo con cabecera recortada: de MANUAL a SUGERIDA |
| **Eliminación del export PDF del e14** | Decisión del dueño (*"sip elimina el pdf"*); el lab lo conserva |

> **Detalles:** [[Doctrina del score y evaluarGate]], [[Estados, intentos y contingencias]], [[Guard de completitud (borde)]], [[El puente - scanner-core-bridge]].

### 3.3 · Telemetría y diagnóstico
| Cambio | Qué resolvió |
|---|---|
| **Marks del pipeline con nombre fijo** | Los E2E de auditoría dependen de ellos: no renombrar |
| **Sondas en `window`** | QA sin instrumentar (`__perfProbe`, `__diagTools`, `__burstTools`...) |
| **Informe de diagnóstico (JSON v1→v5)** | TXT (WhatsApp) + JSON (comparar). Sin imágenes, sin `ocrText` (§1.5) |
| **Juez objetivo fuera del repo** | `analizador-diag-v4.js` (node puro, umbrales públicos). *"Mismo JSON → mismo veredicto"* |
| **Baseline 2026-10-12** | A56, A13 y PC medidos; 3 hallazgos que orientaron todo |

> **Detalles:** [[Telemetría, diagnóstico y baseline]].

---

## 4. Las contradicciones que se resolvieron

| # | Contradicción | Resolución |
|---|---|---|
| 1 | Criterio de calidad (¿binario o 3 bandas?) | ✅ Precedencia + bandas + pisos ([[Doctrina del score y evaluarGate]]) |
| 2 | Hardware objetivo (¿2-4 GB o 4-8 GB?) | ✅ Tiers adaptativos, tope 4032 ([[Memoria, render y tiers]]) |
| 3 | Escenario (¿Congreso o Presidencial?) | ✅ Presidenciales 2026, 2ª vuelta, exterior ([[Modelo del Acta E-14]]) |
| 4 | `ADVERTENCIA` es un estado muerto | 🟠 Abierta: aceptar código defensivo o retunear el piso |
| 5 | §9.12 "papel sin votos" | 🟠 Diseño preliminar listo, decisión pendiente |
| 6 | Flujo de 2 páginas sin implementar | 🟠 Abierta |
| 7 | ERS congelado con inconsistencias | 🟠 Falta emitir v2.0 |
| 8 | Enlace roto a PDF `EL CAIRO D - DOMINGO.pdf` | 🟡 Convertido a texto; pendiente subir archivo |

> **Detalles completos:** [[Contradicciones detectadas]].

---

## 5. Lo que se eliminó (decisiones explícitas del dueño)

| Eliminado | Motivo |
|---|---|
| **Export PDF del e14** | *"sip elimina el pdf"* — nunca fue parte de la transmisión |
| **CTA manual "enviar a revisión humana"** | 0 usos; higiene |
| **Botón "Auto" en la fila** | Sobrevive vía HUD; armado por defecto |
| **`ISO: 1600` como HUD canónico** | `SPEC-camara-funcional` ordenó eliminarlo |
| **Benchmark de dispositivo completo** | Penalizaba a iOS y degradaba el dato |
| **Toast/notificaciones flotantes** | *"cero mensajes"*; la app debe resolver sola |
| **Sistemas visuales previos al Stitch** | Stitch prohíbe los anteriores (`grep = 0`) |

> **Detalles:** [[El puente - scanner-core-bridge]] §4, [[Trampas de iOS y WebKit]], [[Contradicciones detectadas]].

---

## 6. Lo que falta
- 🔴 **Definir el contrato de datos PWA → Supervisión** → [[Contrato PWA → Supervisión]]

### 🔴 Bloqueantes
- **Cerrar el PR #47** (revert del guard-borde) — 30 segundos, riesgo mayor
- **Mergear #39 (burst) y #43 (F2)** — desbloquea el LOTE 2
- **Rebasar el rediseño Stitch** sobre `main` con auditoría E2E

### 🟠 Decisiones de producto
- Resolver el estado `ADVERTENCIA` muerto
- Aprobar (o no) §9.12 "papel sin votos"
- Implementar el flujo de 2 páginas
- Emitir la v2.0 del ERS
- Decidir Plan A vs Plan B de memoria/render

### 🔵 Desarrollo
- Implementar el LOTE 2 (preview escalonado)
- Resolver los 35 commits `v13-post-N`
- Gates físicos G1-G4 de F2 en el A13
- Pruebas físicas: cámara 50 MP, HEIC, linterna, auto armada, foto real El Cairo

> **Detalle completo:** [[Backlog y decisiones pendientes]] y [[Tablero de próximos pasos]].

---

## 7. Mapa rápido de dónde está cada cosa

| Si buscas… | Abre… |
|---|---|
| El modelo del formulario | [[Modelo del Acta E-14]] · [[Que datos contiene un Acta E-14]] |
| El código de barras de 15D | [[Estructura y Significado del Código de Barras]] |
| La regla de decisión de calidad | [[Doctrina del score y evaluarGate]] |
| Cómo se identifica un acta | [[Clasificación DIVIPOL]] |
| El veto de bordes | [[Guard de completitud (borde)]] |
| Los estados del acta | [[Estados, intentos y contingencias]] · [[Estados del Acta E-14 en la PWA]] |
| La arquitectura del motor | [[Arquitectura del motor (scanner-core)]] |
| El puente entre motor y aprobación | [[El puente - scanner-core-bridge]] |
| Cámara y burst | [[Cámara, autocaptura y burst]] |
| Memoria y rendimiento | [[Memoria, render y tiers]] |
| El caso iOS | [[Trampas de iOS y WebKit]] |
| Telemetría y baseline | [[Telemetría, diagnóstico y baseline]] |
| El panel del supervisor | [[Panel de Control y Supervisión Global]] |
| La bandeja de anomalías | [[Módulo de Revisión de Anomalías]] |
| El control de mora | [[Centro de Notificaciones (SLA)]] |
| La carga masiva | [[Módulo de Carga Masiva de Actas]] |
| El modal de auditoría | [[Modal de Auditoría - Revisión de Acta]] |
| Las pantallas de la PWA | [[Interfaz Gráfica (Digitalizador)]] |
| El ERS | [[Especificación de Requerimientos (ERS)]] |
| El flujo de validación | [[Flujo de Validación y Aprobación Operativa]] |
| El backlog completo | [[Backlog y decisiones pendientes]] |

---

## 8. La constitución del proyecto (las reglas que no se rompen)

> **"El rendimiento y la estabilidad se ganan con memoria, CPU de análisis y CSS — NUNCA degradando el dato de imagen."**

De ahí se derivan:
1. **Tope único 4032 px** — *"un tope mayor rompe iPhone"*
2. **WYSIWYG estricto** — *"lo que ves ES la imagen guardada"*
3. **Downscale sí, recorte no** — el piso de 3200 px de gama baja es post-captura
4. **R-10: data URLs en PNG** — *"no convertir a JPEG"*
5. **Error #22 del repo** — nunca `toDataURL` síncrono en canvas grande → `toBlob`
6. **Regla de oro #0** — el lab es la fuente de verdad; se copia literal con `// Fuente: …`

> **Detalles:** [[Arquitectura del motor (scanner-core)]] §3 y §4.

---

*Última actualización de este resumen: 2026-10-13. Si el repo avanza, actualiza esta nota y [[Backlog y decisiones pendientes]].*

---

**Ver también:** [[Inicio - Proyecto Electoral]] · [[Inicio - Ingeniería y Agentes]] · [[Tablero de próximos pasos]] · [[Contradicciones detectadas]]
