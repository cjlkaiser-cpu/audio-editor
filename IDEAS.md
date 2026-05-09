# Audio Editor — Ideas y Roadmap

Editor de audio (y futuro vídeo) para músicos en práctica instrumental.
Herramienta standalone, sin frameworks, pensada para correr en el navegador.

---

## Estado actual (v0.2 — mayo 2026)

- Carga de audio por drag-and-drop, selector de archivo o grabación directa desde micrófono
- Visualización de forma de onda (canvas), zoom y scroll con overview minimap
- Reproducción con cursor de posición en tiempo real
- Selección de región A-B con marcadores visuales
- Recorte (clip) de la región seleccionada con exportación como WAV (también exportación completa)
- Loop de región A-B
- **Control de velocidad sin cambiar pitch** (0.5×–1.25×, `preservesPitch`)
- **Transpose por semitonos** (±1, ±2, ±7, ±12) vía `playbackRate`
- **Detección automática de BPM** (onset-based, se ejecuta al cargar)
- **Espectrograma** en scroll continuo (log 30–12000 Hz)
- **Curva de dinámica** superpuesta a la forma de onda
- **VU meter** con peak hold
- **Goniómetro estéreo** (correlación L-R)
- **LUFS** (EBU R128 simplificado, region o completo)
- **Detección de onsets** con visualización sobre la forma de onda
- **Pitch detection en tiempo real** (algoritmo YIN, badge nota + cents)
- **Piano EQ** preset (highpass 60Hz + peaking 900Hz + highshelf 6kHz + compresor)
- **Normalización** de ganancia
- **Noise gate** destructivo con undo
- **Auto-trim** (detecta inicio/fin de audio, ajusta A-B)
- **Multi-take comparison** (hasta 3 tomas, waveforms sincronizados con playhead)
- **Historial A-B** (guardar/restaurar regiones con nombre)
- **Label markers** (Shift+clic para añadir, clic derecho para eliminar)
- **Atajos de teclado**: Espacio, A/B, L, Z, R, Esc, ←→ ±5s, +/- zoom
- Integración como iframe: protocolo postMessage (`ready`, `load`, `recording-done`, `export-clip`)
- UI dark, sin dependencias externas

---

## Pendiente — Audio

- [ ] Fade in / fade out en selección (destructivo, como el noise gate)
- [ ] Silenciar región (rellenar con silencio)
- [ ] Undo stack general (actualmente solo noise gate tiene undo)
- [ ] Marcadores de compás con snap a BPM (cuadrícula visual + snap de A/B al beat)
- [ ] Reproducción simultánea sincronizada de dos pistas (main + take en paralelo)
- [ ] Historial de archivos recientes (localStorage)
- [ ] Exportación en múltiples formatos: MP3, OGG, FLAC (WebCodecs o ffmpeg.wasm)
- [ ] Selección de dispositivo de entrada (micrófono / interfaz de audio externa)
- [ ] Monitor de nivel de entrada en tiempo real durante grabación (ya hay osciloscopo, falta VU)
- [ ] Grabación activada por umbral de volumen (auto-start on sound)
- [ ] Grabación con overdub (escuchar pista anterior mientras grabas)

## Pendiente — Vídeo

- [ ] Carga de vídeo (MP4, MOV, WebM) — el `<input>` ya acepta `video/*`
- [ ] Vista previa con `<video>` sincronizado con la forma de onda de audio
- [ ] Waveform extraída del audio del vídeo (vía `audioCtx.decodeAudioData`)
- [ ] Recorte de vídeo por región A-B (equivalente al clip de audio)
- [ ] Exportación de clip de vídeo (WebCodecs API)
- [ ] Modo loop de vídeo en región A-B
- [ ] Sincronización audio externo + vídeo (reemplazar pista de audio del vídeo)
- [ ] Exportación de thumbnails en instante concreto
- [ ] Subtítulos / marcadores de compás superpuestos al vídeo
- [ ] Grabación de vídeo con previsualización (webcam + micrófono)

## Pendiente — UX / Infraestructura

- [ ] Modos de vista: compacto (iframe embebido) vs expandido (standalone)
- [ ] PWA: instalable en escritorio / móvil, funciona offline
- [ ] Soporte móvil: gestos touch para zoom y selección
- [ ] Modo oscuro / claro
- [ ] Tests mínimos de integración (Playwright headless)
- [ ] Versioning semántico (marcar releases en git)

---

## Notas técnicas

- Todo en vanilla JS + Web Audio API + Canvas API. Sin bundler, sin framework.
- WebCodecs API para edición/exportación de vídeo (Chrome 94+, Safari 16.4+)
- ffmpeg.wasm como fallback de exportación (pesado, cargar bajo demanda)
- postMessage para integración con apps de estudio externas (protocolo estable desde v0.1)
- `preservesPitch` / `webkitPreservesPitch` en `HTMLAudioElement` para velocidad sin cambio de pitch
- Transpose usa `playbackRate = speed × 2^(semis/12)` — afecta duración percibida
- YIN pitch detection: downsampling ×4 → sr efectivo ~11025 Hz, cubre ≥43 Hz
