# Audio Editor — Ideas y Roadmap

Editor de audio y vídeo para músicos en práctica instrumental.
Herramienta standalone, sin frameworks, pensada para correr en el navegador.

---

## Estado actual (v0.3 — mayo 2026)

### Audio
- Carga por drag-and-drop, selector, o grabación directa de micrófono
- Visualización de forma de onda (canvas), zoom, scroll, overview minimap
- Reproducción con cursor en tiempo real + atajos de teclado completos
- Selección de región A-B, loop, zoom a región
- Historial A-B (guardar/restaurar regiones con nombre)
- Label markers (Shift+clic añadir, clic derecho eliminar)
- Control de velocidad sin cambiar pitch (0.5×–1.25×, `preservesPitch`)
- Transpose por semitonos (±1/2/7/12) vía `playbackRate`
- Detección automática de BPM (onset-based al cargar)
- Detección de onsets con visualización sobre la forma de onda
- Pitch detection en tiempo real (YIN, badge nota + cents)
- Espectrograma en scroll continuo (log 30–12000 Hz)
- Curva de dinámica superpuesta a la forma de onda
- VU meter con peak hold + goniómetro estéreo
- LUFS (EBU R128 simplificado, región o completo)
- Piano EQ preset (3 filtros + compresor)
- Normalización de ganancia
- Noise gate destructivo con undo
- Fade in / fade out destructivo en región A-B con undo
- Auto-trim (detecta inicio/fin, ajusta A-B)
- Multi-take comparison (hasta 3 tomas, waveforms sincronizados)
- Export WAV (completo + clip A-B) con feedback visual
- Historial de archivos recientes (IndexedDB, reabre sin file picker)

### Vídeo
- Carga MP4/MOV/WebM con preview sincronizado a la forma de onda
- Waveform extraída del audio del vídeo
- Loop A-B, zoom, seek, velocidad — funciona igual que en audio
- Export clip de vídeo (A-B) vía MediaRecorder → WebM con progreso %
- Grabación webcam + micrófono simultáneos

### UX / Infraestructura
- UI dark, sin dependencias externas
- Touch completo en móvil (tap=seek, drag=pan, pinch=zoom)
- PWA: manifest + service worker, instalable en escritorio/móvil, offline
- Integración iframe: protocolo postMessage (`ready`, `load`, `recording-done`, `export-clip`)

---

## Roadmap de mejoras pendientes

### Bloque A — Quick wins (< 1h, alto impacto)

| # | Feature | Notas |
|---|---|---|
| A1 | **Silenciar región A-B** | Rellenar con silencio, destructivo + undo. ~20 líneas |
| A2 | **Selección de dispositivo de entrada** | `enumerateDevices()` + `devicechange`. Clave para músicos con interfaz de audio |
| A3 | **Modo claro / oscuro** | Toggle CSS vars. Las variables ya están centralizadas |
| A4 | **Thumbnail de vídeo** | Exportar frame en el instante del cursor como PNG. ~20 líneas |
| A5 | **VU meter en grabación** | Mostrar nivel dB en tiempo real durante la grabación (el osciloscopo ya existe) |

### Bloque B — Medio (1–3h, alto valor musical)

| # | Feature | Notas |
|---|---|---|
| B1 | **Cuadrícula BPM + snap** | Cuadrícula visual de compases sobre la forma de onda. A/B hacen snap al beat más cercano |
| B2 | **Undo stack general** | Refactorizar el undo actual para cubrir gate + fade + silencio. Historial de N ops |
| B3 | **Reproducción sincronizada main + take** | AudioBufferSourceNode compartiendo `audioCtx.currentTime`. Escuchar dos tomas en paralelo |
| B4 | **Grabación con auto-start por umbral** | Inicia la grabación al detectar sonido por encima de un umbral configurable |
| B5 | **Reverse región A-B** | Invertir muestras en región. Destructivo + undo. ~15 líneas |

### Bloque C — Complejo (requieren dependencias externas o mucho trabajo)

| # | Feature | Notas |
|---|---|---|
| C1 | **Exportación MP3 / OGG** | ffmpeg.wasm lazy-load (~30MB). Carga solo al exportar |
| C2 | **Noise reduction mejorado** | Algoritmo 2 pasadas: análisis estadístico → reducción espectral. Vs gate simple actual |
| C3 | **EQ paramétrico** | 5–8 bandas ajustables (freq, gain, Q) vs Piano EQ fijo actual |
| C4 | **Grabación con overdub** | Escuchar pista anterior mientras grabas la nueva. Timing crítico |
| C5 | **Audio externo + vídeo sync** | Reemplazar pista de audio del vídeo con grabación externa (WebCodecs) |

### Bloque D — Infraestructura

| # | Feature | Notas |
|---|---|---|
| D1 | **Tests Playwright** | Smoke tests: carga de archivo, play/pause, loop, export |
| D2 | **Versioning semántico** | Marcar releases v0.3, v0.4… en git tags |

---

## Orden de implementación recomendado

```
A1 → A2 → A3 → A5   (todo el bloque A en una sesión)
B5 → B1 → B2         (edición + snap + undo stack)
B3                   (reproducción simultánea)
C1                   (MP3 export — depende de ffmpeg.wasm)
C3                   (EQ paramétrico)
D1                   (tests al final, cuando la API sea estable)
```

---

## Notas técnicas

- Todo vanilla JS + Web Audio API + Canvas. Sin bundler, sin framework.
- `preservesPitch` / `webkitPreservesPitch` para velocidad sin cambio de pitch
- Transpose: `playbackRate = speed × 2^(semis/12)`
- YIN pitch detection: downsampling ×4 → sr efectivo ~11025 Hz, cubre ≥43 Hz
- IndexedDB para historial de archivos (blobs completos, hasta 8 recientes)
- MediaRecorder para export de clip de vídeo (tiempo real, output WebM)
- ffmpeg.wasm para MP3/OGG (lazy-load bajo demanda, ~30MB)
- postMessage para integración iframe (protocolo estable desde v0.1)
