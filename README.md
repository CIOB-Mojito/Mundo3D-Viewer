<div align="center">

# Mundo3D-Viewer

**Visor interactivo de modelos 3D en formato GLB**, pensado para funcionar en los navegadores de móviles y ordenadores.

![HTML5](https://img.shields.io/badge/HTML5-E34F26?logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)
![Three.js](https://img.shields.io/badge/Three.js-r160.1-000000?logo=three.js&logoColor=white)

</div>

---

## Descripción

Mundo3D-Viewer es una página web autónoma (`visor-3d.html`) que carga y muestra modelos 3D en formato `.glb` con materiales PBR, iluminación ambiental y direccional, y un mapa de entorno para las reflexiones. Incluye un selector horizontal de modelos, controles de cámara orbitales y tres perfiles de calidad adaptables a distintos dispositivos.

## Características

- **Visualización de modelos GLB** con materiales PBR, luces y mapa de entorno.
- **Selector horizontal de modelos**: una sola fila de tarjetas con desplazamiento táctil y la tarjeta activa resaltada.
- **Cámara orbital** (mediante `OrbitControls`): rotación, zoom y paneo, con inercia. *No existe un modo de cámara libre/piloto.*
- **Rotación automática** opcional del modelo (botón «Rotación»).
- **Perfiles de calidad**: `Rendimiento`, `Equilibrado` y `Calidad`.
- **Modo «Rendimiento» por defecto**: se usa al abrir el visor y se restablece cada vez que se abre o selecciona un modelo.
- **Modelos sin suelo visible** al inicio: el suelo existe en la escena pero permanece oculto (el botón «Suelo» permite mostrarlo).
- **Controles táctiles** para Android: un dedo rota, dos dedos hacen zoom y panean.
- **Animaciones procedurales**: Giro, Flotar, Balanceo, Pulso y Órbita (se pueden activar varias a la vez).
- **Pantalla completa** y **medidor de FPS** opcional.
- **Fondo claro/oscuro**, **sombras** conmutables y **centrado/reset** de la cámara.
- **Carga con barra de progreso** y mensajes de error con opción de reintentar.

## Modelos

Modelos registrados actualmente en el selector:

| Nombre visible | Archivo | ¿Incluido en el repositorio? |
| --- | --- | --- |
| Carro | `Carro.glb` | Sí |
| Conejo | `conejo.glb` | Sí |
| Conejoo | `Conejoo.glb` | Sí |
| CarroPro | `CarroPro.glb` | Sí |
| Caja | `Caja.glb` | Sí |
| Ratón | `Ratón.glb` | Sí |
| Huesped | `Huesped.glb` | Sí |
| No lo sé | `No lo se.glb` | Sí |
| Huesped v2 | `Huesped-v2.glb` | Sí |
| Caja sorpresa | `mystery-box-01.glb` | No |
| Caja Premium | `mystery_box-premiun.glb` | No |

> **Importante:** los archivos `.glb` deben estar **en el mismo directorio que `visor-3d.html`** para que se carguen. Los nombres distinguen mayúsculas/minúsculas y acentos.

> **Caja Premium (apertura interactiva):** este modelo **ya incluye su propia animación** de apertura/cierre (`Flap_Open_Close`) sobre cuatro nodos reales (`Flap_Front/Back/Left/Right`). El visor la reproduce con `THREE.AnimationMixer`, posicionando el clip con la coreografía original (las tapas mayores abren antes que las menores y el cierre es el espejo), de modo que se reutiliza la animación del modelo sin reinventarla. Aparece el botón **Abrir caja / Cerrar caja** cuando se selecciona una de las dos cajas.
>
> **Contenido de la Caja Premium (personajes secuenciales):** la caja funciona como una **caja sorpresa**. Cada apertura revela el **siguiente personaje** de la propia lista del selector, en su orden, **excluyendo las dos cajas** (`mystery-box-01.glb` y `mystery_box-premiun.glb`). Un índice de sesión (`contentSeqIndex`) **avanza solo al abrir** (nunca al cerrar) y vuelve al primero al llegar al final, de modo que la 1.ª apertura muestra el 1.er elegible, la 2.ª el 2.º, y así sucesivamente.
>
> Los modelos se **preparan y guardan en caché** (`contentCache`, recreados en memoria, sin modificar los GLB) para reutilizarlos en aperturas posteriores, con un límite LRU (`CONTENT_CACHE_MAX = 4`) para acotar el uso de memoria/GPU en Android. La escala de cada personaje se ajusta automáticamente al hueco real de la caja. Si un GLB **falla al cargar**, se registra el error y se **pasa al siguiente elegible** sin bloquear la caja (y si ninguno carga, la caja se abre igualmente sin contenido).
>
> Al pulsar **Abrir caja**, la revelación se desarrolla en tres fases mientras las tapas reproducen su animación original:
> 1. **Salida (A):** el objeto espera a que las tapas aclaren, sube suavemente desde el interior hasta superar claramente las tapas (altura final reforzada con `CONTENT_RISE_BOOST = 0.30`) con el resplandor cálido y el pulso de escala de siempre.
> 2. **Acercamiento (B):** el objeto se desliza hacia la **cámara** siguiendo la dirección **real** de la vista (no un desplazamiento fijo en X/Z), con una trayectoria curva suave. Recorre `CONTENT_APPROACH_FRAC = 0.20` (20 %, rango 15–25 %) de la distancia cámara–objeto, gana algo de escala (`CONTENT_DISPLAY_SCALE = 1.12`) y **nunca pierde la altura** de la fase A.
> 3. **Presentación (C):** queda flotando delante y por encima de la caja con un vaivén muy sutil (`CONTENT_FLOAT_AMP = 0.10` de la altura del objeto, período `CONTENT_FLOAT_MS = 4200 ms`), mientras la caja permanece en su sitio. El halo/luz se atenúan tras el destello inicial.
>
> Al pulsar **Cerrar caja**, el objeto **retrocede** hasta su posición sobre la caja (`CONTENT_RETREAT_MS = 300 ms`), **desciende** al interior (`CONTENT_SINK_MS = 600 ms`) y se oculta, coordinado para no atravesar las tapas. La escala se calcula automáticamente para que quepa en el hueco real, y el objeto, la luz y el halo se **reutilizan** en cada apertura/cierre (sin duplicados ni fugas). El acercamiento usa un grupo/conversión a coordenadas locales, así que funciona aunque el usuario gire la cámara antes o después de abrir, y no modifica la cámara global.
>
> *Ajuste fino (en `visor-3d.html`):* `CONTENT_EXCLUDE` (modelos que nunca salen de la caja), `CONTENT_CACHE_MAX` (tamaño de la caché LRU), `CONTENT_RISE_BOOST` (altura de salida), `CONTENT_APPROACH_FRAC` (cuánto se acerca), `CONTENT_APPROACH_MS` (duración), `CONTENT_DISPLAY_SCALE` (tamaño de exhibición), `CONTENT_CURVE`/`CONTENT_ARC_LIFT` (curva y arco del acercamiento), `CONTENT_FLOAT_AMP`/`CONTENT_FLOAT_MS` (vaivén) y `CONTENT_RETREAT_MS`/`CONTENT_SINK_MS` (cierre).

> **Caja sorpresa (apertura interactiva):** `mystery-box-01.glb` incluye cuatro nodos de bisagra reales (`Pivot_Flap_Front/Back/Left/Right`), pero su clip incrustado viene **vacío**, así que el visor anima las tapas directamente con la coreografía original del constructor (ángulo `2.18` rad; escalonado de apertura `145` ms y de cierre `105` ms; duración `1050` ms al abrir y `820` ms al cerrar; easing `easeOutCubic`). Front/back giran sobre el eje local X y left/right sobre el Z. Comparte con la Caja Premium el mismo botón **Abrir caja / Cerrar caja**, que actúa sobre la caja que esté seleccionada; el botón se oculta con el resto de modelos y no se solapan animaciones entre cajas.

> **No lo sé:** este modelo se carga de forma independiente con su botón del selector y también forma parte de la secuencia de sorpresas de la Caja Premium.

## Cómo ejecutar

Los navegadores bloquean la carga de modelos locales con `file://` por seguridad, así que hay que servir el proyecto por HTTP. Desde la carpeta del proyecto:

```bash
cd Mundo3D-Viewer
python3 -m http.server 8080
```

Después, abre en el navegador:

```text
http://localhost:8080/visor-3d.html
```

Notas:

- El comando debe ejecutarse **dentro del directorio del proyecto**, donde está `visor-3d.html`.
- Se necesita **conexión a Internet**: el HTML carga Three.js desde el CDN de jsDelivr mediante un *import map* (ver `visor-3d.html`).
- Las dependencias de Three.js que usa el visor son `OrbitControls`, `GLTFLoader` y `RoomEnvironment`.

## Tecnologías

- **HTML5** y **CSS3**.
- **JavaScript** (módulos ES).
- **Three.js r160.1**, cargado desde el CDN de jsDelivr.
  - `OrbitControls` (control de cámara).
  - `GLTFLoader` (carga de modelos `.glb`).
  - `RoomEnvironment` + `PMREMGenerator` (mapa de entorno).

## Añadir nuevos modelos

1. Copia el archivo `.glb` al directorio del proyecto.
2. Añade un botón dentro del contenedor `#models` en `visor-3d.html`, siguiendo el mismo patrón que los existentes:

```html
<button class="modelbtn" data-model="MiModelo.glb" data-label="Mi modelo">
  Mi modelo<small>MiModelo.glb · 1 MB</small>
</button>
```

El código JavaScript detecta automáticamente los botones con la clase `modelbtn` y carga el archivo indicado en `data-model`, mostrando como nombre el valor de `data-label`.

## Repositorio

- GitHub: <https://github.com/CIOB-Mojito/Mundo3D-Viewer>

## Limitaciones conocidas

- El **rendimiento depende del dispositivo** y de la complejidad de cada modelo (número de triángulos y tamaño de texturas).
- Los modelos que no estén incluidos en el repositorio deben **añadirse por separado** para poder cargarlos.
- Se requiere **conexión a Internet** para descargar Three.js desde el CDN.
- La cámara es **orbital**; no hay un modo de cámara libre o de vuelo.
