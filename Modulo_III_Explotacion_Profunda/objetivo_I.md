## ¿Por qué es inútil eliminar únicamente las etiquetas `<script>`?

Intentar frenar un ataque de **Cross-Site Scripting (XSS)** borrando solo la palabra `<script>` y `</script>` es una defensa fallida por tres razones principales:

1. **HTML tiene decenas de formas de ejecutar JavaScript sin usar `<script>`:** Los navegadores modernos permiten ejecutar código JavaScript mediante atributos de eventos (como `onerror`, `onload`, `onmouseover`), etiquetas multimedia (`<img>`, `<svg>`, `<iframe>`, `<video>`) o enlaces con el protocolo `javascript:`. Bloquear una sola etiqueta deja abiertas todas las demás vías.
2. **Filtros no recursivos (Bypass por anidamiento):** Si el filtro simplemente recorre el texto una vez y borra la cadena `<script>`, un atacante puede escribir `<scr<script>ipt>alert(1)</scr</script>ipt>`. Al borrarse la etiqueta del medio, las letras de los costados se juntan y vuelven a formar `<script>alert(1)</script>`.
3. **Diferencias de mayúsculas o codificación:** Muchos filtros básicos buscan exactamente `<script>` en minúscula y fallan si el atacante escribe `<ScRiPt>` o utiliza entidades HTML/codificaciones que el navegador luego interpreta.

---

## Payloads en HTML/JS que ejecutan código sin usar `<script>`

### 1. Etiqueta de imagen (`<img>`) con evento `onerror`
```html
<img src="imagen_inexistente.jpg" onerror="alert('XSS ejecutado')">
```
* **¿Cómo funciona?** El navegador intenta cargar una imagen desde la ruta `imagen_inexistente.jpg`. Como el archivo no existe, se dispara automáticamente el evento `onerror`, ejecutando el código JavaScript que está adentro sin necesidad de que el usuario haga clic ni se use la etiqueta `<script>`.

---

### 2. Etiqueta vectorial (`<svg>`) con evento `onload`
```html
<svg onload="alert(document.domain)">
```
* **¿Cómo funciona?** Apenas el navegador renderiza la etiqueta `<svg>` en la pantalla, se activa de forma inmediata el evento `onload`, ejecutando el JavaScript sin requerir ninguna interacción de la víctima.

---

### 3. Enlace con protocolo `javascript:` (`<a>`)
```html
<a href="javascript:alert('XSS con enlace')">Hacé clic acá para ver más</a>
```
* **¿Cómo funciona?** Si la aplicación permite publicar enlaces, el atacante reemplaza una dirección `https://` normal por el esquema `javascript:`. Cuando otro usuario hace clic en el enlace, el navegador ejecuta el código directamente en el contexto de la pági
