## ¿Por qué el DOM-Based XSS es invisible para los WAF e IPS?

A diferencia del XSS Reflejado o Almacenado —donde el código malicioso viaja en la petición HTTP hasta el servidor y vuelve dentro de la respuesta HTML—, en un ataque **DOM-Based XSS** todo ocurre **exclusivamente dentro del navegador de la víctima**.

La razón principal por la que los firewalls de aplicaciones web (**WAF**) y sistemas de prevención de intrusos (**IPS**) no lo detectan es **cómo los navegadores manejan el símbolo `#` (fragmento) en una URL**:

1. **El fragmento `#` nunca se envía al servidor:** Según el estándar de la web (HTTP), todo lo que aparece después del numeral `#` en una dirección (por ejemplo, `https://sitio.com/perfil#<img src=x onerror=alert(1)>`) se procesa únicamente de forma local en el navegador.
2. **El WAF/IPS solo ve una petición limpia:** Cuando el usuario abre ese enlace, el navegador le pide al servidor únicamente `GET /perfil`. Como el payload malicioso quedó del lado del `#`, nunca viaja por la red hacia el servidor. Para el WAF o el IPS, la petición parece completamente normal e inofensiva, por lo que nunca dispara ninguna alerta ni bloqueo.

---

## ¿Qué sucede cuando JavaScript procesa `location.hash` de forma insegura?

Una vez que la página carga en el navegador, el ataque se completa en dos pasos dentro del código JavaScript del sitio:

1. **La fuente (*Source*):** El script de la página lee el valor que está después del `#` usando propiedades del navegador como `window.location.hash` (o `location.href`) para mostrar un mensaje dinámico, seleccionar una pestaña o saludar al usuario.
2. **El sumidero peligroso (*Sink*):** Si el desarrollador toma ese texto sin limpiarlo y lo inserta directamente en la página usando funciones inseguras como `element.innerHTML = ...` o `document.write(...)`, el navegador interpreta el texto del `#` como **código HTML real** y ejecuta el JavaScript malicioso al instante.



## ¿Cómo se soluciona?
* **Usar propiedades de texto seguro:** Reemplazar `innerHTML` por **`textContent`** o **`innerText`** al mostrar datos leídos de la URL. De esa forma, el navegador trata el contenido de `location.hash` como simple texto plano y nunca como etiquetas HTML ejecutables.
* **Sanitizar si hace falta HTML:** Si realmente se necesita renderizar etiquetas HTML dinámicas, limpiar primero la entrada con una librería segura como **DOMPurify** antes de insertarla en el DOM.
