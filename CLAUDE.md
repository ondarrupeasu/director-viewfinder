# director-viewfinder

App **web tipo visor de director** (director's viewfinder / simulador de focal): usando la
cámara del móvil, previsualiza el **encuadre/recorte** que daría una **cámara de cine +
focal** concretas. Al estilo de *Artemis Director's Viewfinder* o *Cadrage* (apps nativas de
pago), pero en web: cero instalación, gratis, multiplataforma.

**Estado:** proyecto nuevo, sin código todavía. Este CLAUDE.md recoge el diseño ya decidido en
la sesión donde nació la idea (7 jul 2026). Preferencia: base **HTML autocontenida** si es
viable (sin build), como el proyecto hermano `webar-sensor-lab`.

## Qué debe hacer (concepto)

El móvil es un *proxy* de la cámara real. Se captura vídeo con la cámara del móvil y se
**recorta** para que el encuadre coincida con el que daría la cámara/óptica de destino. Encima,
la **máscara del ratio** de rodaje (2.39:1, 16:9, 1.85:1, 4:3…).

Hay **dos "cámaras/ópticas" distintas** en juego — no mezclarlas:
- **(A) Cámara del móvil** que captura → define el ángulo de visión *de partida* en pantalla.
- **(B) Cámara de cine + focal** que se quiere **simular** (Alexa + 35 mm, RED + 85…) → la
  elección creativa, lo que de verdad se previsualiza.

## Viabilidad (ya valorada)

Viable en web. La **matemática es fácil**; el punto crítico es la **calibración del FOV del móvil**.

- **Ángulo de visión** = `2·atan(anchoSensor / (2·focal))`. Con el FOV del móvil (A) y el FOV
  objetivo (B) se calcula el factor de recorte: `crop = tan(FOV_B/2) / tan(FOV_A/2)`.
  - `crop < 1` → recortar hacia dentro (zoom digital): viable, pierde resolución.
  - `crop > 1` → habría que ver MÁS ancho de lo que capta el móvil: **imposible** → avisar y/o
    exigir la cámara ultra gran angular.

## Enfoque de calibración decidido (para el MVP)

El navegador **NO expone el FOV/focal real de la cámara** de forma fiable (iOS Safari es
especialmente cerrado). Solución sin depender de eso:

1. **Paso inicial de setup: "modelo de móvil → cámara"**, con una **tabla de FOV por
   dispositivo y cámara** (igual que hacen las apps nativas). El usuario elige su modelo y qué
   cámara usa; de ahí sale el FOV de partida (A). Detectar el modelo por web es poco fiable, así
   que **que lo elija el usuario**.
2. **Capturar siempre con la cámara más angular del móvil (ultra gran angular)** y recortar
   hacia dentro para simular cualquier focal más cerrada. El teleobjetivo deja casi sin margen;
   la ultra gran angular permite simular desde muy abierto hasta muy cerrado.

Ejemplo de datos (iPhone 12 Pro): Ultra gran angular 13 mm (~108–120°), Gran angular 26 mm
(~70°), Teleobjetivo 52 mm (~38°). Guardar por cámara el **ángulo horizontal (y vertical) en
grados** — con eso basta para el recorte, sin necesidad de dimensiones de sensor.

## Caveats prácticos (probar en dispositivo real, no solo en desktop)

- **HTTPS obligatorio**: `getUserMedia` exige contexto seguro. `file://` no sirve; en local solo
  desktop. Deploy tipo GitHub Pages como `webar-sensor-lab`.
- **Acceso a una lente concreta en iOS por web es quisquilloso**: aunque pidas la ultra gran
  angular, Safari puede darte la principal. Hay que **probarlo en iPhone** y tener plan B
  (enumerar `mediaDevices`, seleccionar por label/capabilities; si falla, calibrar sobre la que
  dé). Este es el mayor riesgo técnico del proyecto.

## Fases sugeridas

- **F1 (MVP):** vídeo de cámara + **máscara de ratio** + setup **modelo/cámara** (tabla FOV) +
  **recorte por focal objetivo**. Un puñado de modelos y una lista corta de cámaras de cine.
- **F2:** catálogo de **cámaras de cine** (sensores: Alexa, RED, Sony Venice, S35, FF…) y de
  **ópticas**; guardar/recuperar configuraciones de rodaje.
- **F3:** **calibración fina** opcional (encuadrar un objeto de tamaño conocido a distancia
  conocida) para corregir la tabla.

## Sinergias (proyectos del mismo autor)

- **ClapperQR** ya tiene un **catálogo de ópticas** (`lenses.json`, cine+foto, offline). Este
  visor puede **comer de ahí** o ser una función/hermano de ClapperQR.
- Mismo cálculo de FOV que el **"modo de encuadres"** del juego `realizacion-puzzle`
  (webar-sensor-lab).
- Reutiliza el patrón `getUserMedia` + geometría de encuadre de `webar-sensor-lab`.

## Perfil del autor (para calibrar respuestas)

Profesional audiovisual con 25+ años (realización, cámara, montaje, postproducción) y profesor
de FP de imagen y sonido. Pensamiento metódico, de ingeniería. Experto en el dominio
audiovisual; beginner-friendly en toolchain JS moderno. Trato de tú, registro masculino, tono de
colega.

Preferencias de trabajo: pensar antes de actuar; leer archivos antes de escribir; editar solo lo
que cambia, no reescribir enteros; sin preámbulos ni resúmenes de relleno; testear antes de dar
por hecho; si algo es ambiguo, decirlo en vez de rellenar.
