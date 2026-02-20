Me Activity Indicator
=========================

**Me Activity Indicator** es un componente de UI para aplicaciones web, inspirado en la "Dynamic Island" de iOS. Un sistema autonomo y personalizable que transforma las notificaciones tradicionales en una experiencia fluida, reactiva y elegante con animaciones basadas en spring physics.

- Zero dependencias: un unico fichero JavaScript, sin CSS externo
- Temas: dark, light o auto (segun preferencia del sistema)
- Cola con prioridades y agrupacion inteligente
- Accesibilidad: ARIA live regions, navegacion por teclado
- Animaciones spring physics para transiciones elasticas

---

Caracteristicas Principales
-----------------------------

### Smart UI

*   **Sistema de Prioridades:** Los mensajes pueden marcarse como `low`, `normal` o `high`. Las notificaciones criticas (`high` o de tipo `error`) se posicionan automaticamente al frente de la cola de visualizacion.

*   **Agrupacion Inteligente:** Detecta eventos con el mismo `groupId` para colapsarlos automaticamente, evitando el ruido visual cuando ocurren multiples acciones similares.

*   **Calculo de Promedio Dinamico:** Si los elementos de un grupo tienen la propiedad `progress`, la isla calcula y muestra el **promedio en tiempo real** de todas las tareas activas del grupo.

### Animaciones y Visual

*   **Micro-interacciones Reactivas:**
    *   **Efecto Shake:** Una vibracion lateral automatica para estados de `error`.
    *   **Efecto Pulse:** Un ligero escalado suave para confirmar estados de `success`.

*   **Capas de Apilamiento (Stack):** Capas con perspectiva 3D detras de la isla que indican visualmente cuantas actividades hay pendientes en cola (hasta 5 capas).

*   **Spring Physics:** Motor de animacion basado en simulacion de muelles que genera transiciones elasticas suaves al expandir y colapsar la isla.

### Arquitectura Plug & Play

*   **Componente Autonomo:** El codigo JavaScript inyecta dinamicamente sus propios estilos CSS al inicializarse. No requiere dependencias externas, archivos CSS separados ni configuracion de compilacion.

---

Demo
----

El fichero `index.html` incluido contiene una demo interactiva con todos los tipos de notificacion, cambio de posicion y tema en tiempo real.

---

Instalacion y Inicio Rapido
------------------------------

Copia `meActivityIndicator.js` en tu proyecto e instancialo:

```html
<script src="meActivityIndicator.js"></script>
<script>
  const indicator = new meActivityIndicator({
    position: 'top-center', // Ubicacion de la isla
    theme: 'system'         // 'light', 'dark' o 'system'
  });
</script>
```

---

Referencia de la API
---------------------------------

### 1. Constructor `new meActivityIndicator(options)`

| Propiedad | Tipo | Por defecto | Descripcion |
|-----------|------|-------------|-------------|
| `position` | `string` | `'top-center'` | `'top-left'`, `'top-center'`, `'top-right'`, `'center'`, `'bottom-left'`, `'bottom-center'`, `'bottom-right'` |
| `theme` | `string` | `'dark'` | Tema visual: `'light'`, `'dark'` o `'system'` (auto segun SO) |
| `stackStyle` | `string` | `'3d'` | Estilo de las capas apiladas: `'3d'` (perspectiva clasica), `'fan'` (abanico) o `'counter'` (badge numerico) |

### 2. Metodo `add(config)`

Anade una actividad a la cola. Devuelve una **Promesa** que se resuelve con `{ id, status: 'closed' }` cuando la notificacion se cierra.

| Propiedad | Tipo | Descripcion |
|-----------|------|-------------|
| `id` | `string` | ID unico opcional. Se genera automaticamente si se omite. |
| `type` | `string` | Tipo visual: `'success'`, `'error'`, `'info'`, `'warning'`, `'loading'`, `'thinking'`, `'generic'`. |
| `title` | `string` | Titulo principal. |
| `subtitle` | `string` | Texto descriptivo secundario. |
| `icon` | `string` | HTML de un icono personalizado. Sobrescribe el icono del tipo. |
| `avatarUrl` | `string` | URL de una imagen para mostrar un avatar circular en lugar del icono. |
| `duration` | `number` | Tiempo en ms para el auto-cierre. Si se omite, permanece abierta. |
| `priority` | `string` | Prioridad en la cola: `'low'`, `'normal'` (defecto), `'high'`. |
| `progress` | `number` | Valor de `0` a `1`. Activa automaticamente la barra de progreso. |
| `groupId` | `string` | ID de grupo para agrupar notificaciones del mismo proceso. |
| `groupTitle` | `string` | Plantilla de titulo para el grupo. Usa `{n}` para el conteo (ej: `'Subiendo {n} archivos'`). |
| `actions` | `array` | Botones de accion: `[{ label: 'Texto', type: 'primary', onClick: (ctx) => {} }]`. El callback recibe `{ activityId }`. |
| `closeOnClick` | `boolean` | Si es `true`, la isla se cierra al hacer clic sobre ella. |
| `isBlocking` | `boolean` | Activa un overlay que impide interactuar con el fondo. |
| `waitToDisplay` | `boolean` | Si es `true`, el temporizador de `duration` no arranca hasta que la actividad se muestre. |
| `enableAnimations` | `boolean` | Permite desactivar los efectos Shake/Pulse. Por defecto `true`. |
| `soundUrl` | `string` | URL de un archivo de audio para reproducir al activarse. |

### 3. Metodos de Control

| Metodo | Descripcion |
|--------|-------------|
| `update(id, patch)` | Modifica una actividad activa. Util para actualizar progreso, subtitulo, etc. |
| `remove(id)` | Cierra y elimina una actividad inmediatamente. |
| `setTheme(theme)` | Cambia el tema: `'light'`, `'dark'` o `'system'`. |
| `setPosition(pos)` | Mueve la isla a otra posicion de la pantalla. |
| `setStackStyle(style)` | Cambia el estilo de apilamiento: `'3d'`, `'fan'` o `'counter'`. |

### 4. Propiedad `icons`

La instancia expone un mapa de iconos SVG integrados a traves de `indicator.icons`. Esto permite reutilizar los iconos predefinidos del componente en cualquier actividad mediante la propiedad `icon`.

Iconos disponibles: `success`, `error`, `info`, `warning`, `upload`, `download`, `loading`, `thinking`, `speaking`, `listening`.

```javascript
indicator.add({
  type: 'generic',
  icon: indicator.icons.upload,
  title: 'Subiendo archivo',
  subtitle: 'documento.pdf'
});
```

---

Ejemplos de Uso
---------------------------

### A. Alerta Critica con Acciones y Animacion

```javascript
indicator.add({
  type: 'error',
  priority: 'high',
  title: 'Error de Sincronizacion',
  subtitle: 'No se han podido guardar los cambios',
  enableAnimations: true, // Ejecutara el efecto "Shake"
  actions: [
    {
      label: 'Reintentar',
      type: 'primary',
      onClick: () => ejecutarSincronizacion()
    },
    {
      label: 'Cerrar',
      onClick: (ctx) => indicator.remove(ctx.activityId)
    }
  ]
});
```

### B. Gestion de Cargas Masivas (Promedio de Progreso)

```javascript
// Si lanzas estas tres llamadas, la isla mostrara el promedio (50%)
var uploadData = { groupId: 'up-1', groupTitle: 'Subiendo {n} fotos...', type: 'loading' };

indicator.add({ ...uploadData, progress: 0.2 });
indicator.add({ ...uploadData, progress: 0.5 });
indicator.add({ ...uploadData, progress: 0.8 });
```

### C. Notificacion con Avatar

```javascript
indicator.add({
  type: 'generic',
  title: 'Elena Garcia',
  subtitle: 'Te ha enviado una imagen',
  avatarUrl: 'https://i.pravatar.cc/100?u=elena',
  duration: 5000,
  closeOnClick: true
});
```

### D. Progreso con Actualizacion Manual

```javascript
var id = 'upload-1';
indicator.add({ id: id, type: 'loading', title: 'Subiendo Video', subtitle: '0%', progress: 0 });

// Actualizar progreso desde un callback
indicator.update(id, { progress: 0.5, subtitle: '50%' });

// Completar
indicator.update(id, { progress: 1, subtitle: 'Completado', type: 'success', duration: 2000 });
```

### E. Esperar al cierre con Promesa

```javascript
var result = await indicator.add({
  type: 'success',
  title: 'Tarea Completada',
  duration: 3000
});
console.log(result); // { id: '...', status: 'closed' }
```

---

Personalizacion Visual (CSS Variables)
-----------------------------------------

Puedes ajustar los colores y el estilo inyectando variables en tu CSS global:

```css
#me-ai-island-root {
  /* Colores de estados */
  --me-ai-color-success: #00ff88;
  --me-ai-color-error: #ff3333;
  --me-ai-color-info: #00d4ff;
  --me-ai-color-warning: #ffaa00;

  /* Color de acento generico */
  --me-ai-color-generic-dark: #ffffff;
  --me-ai-color-generic-light: #1d1d1f;

  /* Apariencia fisica */
  --me-ai-island-radius: 20px;
  --me-ai-island-blur: 24px;
}
```

---

Accesibilidad (A11y)
----------------------

Cumple con los estandares para lectores de pantalla y navegacion asistida:

*   **ARIA Live:** Gestion dinamica de regiones (`assertive` para errores y alta prioridad, `polite` para el resto).
*   **Control por Teclado:** Soporte nativo para `Tab`, `Enter` y `Espacio`.
*   **Semantica:** Implementacion automatica de `role="alert"`, `role="status"` y `role="progressbar"`.

---

Compatibilidad
----------------

El componente utiliza ECMAScript 2020+ (clases, `const`/`let`, arrow functions, template literals, `Map`, optional chaining `?.`, spread `...`). Compatible con:

*   Chrome 80+
*   Firefox 72+
*   Safari 13.1+
*   Edge 80+

---

Autor
------

**Marcos Esperon** - [github.com/marcosesperon](https://github.com/marcosesperon)

Si este proyecto te resulta util, puedes apoyar su desarrollo:

[![Buy Me a Coffee](https://img.shields.io/badge/Buy%20Me%20a%20Coffee-ffdd00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black)](https://buymeacoffee.com/marcosesperon)

---

Licencia
-----------

Este proyecto esta bajo la licencia **MIT**. Consulta el fichero [LICENSE.md](LICENSE.md) para mas detalles.
