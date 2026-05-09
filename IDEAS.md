# Audio Editor — Ideas y Roadmap

Editor de audio y vídeo para músicos en práctica instrumental.
Herramienta standalone, sin frameworks, pensada para correr en el navegador.

**Demo:** https://cjlkaiser-cpu.github.io/audio-editor/

---

## Estado actual (v0.5 — mayo 2026)

### Audio
- Carga por drag-and-drop, selector, o grabación directa de micrófono
- Visualización de forma de onda (canvas 180px), zoom, scroll, overview minimap
- Reproducción con cursor en tiempo real + atajos de teclado completos
- Selección de región A-B, loop, zoom a región
- Historial A-B (guardar/restaurar regiones con nombre)
- Label markers (Shift+clic añadir, clic derecho eliminar)
- Control de velocidad sin cambiar pitch (0.5×–1.25×, `preservesPitch`)
- Transpose por semitonos (±1/2/7/12) vía `playbackRate`
- Detección automática de BPM (onset-based al cargar) + cuadrícula visual + snap A/B
- Detección de onsets con visualización sobre la forma de onda
- Pitch detection en tiempo real (YIN, badge nota + cents)
- **Curva de afinación** — desviación en cents sobre el tiempo, media y % en tono
- **Curva de tempo / rubato** — BPM local beat-a-beat, muestra aceleraciones/frenadas
- Espectrograma en scroll continuo (log 30–12000 Hz)
- Curva de dinámica superpuesta a la forma de onda
- VU meter con peak hold + goniómetro estéreo
- LUFS (EBU R128 simplificado, región o completo)
- Piano EQ preset (3 filtros + compresor)
- Normalización de ganancia
- Noise gate destructivo con undo
- Fade in / fade out destructivo en región A-B con undo
- Silenciar región A-B con undo
- Reverse región A-B con undo
- Auto-trim (detecta inicio/fin, ajusta A-B)
- Undo stack general (hasta 12 operaciones, cubre todas las ops destructivas)
- Multi-take comparison (hasta 3 tomas, waveforms sincronizados + reproducción simultánea)
- Export WAV (completo + clip A-B) con feedback visual
- Historial de archivos recientes (IndexedDB, reabre sin file picker)
- Selección de dispositivo de entrada (`enumerateDevices()` + `devicechange`)

### Vídeo
- Carga MP4/MOV/WebM con preview sincronizado a la forma de onda
- Waveform extraída del audio del vídeo
- Loop A-B, zoom, seek, velocidad — funciona igual que en audio
- Export clip de vídeo (A-B) vía MediaRecorder → WebM con progreso %
- Grabación webcam + micrófono simultáneos
- Export frame actual como PNG

### UX / Infraestructura
- UI dark/light con toggle persistente (CSS custom properties)
- Controles organizados en 3 secciones colapsables: Reproducción · Editar · Analizar
- Status chips en file-bar (BPM, velocidad, transpose, duración A-B)
- Región A-B con gradiente y líneas de acento superior/inferior
- Playhead con triángulo superior
- Drop zone con animación pulsante al arrastrar
- Touch completo en móvil (tap=seek, drag=pan, pinch=zoom)
- PWA: manifest + service worker, instalable en escritorio/móvil, offline
- Integración iframe: protocolo postMessage (`ready`, `load`, `recording-done`, `export-clip`)

---

## Roadmap de mejoras pendientes

### Bloque E — Práctica instrumental avanzada

| # | Feature | Notas |
|---|---|---|
| E1 | **Ciclo de regiones de práctica** | Tecla `N` salta a la siguiente región del historial A-B. Modo "drill": al acabar la región activa, salta automáticamente a la siguiente |
| E2 | **Comparación con referencia** | Cargar una grabación de referencia (p.ej. grabación profesional) y alternar entre ella y la propia con una tecla. Distinto de multi-take: la referencia no se mezcla, se alterna |
| E3 | **Metrónomo visual configurable** | Grid de compases con compás elegible (4/4, 3/4, 6/8, 5/4…) y tempo manual. Independiente del BPM detectado |
| E4 | **Anotaciones de texto en el tiempo** | Marcadores con texto libre adjunto (no solo etiqueta corta). Panel lateral o tooltip expandible |
| E5 | **Grabación con auto-start por umbral** | Inicia REC al detectar sonido por encima de un umbral. Útil para no tener que pulsar el botón justo al empezar a tocar |

### Bloque C — Complejo (dependencias externas o mucho trabajo)

| # | Feature | Notas |
|---|---|---|
| C1 | **Exportación MP3 / OGG** | ffmpeg.wasm lazy-load (~30MB). Carga solo al exportar. El más demandado universalmente |
| C2 | **Noise reduction espectral** | 2 pasadas: análisis estadístico del ruido de fondo → sustracción espectral. Vs gate simple actual |
| C3 | **EQ paramétrico** | 5–8 bandas ajustables (freq, gain, Q) vs Piano EQ fijo actual |
| C4 | **Grabación con overdub** | Escuchar pista anterior mientras grabas la nueva. Timing crítico con Web Audio |
| C5 | **Audio externo + vídeo sync** | Reemplazar pista de audio del vídeo con grabación externa (WebCodecs) |

### Bloque D — Infraestructura

| # | Feature | Notas |
|---|---|---|
| D1 | **Tests Playwright** | Smoke tests: carga de archivo, play/pause, loop, export |
| D2 | **Versioning semántico** | Marcar releases en git tags (v0.3, v0.4, v0.5…) |

---

## Orden de implementación recomendado

```
E1 → E2 → E3   (práctica instrumental — son la diferencia con herramientas genéricas)
E5              (grabación cómoda)
C1              (MP3 export — depende de ffmpeg.wasm)
C3              (EQ paramétrico)
D1              (tests al final, cuando la API sea estable)
```

---

## Notas técnicas

- Todo vanilla JS + Web Audio API + Canvas. Sin bundler, sin framework.
- `preservesPitch` / `webkitPreservesPitch` para velocidad sin cambio de pitch
- Transpose: `playbackRate = speed × 2^(semis/12)`
- YIN pitch detection: downsampling ×4 → sr efectivo ~11025 Hz, cubre ≥43 Hz
- Curva afinación: YIN batch en ventanas 50ms/hop 50ms → smooth 5-frame → cents desviación
- Curva rubato: detección onsets (sens 0.35) → IOI → BPM local → smooth 5-frame
- IndexedDB para historial de archivos (blobs completos, hasta 8 recientes)
- MediaRecorder para export de clip de vídeo (tiempo real, output WebM)
- ffmpeg.wasm para MP3/OGG (lazy-load bajo demanda, ~30MB) — pendiente implementar
- postMessage para integración iframe (protocolo estable desde v0.1)
- Undo stack: array de `{buffer, label}`, máximo 12 entradas, LIFO
