
## Escenarios donde sqlmap falla y la explotación manual tiene éxito

### 1. Inyección SQL de Segundo Orden (*Second-Order SQLi*)
* **¿Qué pasa?** El payload malicioso no se ejecuta en el momento en que el usuario lo envía, sino más tarde en otro lugar de la aplicación. Por ejemplo, un usuario se registra poniendo como nombre de usuario `admin' --` (la aplicación lo guarda de forma segura en la base de datos usando consultas parametrizadas), pero luego, al entrar a su perfil o cambiar su contraseña, otra consulta interna toma ese nombre directamente de la base de datos y lo concatena sin filtrar.
* **¿Por qué falla sqlmap?** Porque `sqlmap` envía el payload en una petición HTTP y analiza inmediatamente la respuesta de *esa misma* petición. Como el formulario de registro responde "Usuario creado con éxito" sin errores ni demoras, la herramienta asume que el parámetro es seguro y pasa de largo.
* **¿Cómo lo logra un atacante manual?** Observa el flujo completo de la aplicación en Burp Suite: guarda el carácter de prueba (`'`) al registrarse y luego navega por las distintas pantallas del sistema hasta detectar dónde se dispara el error de sintaxis o el comportamiento extraño al leer ese dato guardado.

---

### 2. Bloqueo por WAF (Web Application Firewall) con reglas personalizadas
* **¿Qué pasa?** Muchas aplicaciones están protegidas por un WAF (como Cloudflare, AWS WAF o ModSecurity) que bloquea automáticamente peticiones que contengan palabras clave típicas de SQL (`UNION SELECT`, `OR 1=1`, `SLEEP`, `--`) o que detecta ráfagas rápidas de pruebas automatizadas, bloqueando la IP del atacante.
* **¿Por qué falla sqlmap?** Por defecto, `sqlmap` lanza cientos de peticiones por minuto usando payloads muy ruidosos y conocidos. El WAF detecta el patrón al instante y empieza a devolver errores `403 Forbidden` o captchas, haciendo que `sqlmap` crea que la página no es vulnerable o que se cayó.
* **¿Cómo lo logra un atacante manual?** Envía una sola petición a la vez desde el *Repeater* de Burp Suite, analiza exactamente qué palabra o carácter está bloqueando el firewall y arma un payload artesanal para evadirlo (por ejemplo, usando comentarios en línea `/*!50000UNION*/`, codificación doble URL o funciones alternativas de SQL que el filtro no contempla).

---

### 3. Parámetros ocultos dentro de estructuras complejas o codificadas (JSON, XML, Base64)
* **¿Qué pasa?** El dato vulnerable no viaja como un parámetro simple en la URL (`?id=5`), sino escondido dentro de un objeto JSON anidado, un archivo XML o un string codificado en Base64 dentro de una cookie o cabecera personalizada.
* **¿Por qué falla sqlmap?** Si se la ejecuta de forma automática sin indicarle exactamente dónde inyectar, la herramienta suele probar únicamente los parámetros estándar de la URL o del formulario y no sabe cómo descomponer, modificar y volver a empaquetar correctamente una estructura personalizada antes de enviarla.
* **¿Cómo lo logra un atacante manual?** Decodifica el parámetro manualmente en Burp Suite (*Decoder* o *Inspector*), identifica qué campo interno viaja hacia la base de datos, inserta la inyección SQL exactamente dentro de ese campo y vuelve a codificar la estructura antes de reenviar la petición.
