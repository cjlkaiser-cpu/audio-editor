# Audio Editor — Ideas y Roadmap

Editor de audio (y futuro vídeo) para músicos en práctica instrumental.
Herramienta standalone, sin frameworks, pensada para correr en el navegador.

---

## Estado actual (v0.1 — mayo 2026)

- Carga de audio por drag-and-drop, selector de archivo o grabación directa desde micrófono
- Visualización de forma de onda (canvas), zoom y scroll
- Reproducción con cursor de posición en tiempo real
- Selección de región con ratón (start / end)
- Recorte (clip) de la región seleccionada con exportación como WAV
- Loop de región seleccionada
- Integración como iframe: protocolo postMessage (`ready`, `load`, `recording-done`, `export-clip`)
- UI dark, sin dependencias externas

---

## Ideas de desarrollo

### Edición de audio
- [ ] Control de velocidad de reproducción sin cambiar pitch (preserving pitch playback)
- [ ] Cambio de pitch sin cambiar velocidad (útil para transponer pasajes)
- [ ] Amplificación / normalización de volumen en región seleccionada
- [ ] Silenciar región (rellenar con silencio)
- [ ] Fade in / fade out en selección
- [ ] Deshacer / rehacer (undo stack)
- [ ] Marcadores de compás: snap a cuadrícula de tiempo (BPM configurable)
- [ ] Detección automática de BPM (onset detection)
- [ ] Espectrograma en paralelo a la forma de onda (vista frecuencial)
- [ ] Exportación en múltiples formatos: MP3, OGG, FLAC (via WebCodecs o ffmpeg.wasm)
- [ ] Modo comparación: dos pistas en paralelo (referencia vs grabación propia)
- [ ] Pitch tracking visual: curva de entonación sobre la forma de onda (pYIN en WASM)
- [ ] Anotaciones de texto ancladas a instantes de tiempo

### Edición de vídeo
- [ ] Carga de vídeo (MP4, MOV, WebM)
- [ ] Vista previa con timeline sincronizado con la forma de onda de audio
- [ ] Recorte de vídeo por región (equivalente al clip de audio)
- [ ] Exportación de clip de vídeo (WebCodecs API)
- [ ] Subtítulos / marcadores de compás superpuestos al vídeo
- [ ] Modo loop de vídeo en región seleccionada
- [ ] Sincronización audio externo + vídeo (reemplazar pista de audio del vídeo)
- [ ] Exportación de thumbnails / capturas en instante concreto

### Grabación
- [ ] Grabación de audio con overdub (escuchar pista anterior mientras grabas)
- [ ] Grabación de vídeo con previsualización (ya implementado en apps de estudio, traer aquí)
- [ ] Selección de dispositivo de entrada (micrófono / interfaz de audio externa)
- [ ] Monitor de nivel de entrada en tiempo real (VU meter)
- [ ] Grabación activada por umbral de volumen (auto-start on sound)

### UX / producto
- [ ] Historial de archivos recientes (localStorage)
- [ ] Modos de vista: compacto (iframe embebido) vs expandido (standalone)
- [ ] Atajo de teclado completo: espacio play/pause, L loop, R grabar, etc.
- [ ] PWA: instalable en escritorio / móvil, funciona offline
- [ ] Modo oscuro / claro
- [ ] Soporte móvil: gestos touch para zoom y selección

### Infraestructura
- [ ] GitHub Pages como URL pública permanente
- [ ] Versioning semántico (v0.1, v0.2…)
- [ ] Tests mínimos de integración (Playwright headless)

---

## Notas técnicas

- Todo en vanilla JS + Web Audio API + Canvas API. Sin bundler, sin framework.
- WebCodecs API para edición/exportación de vídeo (Chrome 94+, Safari 16.4+)
- ffmpeg.wasm como fallback de exportación (pesado, cargar bajo demanda)
- pYIN para pitch tracking: buscar implementación WASM o port JS
- postMessage para integración con apps de estudio externas (protocolo ya estable en v0.1)
