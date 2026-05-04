# 🐚 ShellSynth

> Un sintetizador-concha donde la forma es el timbre.
> *A conch-shell synthesizer where shape is timbre.*

ShellSynth es una interfaz audiovisual experimental construida sobre la observación de que la geometría interna de una caracola marina y la serie armónica comparten una intuición de crecimiento, resonancia y proporción. Suena con síntesis aditiva real de Web Audio y se controla con un pad XY sobre una concha generativa.

**Live demo:** [vlasvlasvlas.github.io/shellsynth](https://vlasvlasvlas.github.io/shellsynth/)

---

## Concepto

> *"See sound harmonics. Hear a conch shell."*

La fuente del proyecto es la observación, popularizada por Stephanie McPeak Petersen y Pradeep Kariyawasam, de que la radiografía de una caracola y el diagrama de los primeros nueve armónicos de una cuerda comparten una geometría sorprendente: ondas estacionarias entre dos focos forman la lente que también dibujan las cámaras internas de la concha.

ShellSynth no afirma que la concha *genere* la serie armónica. Usa esa relación visual como interfaz: la concha funciona como mapa del timbre y la serie armónica es la matemática que efectivamente suena.

Tres modos conviven en el mismo modelo:

- **Componer** — sumar armónicos uno a uno y ver cómo nace la concha.
- **Esculpir** — modificar amplitudes con sliders, presets de especies o el pad XY.
- **Performar** — tocar como un instrumento (press-to-play, sonido sostenido, secuencias automatizadas, barrido continuo).

Inspirado en Bernstein (*The Unanswered Question*, 1973) y en referencias visuales sobre la relación entre diagramas armónicos y radiografías de conchas.

---

## Características

### Audio engine (Web Audio API)
- **16 osciladores** en síntesis aditiva, uno por armónico.
- **4 formas de onda**: sine / triangle / square / saw.
- **Just Intonation `=`** (ratios enteros 1, 2, 3, …) o **Equal Temperament `≈`** (12-TET) — la diferencia es audible y visual en la alineación de las ondas.
- **Tonic transposable** (C, C#, D, … B) y 10 escalas: Major, Minor, Pentatonic Major/Minor, Phrygian, Hijaz, Yaman, Bhairav, Whole Tone, Chromatic.
- **Cadena de FX en línea**: master volume, reverb por convolución (IR oceánica generada), delay con feedback (cap 92% para evitar runaway), vibrato (LFO sobre `detune`).
- **Kill switch de colas**: cuando un press termina o se pausa el path, las colas de reverb/delay se silencian rápidamente para un stop real.

### Visualización
- **Wave panel** (izquierda): ondas armónicas separadas (`SEP`) o superpuestas (`SUP`). La velocidad y altura de oscilación están alineadas entre ambos paneles (`omega = 2π · 0.25 · ratio`).
- **Living Shell** (derecha): tres vistas:
  - `ESP` — espiral logarítmica X-ray con cámaras coloreadas, costillas radiales y vibración por armónico.
  - `ARM` — ondas estacionarias entre dos focos, inspirado en la referencia Bernstein/Petersen.
  - `SHELL` — concha de perfil translúcida, coloreada por preset y deformada por los armónicos activos.
- **Grid `⊞`** toggleable en la toolbar (overlay de líneas punteadas en ambos canvas).
- **Fullscreen `⛶`** por panel.

### Modos de interacción

| Modo | Cómo se activa | Comportamiento |
|---|---|---|
| **Toggle cámara** | Click corto en una cámara de la concha | Enciende / apaga ese armónico individual. |
| **Pad XY (press-to-play)** | Hold > 180 ms o drag > 8 px sobre la concha | X = barrido del peak armónico (gaussiana sobre los 16 parciales). Y = pitch que snappea a la nota más cercana de la escala elegida, transponiendo todo el armónico en cascada. |
| **Spacebar** | Mantener `Space` apretado | Activa el pad XY usando la última posición del mouse sobre la concha. Mover el mouse mientras `Space` está apretado actualiza el sonido en tiempo real. |
| **HOLD** | Botón `HOLD` en el toggle de la concha | Cuando está **off** (default): el sonido vive solo mientras presionás (release silencia con kill de colas). Cuando está **on**: el sonido queda sostenido al soltar. |
| **PATH** | Botón `PATH` + click sobre la concha | Crea waypoints numerados. Doble-click sobre un punto lo borra. `↔ / → / ←` elige sentido de circulación (ping-pong, forward, reverse). `▶ / ■` reproduce o detiene. `✕` borra todos los puntos. `Path Speed` define el tiempo total de recorrido (1–30 s). El playhead recorre el camino disparando el pad XY automáticamente. |
| **Mute por armónico** | Botón `S` en cada slider | Silencia ese armónico preservando el valor del slider. |
| **Atajos de teclado** | `1`–`9` toggle armónicos, `+` agrega siguiente, `?` ayuda. | |

### Touch
Pointer events nativos — funciona en mobile / tablet / pen sin código adicional.

### Presets de conchas

| Preset | Especie | Spiral b | Carácter sonoro |
|---|---|---|---|
| 🌀 Nautilus | *Nautilus pompilius* | 0.176 | Balanceado, decay gradual |
| 🐚 Murex | *Murex pecten* | 0.31 | Espiral abierta, armónicos altos prominentes |
| 🕉️ Shankha | *Turbinella pyrum* | 0.22 | Fundamental fuerte, armónicos impares |
| 🔱 Triton | *Charonia tritonis* | 0.19 | Armónicos medios protagónicos |
| 🌊 Strombus | *Strombus gigas* | 0.25 | Fundamental masivo, agudos débiles |
| 🌻 Phi | abstracción matemática | 0.306 | Espiral áurea, decay por 1/φ |
| ∅ Empty | — | — | Estado vacío para empezar de cero |

Los presets respetan `HOLD`: con `HOLD` off, cargar un preset prepara los sliders y la concha pero queda en silencio hasta que toques. Con `HOLD` on, el sonido queda sostenido al instante.

---

## Instalación

ShellSynth es un único `index.html` sin build step ni dependencias. Tres formas de correrlo localmente:

### A — Servidor con Python (recomendado)

```bash
git clone https://github.com/vlasvlasvlas/shellsynth.git
cd shellsynth
python3 -m http.server 8080
```

Abrí `http://localhost:8080`.

### B — Servidor con Node

```bash
npx serve . -p 8080
```

### C — Abrir directo

```bash
open index.html
```

(Algunos navegadores bloquean el `AudioContext` con `file://`. Si no suena, usá A o B.)

---

## Uso recomendado

1. Cargá `Nautilus` o `Phi`. Sliders se llenan, concha se forma, silencio.
2. Subí `Reverb` a 50% y `Delay` a 20% con `Time` 600 ms.
3. Tocá sobre la concha sosteniendo el click — escuchá el pad XY en vivo.
4. Activá `PATH`, marcá 4 puntos formando un patrón, dale `▶`, ajustá `Path` a 12 s.
5. Cambiá `Scale` a `Bhairav` o `Hijaz` para tonalidad oriental.
6. Alterná `=` / `≈` con varios armónicos prendidos para escuchar y ver cómo cambia la alineación de las ondas.
7. Switcheá la vista del shell entre `ESP`, `ARM` y `SHELL`.
8. Para tocar en continuo: prendé `HOLD`, cargá Phi + Saw + Vibrato 30 y ajustá el pad XY.

---

## Atajos

| Tecla | Acción |
|---|---|
| `Space` (sostenida) | Activar el pad XY desde la última posición del mouse |
| `1`–`9` | Toggle del armónico H1–H9 |
| `+` / `=` | Agregar el siguiente armónico |
| `?` / `h` | Abrir / cerrar help overlay |
| `Esc` | Salir de fullscreen |

---

## Tecnología

- **HTML / CSS / JS vanilla**, sin build, sin frameworks, sin dependencias.
- **Web Audio API**: `OscillatorNode`, `GainNode` con `setTargetAtTime` para automation suave, `DynamicsCompressorNode`, `ConvolverNode` (IR sintética), `DelayNode` con feedback loop, LFO sobre `detune` para vibrato.
- **Canvas 2D** para los dos paneles de visualización.
- **Pointer Events** unificados para mouse + touch + pen.
- **GitHub Actions** para deploy automático a GitHub Pages.

---

## Fuentes

- Stephanie McPeak Petersen — [Harmonics and the Conch Shell](https://stephaniemcpeakpetersen.substack.com/p/harmonics-and-the-conch-shell)
- Pradeep Kariyawasam — [Connection Between Harmonic Waves and Conch Shells](https://www.kariyawasam.com/connection-between-harmonic-waves-and-conch-shells/)
- Leonard Bernstein — *The Unanswered Question*, Norton Lectures, Harvard, 1973
