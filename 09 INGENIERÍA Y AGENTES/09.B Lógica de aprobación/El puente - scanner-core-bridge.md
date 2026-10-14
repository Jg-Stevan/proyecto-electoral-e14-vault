---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, e14, bridge, arquitectura]
---

# El puente — scanner-core-bridge

> El punto exacto donde **el motor entrega la imagen** y **el juez decide el destino del acta**. Es el archivo que conecta las dos líneas de trabajo.

## 1. Dónde vive la lógica de aprobación

```
apps/digitalizador-e14/src/lib/e14/
├── bridge.ts                # contrato
├── get-bridge.ts            # selector de implementación
├── scanner-core-bridge.ts   # ⭐ el puente real + evaluarGate
├── clasificador.ts          # identificación DIVIPOL
├── completitud.ts           # guard de borde
├── divipol.ts               # carga de la base
├── store.ts                 # estado de la app
├── types.ts / seed.ts       # tipos y datos semilla
└── image-utils.ts
```

**Dato clave:** las reglas de aprobación **viven en código, no en los specs**. Por eso el vault no las tenía: había que leer el repo.

## 2. La arquitectura del bridge (v2)

| Pieza | Rol |
|---|---|
| **`get-bridge.ts`** | Todos los componentes piden el bridge aquí; **las vistas nunca importan implementaciones** |
| **`CompositeBridge`** | Enruta según la **fuente de captura** |
| **`MockBridge`** | Fuente `SIMULACION` — red de seguridad del demo |
| **`RealCoreBridge`** | Fuente real (`CAMARA` \| `ARCHIVO`) con el pipeline completo |

**Contrato asíncrono:** `async` con `onProgreso` — así la UI puede mostrar el avance sin bloquearse.

## 3. El pipeline real (5 etapas)

```
detectDocumentEdges  →  processImage(quad, filtro)  →  calidad/score
        →  OCR  →  clasificación DIVIPOL  →  evaluarGate
```

**Orden importante (v16):** la **clasificación corre PRIMERO** (es barata y cacheada) y `evaluarGate` pasa a ser **síncrona** con la tabla de decisión.

```
evaluarGate(score, ocrTexto, clasificacion, intento, maxIntentos)
```

## 4. Lo que se eliminó del puente (SPEC ENVIADA Rev. 2)

El flujo **EXPORTAR PDF se eliminó del e14** por decisión explícita del dueño (*"sip elimina el pdf"*).

**Verificación de seguridad previa (7 bloques mapeados):** `buildDocPdf` solo alimentaba `downloadBlob` (descarga local) — **el PDF nunca fue parte de la transmisión** (`enviarActa`, cola offline y SHA no dependían de él). Cadena de borrado:

| Sitio | Qué se quitó |
|---|---|
| `ReviewView` L26 | `DownloadIcon` del import |
| `ReviewView` L746 | El selector |
| `ReviewView` L749-770 | El branch `if(esReal)` → colapsa a un `return` único |
| `ReviewView` L750-751 | `esReal` de `ReviewCtas` sin uso (⚠️ el de L250 **se queda**) |
| `store.ts` L85-86 + L498-509 | Interfaz + acción |
| `bridge.ts` L37-38 + L91-97 + L10 | Contrato |
| `scanner-core-bridge.ts` L42-45 + L755-801 | Import + método `exportarPdf` |

**Guardrail:** `packages/scanner-core/pdf-export` **no se tocó** (el lab sí lo importa en `EditorView` y `LibraryView`) → regla de oro intacta.

## 5. Filtros y export

| Elemento | Decisión |
|---|---|
| `PageFilter` | `"original" \| "text" \| "bw"` |
| Filtro por defecto | **B/N adaptativo** |
| Filtro "Texto claro" (F-TEXT-CLEAN) | LUT sigmoidal + afilado **solo en bordes de tinta** (el papel nunca se toca) + reconstrucción cromática que **preserva el color de la tinta** |
| **FILTRAR (F2)** | **Solo `processImage` — sin re-OCR** (prohibido re-evaluar el gate por cambiar de filtro) |
| R-10 | Los data URLs de filtros son **PNG**, no JPEG |

## 6. Modo SIMULACIÓN vs real

| Fuente | Qué hace |
|---|---|
| `SIMULACION` | Corre el **pipeline real** sobre el acta E-14 real incluida; los chips fuerzan el score. La completitud se omite **solo** aquí |
| `CAMARA` | Pipeline completo con cámara del dispositivo |
| `ARCHIVO` | Importación (incluye HEIC y fotos nativas de 12-48 MP) |

**Regla:** la simulación **nunca** puede relajar una regla del flujo real. Las guardas están explícitas en código (piso AUTO solo con `scoreSimulado >= 8`).

---

**Ver también:** [[Doctrina del score y evaluarGate]] · [[Clasificación DIVIPOL]] · [[Guard de completitud (borde)]] · [[Arquitectura del motor (scanner-core)]]
