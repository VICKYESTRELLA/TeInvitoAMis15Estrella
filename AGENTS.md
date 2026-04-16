# AGENTS.md - TeInvitoAMis15Estrella

## Descripción General del Proyecto
**TeInvitoAMis15Estrella** es una invitación digital completamente responsiva basada en web para una fiesta de cumpleaños de 15 años. El sitio presenta animaciones elegantes, una interfaz de desplazamiento horizontal y múltiples secciones interactivas (detalles del evento, temporizador de cuenta regresiva, galería de fotos y formularios de participación).

## Stack Tecnológico
- **Frontend**: HTML5 Vanilla, CSS3, jQuery 3.6.0
- **Estilos**: CSS Grid/Flexbox con patrones de diseño responsivos
- **Animaciones**: Animaciones CSS + transformaciones de desplazamiento basadas en jQuery
- **Librerías Externas**: 
  - jQuery UI y jQuery Mousewheel para interacciones de desplazamiento
  - FancyBox para lightbox de galería de imágenes
  - Modernizr para detección de características
- **Análisis**: Google Analytics (ID GA: G-W69QWCYTBZ)
- **Idioma**: Español (es)

## Arquitectura y Componentes Clave

### 1. **Secciones Visuales** (`index.html` líneas 45-359)
La página se divide en secciones de desplazamiento horizontal distintas:
- **Primera Animación** (`.envelope`): Animación de sobre abriéndose con confeti al cargar la página
- **Contenido de la Carta**: Diseño basado en SVG con acentos dorados animados (nombre "Estrella" + texto "Mis 15")
- **Paneles de Información**: 5 secciones horizontales navegables mediante rueda del ratón, toque o botones de navegación
  - Sección 1: Detalles del evento (fecha/hora/ubicación con enlace a Google Maps)
  - Sección 2: Confirmación de RSVP + temporizador de cuenta regresiva hasta el evento
  - Sección 3: Galería de fotos (4 imágenes con lightbox FancyBox)
  - Sección 4: Promoción de hashtag de Instagram (#15estrella)
  - Sección 5: Formulario de sugerencias de canciones

### 2. **Comportamiento JavaScript** (`assets/js/scripts.js`)

#### Detección de Navegador y Tiempo de Animación (líneas 1-12)
- Detecta específicamente navegador Chrome para controlar el tiempo de animación mediante la variable CSS `--time-chrome`
- Los navegadores que no son Chrome omiten la animación del sobre inicial (eliminado del DOM)

#### Temporizador de Cuenta Regresiva (líneas 14-44)
- **Crítico**: Fecha codificada `04/03/2021 21:00:00` (obsoleta - necesita actualización para evento real)
- Actualiza el elemento `#reloj` cada 1 segundo con formato: `"X días Yhs Zm Ws"`
- Cuando la cuenta regresiva llega a cero, muestra "¡LLEGO EL GRAN DÍA!"

#### Variables Responsivas (líneas 46-56)
- Establece propiedades CSS personalizadas `--vh` y `--vw` para el tamaño relativo al viewport
- Se recalcula en el cambio de tamaño de la ventana

#### Efecto Confeti (líneas 58-89)
- Genera partículas de confeti aleatorias en el contenedor `.confetti`
- Utiliza una extensión jQuery personalizada `$.rnd()` para generar números aleatorios
- Conteo de partículas: ~(ancho / 50) * 8
- Las partículas varían en tamaño, posición y retraso de animación

#### Efecto de Transformación de Desplazamiento Dorado (líneas 91-128)
- Anima la opacidad del SVG en el desplazamiento vertical (primeros 300px)
- Transforma entre versiones en escala de grises y doradas de:
  - `#estrella_gold` (texto del nombre)
  - `#mis_quince_gold` (texto del título)
  - `#marco_gold` (borde del marco)
- Cálculo: `letter_num = (elementOffset - windowHeight) / (windowHeight * 0.38)`

#### Navegación de Desplazamiento Horizontal (líneas 131-218)
- **Interacción principal**: Desplazamiento de rueda del ratón/touchpad = navegación horizontal
- Soporte táctil: Detecta deslizamiento izquierda/derecha (umbral: 15px)
- Navegación con clic: Los enlaces en `<nav>` se sincronizan con las diapositivas
- Fórmula de desplazamiento: `$(window).width() * 0.85 * slideIndex`
- Debounce de 500ms entre cambios de diapositivas (previene disparos rápidos repetitivos)
- Indicador de diapositiva activa: Actualiza la clase `.active` en los enlaces de navegación

### 3. **Arquitectura de Estilos** (`assets/css/`)

#### Sistema de Diseño (`styles.css` líneas 1-9)
```css
:root {
  --white_envelope: #f3f4f5
  --gold_envelope: url(../img/gold_texture.png) #e3ca6f
  --gold: #e1b100  /* Color de acento principal */
  --gray: #a5a5a5
  --time-chrome: 9s  /* Tiempo de animación */
}
```

#### Estrategia de Escalado de Viewport
- El tamaño de fuente base cambia a través de consultas de medios:
  - 2100px+: 0.6vw → 0.7vw (para pantallas muy anchas)
  - 1100px+: 1vw (escritorio)
  - Inferior a 1100px: escalado se ajusta (mobile-first)
- La variable personalizada `--vh` maneja problemas de altura de viewport móvil (100vh ≠ altura real en móvil)

#### Patrones de Maquetación
- Imágenes de fondo fijo con opacidad animada (`.imgPortada`)
- Posicionamiento pegajoso para contenido de encabezado (`.sticky_effect`)
- Desplazamiento horizontal basado en transformación (acelerado por GPU `translateX`)
- Enmascaramiento SVG para bordes decorativos (`#mask_marco`)

## Flujo de Datos y Gestión de Estado

### Reloj de Cuenta Regresiva
1. Fecha del evento codificada en `scripts.js` línea 15
2. El intervalo del temporizador se actualiza cada 1000ms
3. Destino del DOM: elemento `#reloj` (línea 35)

### Seguimiento de Posición de Desplazamiento
1. `window.onscroll` se dispara en el desplazamiento vertical (línea 100)
2. Calcula transformaciones de opacidad basadas en el desplazamiento de `.letter_content`
3. Actualiza las propiedades de opacidad del elemento SVG directamente a través de métodos jQuery CSS

### Estado de Navegación
- Variable `currSlide` realiza seguimiento de la diapositiva horizontal actual (línea 184)
- Función `showSlide(n)` actualiza la transformación `.sections` y la clase `.active` del enlace
- Debounced para prevenir eventos de navegación concurrentes

## Flujos de Trabajo Críticos para Desarrolladores

### Actualización de Detalles del Evento
1. **Fecha/Hora del Evento**: Actualizar la cadena codificada en `scripts.js:15` (`fechaCuentaRegresiva`)
   - Formato: `MM/DD/YYYY HH:mm:ss`
   - Zona horaria: Basada en la hora local del navegador
2. **Ubicación**: Editar `index.html` línea 218 (href del enlace de Google Maps)
3. **Texto de Fecha**: Actualizar "Sabado 26 de Septiembre" en `index.html` línea 210

### Personalización para Diferentes Eventos
1. Reemplazar "Estrella" en el símbolo SVG `#nombre_estrella` (línea 174)
2. Actualizar hashtag en la Sección 4 (línea 312) y enlace de Instagram (línea 320)
3. Modificar galería de imágenes: Reemplazar rutas de imágenes en elementos de la galería de la Sección 3 (líneas 251-306)
4. Actualizar enlaces de Google Forms para RSVP (línea 231) y sugerencias de canciones (línea 336)

### Agregar/Eliminar Secciones
- Cada sección necesita:
  1. Bloque `<section id="N">` en `.sections` (líneas 205-341)
  2. `<li><a href="#N">N</a></li>` correspondiente en navegación (líneas 346-350)
  3. Actualizaciones manuales del conteo de diapositivas si se modifica la navegación

### Consideraciones de Rendimiento
- La generación de confeti se ejecuta al cargar la página (línea 89) - costosa con viewports grandes
- El cambio de tamaño de ventana dispara recálculo de diseño costoso (línea 171)
- Los eventos de desplazamiento disparan consultas de DOM cada píxel - considerar debouncing si el rendimiento se degrada
- Las transformaciones SVG prefieren `opacity` sobre cambios `fill` para aceleración GPU

## Puntos de Integración e Dependencias Externas

### Formularios y Enlaces Externos
- **Formulario RSVP**: Google Forms (línea 231) - alojado externamente
- **Sugerencias de Canciones**: Google Forms (línea 336) - alojado externamente  
- **Hashtag de Instagram**: URL directa de exploración de Instagram (línea 320)
- **Ubicación**: URL de inserción de Google Maps (línea 218)

### Carga de Fuentes y Recursos
- Google Fonts: Parisienne (cursiva) + Montserrat (sans-serif) cargadas desde `fonts.googleapis.com`
- Las fuentes son críticas para el logo y tipografía - verificar carga antes de animaciones

### Scripts de Terceros
- jQuery UI (cdn.cloudflare.com) - para animaciones avanzadas
- Modernizr (cdnjs.cloudflare.com) - detección de características (mayormente sin usar)
- FancyBox - lightbox de galería de imágenes

## Pruebas y Notas de Compatibilidad del Navegador

- **Chrome**: Ruta especial de tiempo de animación (lógica de detección línea 5)
- **Mobile Safari**: Asegurar que la propiedad personalizada `--vh` funcione (manejo de altura de viewport)
- **Eventos Táctiles**: Probado a través de manejadores `touchstart`/`touchend` (líneas 195-207)
- **IE/Heredado**: Modernizr incluido pero sin alternativas elegantes para CSS Grid/Transform

## Banderas de Mantenimiento Futuro

1. **Codificación de Fecha**: La fecha del temporizador de cuenta regresiva está permanentemente fija - requiere actualización manual para reutilización
2. **Puntos de Corte Responsivos**: Limitado a algunos tamaños de viewport - puede necesitar ajuste para nuevos dispositivos
3. **Máscaras SVG**: El soporte del navegador varía - probar si ocurren problemas de visualización entre navegadores
4. **Dependencia jQuery**: El proyecto se basa en jQuery 3.6 - considerar refactorización a JavaScript simple para compilaciones modernas
5. **Rendimiento de Confeti**: La generación de partículas se escala con el viewport - puede causar retraso en móvil




