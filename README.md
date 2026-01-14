# Astro WebGL Transition Canvas

Un componente de Astro "drop-in" para crear transiciones de página líquidas y fluidas utilizando WebGL (Three.js) y el Router de Astro.

Este componente detecta las imágenes en la página saliente y entrante, y aplica un efecto de distorsión basado en un mapa de desplazamiento durante la navegación.

## ✨ Características

*   **Integración Transparente:** Diseñado para funcionar nativamente con `<ClientRouter />` (View Transitions) de Astro.
*   **Alto Rendimiento:**
    *   **Smart Loop:** El bucle de renderizado se detiene automáticamente cuando no hay animación activa.
    *   **Low-End Detection:** Desactiva automáticamente WebGL en dispositivos con poca RAM (<4GB), pocos núcleos (<=2) o en modo "Ahorro de datos".
*   **Accesibilidad (A11y):** Respeta la preferencia del sistema `prefers-reduced-motion`, desactivando los efectos si el usuario lo solicita.
*   **Responsive:** Sincronización perfecta entre las imágenes del DOM y el Canvas WebGL al hacer scroll o redimensionar la ventana.
*   **Developer Experience:** Incluye un modo `debug` con un panel de control (lil-gui) para ajustar la velocidad, intensidad y curvas en tiempo real.
*   **Degradación Elegante:** Si WebGL falla o se desactiva, el sitio continúa funcionando como una web estándar sin romper el diseño.

## 🚀 Instalación

1.  **Instala el paquete** y sus dependencias en tu proyecto de Astro.

```bash
npm install webgl-images-view-transitions three lil-gui
```

Copia la carpeta del componente en tu proyecto (ej. `src/components/WebGLViewTransitions/`).

## 🛠 Uso

### 1. Configuración Global

Importa y coloca el componente `WebGLViewTransitions` en tu Layout principal (o en todas las páginas donde desees el efecto). Debe estar presente tanto en la página de origen como en la de destino.

```astro
---
// src/layouts/Layout.astro
import { ClientRouter } from 'astro:transitions';
import WebGLViewTransitions from '../components/WebGLViewTransitions.astro';
import displacementImg from '../assets/displacement.jpg'; // Tu textura de desplazamiento
---

<html lang="es">
  <head>
    <ClientRouter />
  </head>
  <body>
    <slot />
    
    <WebGLViewTransitions 
      displacementImage={displacementImg.src}
      speed={0.02}
      intensity={0.4}
      zoom={0.2}
    />
  </body>
</html>
```

### 2. Marcado de Imágenes

Para que el componente detecte qué imágenes debe animar, añade el atributo `data-sampler="texture"` a tus etiquetas `<img>`.

```html
<img src="/mi-foto.jpg" data-sampler="texture" alt="Paisaje" />
```

## ⚙️ API (Props)

| Propiedad | Tipo | Default | Descripción |
| :--- | :--- | :--- | :--- |
| `displacementImage` | `string` | **Requerido** | URL de la imagen (preferiblemente B/N) usada para distorsionar la transición. |
| `speed` | `number` | `0.015` | Velocidad de la animación (0.001 a 0.1). |
| `intensity` | `number` | `0.25` | Fuerza de la distorsión líquida. |
| `zoom` | `number` | `0.1` | Efecto de profundidad/acercamiento durante la transición. |
| `dispScale` | `number` | `1.0` | Escala de la textura de desplazamiento (frecuencia de las ondas). |
| `easing` | `string` | `'easeOut'` | Curva de animación: `'linear'`, `'easeIn'`, `'easeOut'`, `'easeInOut'`. |
| `debug` | `boolean` | `import.meta.env.DEV` | Muestra un panel GUI para ajustar valores en vivo. |

## 📝 Notas Técnicas

*   El componente inyecta un `canvas` con `position: fixed` y `z-index: 1` que cubre toda la pantalla.
*   Las imágenes originales se ocultan (`opacity: 0`) solo cuando el script confirma que WebGL está listo, evitando parpadeos (FOUC).