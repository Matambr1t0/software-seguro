Para eliminar el riesgo de **Cross-Site Scripting (XSS)** de forma definitiva, no alcanza con una sola medida; se aplica una estrategia de **defensa en profundidad** combinando dos capas: una en el código de la aplicación y otra en la configuración del servidor web.

---

## Capa 1: Output Encoding (Codificación de Salida) a nivel de código

* **¿Qué es?** Es la defensa principal contra XSS. Consiste en transformar cualquier dato ingresado por el usuario en un formato seguro **justo antes de mostrarlo en la página HTML**, convirtiendo los caracteres especiales en entidades inocuas para el navegador.
* **¿Cómo funciona?**
  * El carácter `<` se convierte en `&lt;`
  * El carácter `>` se convierte en `&gt;`
  * Las comillas `"` y `'` se convierten en `&quot;` y `&#x27;`
* **¿Por qué frena el ataque?** Si un usuario escribe `<script>alert(1)</script>`, el navegador recibe `&lt;script&gt;alert(1)&lt;/script&gt;`. Gracias a esto, lo dibuja en la pantalla literalmente como texto plano y **nunca lo interpreta como código ejecutable**.
* **Aplicación práctica:** Utilizar frameworks modernos (como React, Angular o Vue) que codifican las variables automáticamente por defecto, o usar funciones seguras como `textContent` en lugar de `innerHTML` al manipular el DOM.

---

## Capa 2: Content Security Policy (CSP) a nivel de cabeceras HTTP

* **¿Qué es?** Es una segunda barrera de seguridad (*red de seguridad*) que el servidor envía al navegador mediante la cabecera HTTP **`Content-Security-Policy`**. Sirve para indicarle al navegador **de qué lugares confiables tiene permiso para cargar y ejecutar código JavaScript**, bloqueando todo lo demás.
* **¿Cómo funciona?**
  * **Bloqueo de scripts en línea (*Inline Scripts*):** Una política CSP estricta prohíbe ejecutar código JavaScript metido directamente dentro de etiquetas HTML (como `<script>...</script>` o atributos `onerror="..."`, `onload="..."`). Aunque un atacante lograra colar una etiqueta maliciosa por un error del programador, el navegador se niega a ejecutarla.
  * **Lista blanca de orígenes (`script-src`):** Solo permite cargar archivos `.js` desde dominios autorizados explícitamente (por ejemplo, el propio servidor `'self'`) o scripts que tengan una firma criptográfica temporal única (*nonce*).
* **Ejemplo de cabecera CSP segura:**
  ```http
  Content-Security-Policy: default-src 'self'; script-src 'self'; object-src 'none';
  ```
* **¿Por qué complementa al Output Encoding?** Si en algún rincón de la aplicación un desarrollador olvidó codificar una salida y dejó un hueco XSS, la política CSP actúa como freno de emergencia impidiendo que el navegador ejecute el script inyectado o envíe datos robados hacia el servidor del atacante.
