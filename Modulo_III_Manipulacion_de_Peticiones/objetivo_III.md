## Diferencia fundamental: ¿En quién confía cada ataque?

Aunque tanto **XSS** (*Cross-Site Scripting*) como **CSRF** (*Cross-Site Request Forgery*) aprovechan el navegador de la víctima para atacar su cuenta, funcionan exactamente al revés respecto a la confianza entre el usuario y el sitio web:

* **XSS explota la confianza que el usuario tiene en un sitio web:** La víctima entra a una página legítima y confía en todo lo que esa página ejecuta en su navegador. El atacante logra inyectar código JavaScript malicioso *adentro* de esa página confiable para que se ejecute como si fuera parte del sitio oficial.
* **CSRF explota la confianza que un sitio web tiene en el navegador del usuario:** El atacante arma una trampa desde *afuera* (en otra página distinta) y engaña al navegador de la víctima para que envíe una petición oculta hacia el sitio legítimo. Como el navegador adjunta automáticamente las cookies de sesión, el sitio web confía en que la petición la hizo el propio usuario.

---

## Comparación directa

| Característica | XSS (Cross-Site Scripting) | CSRF (Cross-Site Request Forgery) |
| :--- | :--- | :--- |
| **¿Dónde se ejecuta el ataque?** | **Dentro** del sitio web vulnerable (en el navegador de la víctima). | **Desde afuera** (desde una página maliciosa externa hacia el sitio vulnerable). |
| **¿Permite ejecutar código JavaScript en el sitio?** | **Sí**, el atacante ejecuta cualquier script JavaScript arbitrario en la sesión de la víctima. | **No**, el atacante no puede inyectar ni ejecutar código dentro del sitio legítimo; solo fuerza peticiones HTTP. |
| **¿Permite leer información privada?** | **Sí** (puede leer datos de la pantalla, robar tokens del `localStorage`, leer correos o ver el saldo). | **No** (es un ataque "ciego": envía la orden, pero la política *Same-Origin* del navegador impide que el sitio externo lea la respuesta). |
| **¿Qué necesita para funcionar?** | Que la página web muestre datos sin codificar/escapar correctamente. | Que la víctima tenga una sesión activa con cookies y que el servidor no valide el origen real de la petición. |
| **Defensa principal** | **Output Encoding** (escapar caracteres HTML) + **CSP** (*Content Security Policy*). | Cookies con **`SameSite=Strict` / `Lax`** + **Tokens Anti-CSRF** únicos por formulario. |
