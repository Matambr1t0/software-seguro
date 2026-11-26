## ¿Por qué ocurre este fallo de seguridad?
Un ataque **CSRF** (*Cross-Site Request Forgery* o falsificación de petición en sitios cruzados) ocurre por una característica propia de cómo funcionan los navegadores web: **los navegadores adjuntan automáticamente las cookies de sesión** cada vez que se envía una petición hacia un dominio en el que el usuario ya inició sesión, sin importar desde qué página externa se haya originado esa petición.

Si el servidor confía únicamente en esa cookie de sesión para autorizar una acción (como cambiar una contraseña o hacer una transferencia) y no verifica si la petición salió realmente de su propio sitio web, un atacante puede engañar al navegador de la víctima para que ejecute esa acción sin que el usuario se dé cuenta.

---

## Flujo exacto de un ataque CSRF exitoso

1. **Inicio de sesión legítimo:** La víctima inicia sesión en su banco (`https://mibanco.com`) y el navegador guarda la cookie de sesión (`session_id=abc123`). La víctima deja esa pestaña abierta o simplemente no cierra sesión.
2. **Trampa en un sitio malicioso:** Sin cerrar su sesión del banco, la víctima abre otra pestaña y entra a una página controlada por el atacante (`https://sitio-malicioso.com`), por ejemplo, desde un enlace en un correo o mensaje.
3. **Envío oculto de la petición falsificada:** La página del atacante contiene un formulario oculto en HTML o un script que envía automáticamente una petición `POST` hacia `https://mibanco.com/transferir` con los datos del atacante (`destino=atacante&monto=50000`).
4. **El navegador adjunta la cookie automáticamente:** Al ver que el destino de la petición es `mibanco.com`, el navegador de la víctima **le pega automáticamente la cookie `session_id=abc123`** a esa solicitud, como haría con cualquier petición normal.
5. **Ejecución por parte del servidor:** El servidor de `mibanco.com` recibe la petición, comprueba que la cookie `session_id=abc123` corresponde a una sesión activa y válida, y **ejecuta la transferencia** pensando que fue el propio usuario quien hizo clic en su cuenta.

---

## ¿Cómo mitiga este ataque el atributo `SameSite` de las cookies?

El atributo **`SameSite`** le indica al navegador cuándo tiene permiso para enviar una cookie junto con peticiones que nacen desde sitios web externos (*cross-site*):

* **`SameSite=Strict` (Máxima protección):**
  * **¿Qué hace?** El navegador **nunca** envía la cookie si la petición se origina desde un dominio distinto al del sitio web, sin excepciones.
  * **Efecto contra CSRF:** Bloquea el 100% de los ataques CSRF. Incluso si la víctima hace clic en un enlace externo hacia `mibanco.com`, el navegador no envía la cookie de sesión en esa primera navegación.
* **`SameSite=Lax` (Equilibrio entre seguridad y usabilidad — valor por defecto en navegadores modernos):**
  * **¿Qué hace?** Bloquea el envío de la cookie en peticiones ocultas en segundo plano (como formularios `POST` automáticos, peticiones `fetch`, etiquetas `<img>` o `<iframe>` desde sitios externos), pero **sí permite** enviarla cuando el usuario hace una navegación normal de nivel superior haciendo clic en un enlace (`GET` estándar).
  * **Efecto contra CSRF:** Frena todos los ataques CSRF clásicos basados en formularios ocultos `POST` o peticiones en segundo plano, manteniendo una buena experiencia de usuario al navegar desde enlaces externos. *(Para que `Lax` sea efectivo, el backend nunca debe permitir que una petición `GET` realice cambios sensibles como transferir dinero o borrar datos).*
