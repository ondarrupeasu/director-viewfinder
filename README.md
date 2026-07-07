# director-viewfinder

Visor de director (director's viewfinder) en **web autocontenida**: usa la cámara del móvil
como proxy y previsualiza el **encuadre** que daría una **cámara de cine + focal** concretas,
con la **máscara del ratio** de rodaje encima.

## F1 (MVP) — lo que hay ahora

- Vídeo de la cámara a pantalla completa (`getUserMedia`, cámara trasera).
- **Máscara de ratio** seleccionable: 2.39 / 1.85 / 16:9 / 4:3 / 1:1 / 9:16.
- Setup **móvil → cámara** con **tabla de FOV** por cámara (arranca con el iPhone 12 Pro).
- Cámara de cine a simular + **focal objetivo** → recorte digital por `crop = tan(FOVₐ/2)/tan(FOV_b/2)`.
- Aviso cuando la focal es demasiado abierta para la cámara elegida (no se puede des-recortar).

Todo en un único `index.html`, sin build ni dependencias.

## Uso en local

`getUserMedia` exige contexto seguro (HTTPS o `localhost`). En desktop:

```
python3 -m http.server 8000
# abrir http://localhost:8000
```

En móvil hay que servirlo por HTTPS → **GitHub Pages**.

## Deploy en GitHub Pages

Repo → *Settings* → *Pages* → *Deploy from a branch* → `main` / `/ (root)`.
El `.nojekyll` evita el procesado Jekyll. Al ser HTTPS, la cámara funciona en el móvil.

## Roadmap

- **F2:** catálogo ampliado de cámaras de cine y ópticas; más modelos de móvil; guardar/recuperar
  configuraciones. Posible sinergia con el catálogo de ópticas de ClapperQR.
- **F3:** calibración fina opcional (objeto de tamaño conocido a distancia conocida).

## Nota técnica (iOS)

El acceso a una lente concreta por web en iOS Safari no está garantizado (puede darte la
principal aunque pidas la ultra gran angular). Es el mayor riesgo del proyecto y hay que
probarlo en dispositivo real.
