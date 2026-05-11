# Audio Editor — Ideas y Roadmap

Editor de audio y vídeo para músicos en práctica instrumental.
Herramienta standalone, sin frameworks, pensada para correr en el navegador.

**Demo:** https://cjlkaiser-cpu.github.io/audio-editor/

---

## Estado actual (v0.7 — mayo 2026)

### Audio
- Carga por drag-and-drop, selector, o grabación directa de micrófono
- Visualización de forma de onda con altura redimensionable (arrastrar handle)
- Reproducción con cursor en tiempo real + atajos de teclado completos
- Marcadores A/B arrastrables directamente en la forma de onda
- Selección de región A-B, loop, zoom a región
- Historial A-B (guardar/restaurar regiones con nombre)
- **Modo Drill** — salta automáticamente a la siguiente región guardada al terminar
- **Comparación con referencia** — alterna entre grabación propia y referencia (tecla Q)
- Anotaciones con texto libre vinculadas a posición (Shift+clic, panel cronológico)
- Control de velocidad sin cambiar pitch (0.5×–1.25×, `preservesPitch`)
- Transpose por semitonos (±1/2/7/12) vía `playbackRate`
- **Metrónomo visual configurable** — BPM manual + compás (4/4, 3/4, 6/8, 5/4, 2/4) + beat flash
- Detección automática de BPM (onset-based al cargar) + cuadrícula visual + snap A/B
- Detección de onsets con visualización sobre la forma de onda
- Pitch detection en tiempo real (YIN, badge nota + cents)
- Curva de afinación — desviación en cents sobre el tiempo, con altura redimensionable
- Curva de tempo / rubato — BPM local beat-a-beat, muestra aceleraciones/frenadas
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
- **Grabación con auto-start** — espera sonido por encima de umbral configurable

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
- Touch completo en móvil (tap=seek, drag=pan, pinch=zoom)
- PWA: manifest + service worker, instalable en escritorio/móvil, offline
- Integración iframe: protocolo postMessage (`ready`, `load`, `recording-done`, `export-clip`)
- Compatible Safari: wrapper con callbacks para `decodeAudioData`

---

## Roadmap de mejoras pendientes

### Bloque E — Práctica instrumental avanzada ✅ COMPLETADO

| # | Feature | Estado |
|---|---|---|
| E1 | **Ciclo de regiones / Drill** | ✅ Tecla N + botón Drill con auto-advance |
| E2 | **Comparación con referencia** | ✅ Carga ref + tecla Q alterna sin mezclar |
| E3 | **Metrónomo visual configurable** | ✅ BPM manual, compases, grid + beat flash |
| E4 | **Anotaciones de texto en el tiempo** | ✅ Modal etiqueta+nota, panel cronológico |
| E5 | **Grabación con auto-start por umbral** | ✅ Toggle + umbral configurable en overlay |

---

### Bloque F — Persistencia y flujo de sesión

| # | Feature | Notas |
|---|---|---|
| F1 | **Persistencia de anotaciones y regiones** | Guardar marcadores, anotaciones y regiones A-B en IndexedDB vinculadas al archivo (nombre+tamaño). Se restauran al reabrir desde el historial |
| F2 | **Export de sesión como JSON** | Exportar configuración de práctica (regiones, anotaciones, BPM, tempo) para compartir o restaurar en otra sesión |
| F3 | **Contador de repeticiones en drill** | Contador visible en el chip de status durante el modo Drill. Útil para saber cuántas veces has practicado cada región |
| F4 | **Curvas de afinación superpuestas** | Al tener referencia cargada, mostrar ambas curvas de afinación (propia en color, referencia en gris) sobre el mismo canvas |
| F5 | **Historial de sesión de práctica** | Log timestamped de regiones practicadas + repeticiones. Exportable como CSV para seguimiento a largo plazo |

---

### Bloque C — Complejo (dependencias externas o mucho trabajo)

| # | Feature | Notas |
|---|---|---|
| C1 | **Exportación MP3 / OGG** | ffmpeg.wasm lazy-load (~30MB). Carga solo al exportar. El más demandado universalmente |
| C2 | **Noise reduction espectral** | 2 pasadas: análisis estadístico del ruido de fondo → sustracción espectral. Vs gate simple actual |
| C3 | **EQ paramétrico** | 5–8 bandas ajustables (freq, gain, Q) vs Piano EQ fijo actual |
| C4 | **Grabación con overdub** | Escuchar pista anterior mientras grabas la nueva. Timing crítico con Web Audio |
| C5 | **Audio externo + vídeo sync** | Reemplazar pista de audio del vídeo con grabación externa (WebCodecs) |

---

### Bloque G — UX / Pulido

| # | Feature | Notas |
|---|---|---|
| G1 | **Overlay de atajos** | Tecla `?` muestra panel con todos los atajos de teclado |
| G2 | **Zoom vertical de la forma de onda** | Amplificar visualmente la señal en canvas sin modificar el audio. Útil para señales de baja amplitud (p.ej. voz suave) |
| G3 | **Modo pantalla completa** | La forma de onda ocupa toda la pantalla para análisis detallado |
| G4 | **Snap de marcadores de anotación** | Al añadir una anotación, opción de snapear al onset o beat más cercano |

---

### Bloque D — Infraestructura

| # | Feature | Notas |
|---|---|---|
| D1 | **Tests Playwright** | Smoke tests: carga de archivo, play/pause, loop, export |
| D2 | **Versioning semántico** | Marcar releases en git tags (v0.5, v0.6, v0.7…) |

---

## Orden de implementación recomendado

```
F1              (persistencia — alta utilidad práctica, bajo coste)
F3 + G1         (polish rápido)
G2              (zoom vertical — muy pedido para señales débiles)
F2              (export sesión — convierte la herramienta en algo compartible)
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
- `decodeAudioData`: siempre usar wrapper con callbacks explícitos (compatibilidad Safari)
- Referencia E2: snapshot de `{buffer, peaks, dynCurve, blobUrl}` para swap sin recargar grafo completo
- Metrónomo E3: BPM manual > BPM detectado como fallback
