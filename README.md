# Audio Editor

Editor de audio y vídeo para músicos en práctica instrumental — standalone, sin frameworks, sin dependencias externas.

**[▶ Abrir en el navegador](https://cjlkaiser-cpu.github.io/audio-editor/)**

---

## Características

### Carga y grabación
- Drag-and-drop, selector de archivo o grabación directa de micrófono / cámara
- Formatos soportados: MP4, MOV, WebM, MP3, WAV, M4A, OGG
- **Grabación con auto-start** — espera automáticamente hasta detectar sonido por encima de un umbral configurable
- Historial de archivos recientes (IndexedDB — reabre sin file picker)
- Selección de dispositivo de entrada con detección de cambios en caliente

### Forma de onda y navegación
- Visualización canvas de alta resolución con altura **redimensionable** (arrastrar handle inferior)
- Minimap overview con viewport arrastrable
- Ruler de tiempo adaptativo (ms o s según zoom)
- Pan por arrastre, zoom con rueda / teclado / pinch
- **Marcadores A/B arrastrables** — reposicionar las líneas de loop directamente en la forma de onda
- Touch completo en móvil (tap=seek · drag=pan · pinch=zoom)

### Reproducción y práctica
- Control de velocidad sin cambio de pitch — 0.5× a 1.25× (`preservesPitch`)
- Transpose por semitonos ±1 / ±2 / ±7 / ±12 vía `playbackRate`
- Loop A-B con historial de regiones nombradas (guardar / restaurar)
- **Modo Drill** — al terminar la región activa salta automáticamente a la siguiente guardada
- **Comparación con referencia** — alterna entre la grabación propia y una referencia profesional (tecla `Q`)
- Reproducción simultánea de tomas de comparación sincronizadas (hasta 3)

### Metrónomo y análisis rítmico
- **Metrónomo visual configurable** — BPM manual + compás (4/4, 3/4, 6/8, 5/4, 2/4). Grid verde en la forma de onda + punto pulsante en cada beat
- Detección BPM automática (onset-based) con cuadrícula visual y snap de A/B al beat más cercano
- **Curva de tempo / rubato** — BPM local beat-a-beat: muestra aceleraciones y frenadas

### Análisis de afinación
- **Curva de afinación** — desviación en cents a lo largo del tiempo (verde ±15¢ · ámbar ±30¢ · rojo >30¢) + estadística media y % en tono. Altura **redimensionable**
- **Pitch detection** en tiempo real durante reproducción (YIN, badge nota + cents)

### Análisis de señal
- Detección de onsets con visualización sobre la forma de onda y control de sensibilidad
- Espectrograma en scroll continuo (log 30–12000 Hz)
- Curva de dinámica superpuesta a la forma de onda
- VU meter con peak hold
- Goniómetro estéreo (correlación de fase L/R)
- LUFS (EBU R128 simplificado, región A-B o completo)
- Piano EQ preset (high-pass 60Hz + mid peaking 900Hz + high-shelf 6kHz + compresor)

### Edición destructiva
- Fade in / fade out en región A-B
- Silenciar región
- Reverse de región
- Noise gate con umbral configurable (−60 a −5 dB)
- Auto-trim (detecta inicio/fin de contenido, ajusta A-B)
- Normalización de ganancia
- Undo stack general hasta 12 operaciones

### Anotaciones
- **Panel de anotaciones** — Shift+clic abre un modal con etiqueta corta + texto libre. Panel cronológico debajo del editor con seek al hacer clic
- Marcadores de posición con etiqueta (clic derecho para eliminar)

### Vídeo
- Preview sincronizado a la forma de onda
- Loop A-B, zoom, seek, velocidad — mismo workflow que audio
- Export clip A-B como WebM con progreso en %
- Grabación webcam + micrófono simultáneos
- Export frame actual como PNG

### UX / Interfaz
- Tema claro / oscuro con toggle persistente
- Controles organizados en 3 secciones colapsables: **Reproducción · Editar · Analizar**
- Status chips en la barra de archivo (BPM activo, velocidad ≠ 1×, transpose, duración A-B)
- Export WAV (completo + clip A-B)
- PWA instalable — funciona offline
- Compatible Safari (wrapper explícito de `decodeAudioData` con callbacks)

---

## Atajos de teclado

| Tecla | Acción |
|---|---|
| `Espacio` | Play / Pause |
| `A` / `B` | Marcar punto A / B del loop |
| `L` | Activar / desactivar loop |
| `Z` | Zoom a región A-B |
| `R` | Reset zoom (100%) |
| `←` / `→` | ±5 segundos |
| `+` / `-` | Zoom in / out |
| `Esc` | Limpiar región A-B |
| `N` | Siguiente región guardada (con zoom automático) |
| `Q` | Alternar entre grabación propia y referencia cargada |
| `Shift + clic` | Añadir anotación (etiqueta + texto libre) |
| `Clic derecho` | Eliminar marcador cercano |
| Arrastrar línea A / B | Reposicionar marcador de loop |

---

## Stack técnico

```
Vanilla JS + Web Audio API + Canvas API
Sin bundler · Sin framework · Sin dependencias externas
```

| API | Uso |
|---|---|
| `AudioContext` / `OfflineAudioContext` | Decodificación, graph, export WAV |
| `HTMLMediaElement` | Fuente de audio unificada (audio y vídeo) |
| `MediaRecorder` | Grabación de micrófono y export de clip de vídeo |
| `IndexedDB` | Historial de archivos (blobs completos, hasta 8 recientes) |
| `ServiceWorker` + `manifest.json` | PWA offline e instalable |
| YIN algorithm | Pitch detection, downsampling ×4 (~11025 Hz efectivo) |

---

## Uso local

Abre directamente `index.html` en el navegador (doble clic o `open index.html`). No hay build step ni servidor necesario.

> Recomendado Chrome o Edge. Safari soportado con limitaciones (Web Audio API parcial).
