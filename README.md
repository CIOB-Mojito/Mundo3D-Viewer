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

> **Importante:** los archivos `.glb` deben estar **en el mismo directorio que `visor-3d.html`** para que se carguen. Los nombres distinguen mayúsculas/minúsculas y acentos.

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
