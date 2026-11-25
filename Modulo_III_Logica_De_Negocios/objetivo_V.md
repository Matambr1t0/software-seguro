## ¿Por qué es una falsa sensación de seguridad?
Cifrar los datos con JavaScript en el navegador antes de enviarlos al servidor no evita que un atacante los lea o los modifique. Esto es solo **seguridad por oscuridad**: hace que el tráfico se vea ilegible a simple vista en un proxy como Burp Suite, pero no protege realmente la integridad ni la confidencialidad de los datos frente al propio usuario.

---

## ¿Dónde reside el fallo fundamental de diseño?

El error principal de arquitectura es **confiar en el entorno del cliente** y entregarle al usuario todo lo necesario para realizar el cifrado:

* **El código se ejecuta en la computadora del atacante:** Todo el código JavaScript (el algoritmo utilizado, la lógica de armado y las claves o secretos necesarios para cifrar) se descarga en el navegador del usuario. El atacante tiene control total sobre su propio navegador y puede ver, pausar o modificar ese código.
* **No reemplaza las validaciones del servidor:** Aunque los datos lleguen cifrados en un formato distinto, el servidor sigue recibiendo valores que vienen desde afuera sin comprobar si las reglas de negocio son válidas.

---

## Pasos tácticos de un atacante para evadir esta protección

Para leer los datos y enviar valores manipulados al servidor, un atacante no necesita romper el algoritmo matemático de cifrado; simplemente interviene el proceso **antes** de que los datos se cifren:

1. **Inspeccionar el código JavaScript del sitio:** Abre las herramientas de desarrollador del navegador (*DevTools > Sources / Debugger*) y busca la función encargada de cifrar el cuerpo de la petición `POST` (buscando palabras clave como `encrypt`, `CryptoJS`, `AES`, `JSON.stringify`, etc.).
2. **Colocar un punto de interrupción (*Breakpoint*):** Pone un *breakpoint* justo en la línea anterior a que la función de cifrado tome los datos en texto plano.
3. **Leer y modificar los valores en memoria:** Cuando envía el formulario, la ejecución del navegador se congela en ese punto exacto. Desde la consola de *DevTools*, el atacante ve los parámetros reales antes de encriptarse y cambia el valor de la variable en memoria (por ejemplo, cambia `monto = 100` por `monto = -500` o `monto = 999999`).
4. **Dejar que el propio sitio cifre el dato falso:** Reanuda la ejecución del script. El código JavaScript de la aplicación cifra automáticamente el valor ya manipulado usando la clave y el formato correctos, y envía la petición al backend como si fuera totalmente legítima.
5. **Automatizarlo (opcional):** Si quiere hacer muchas pruebas rápidas en Burp Suite, simplemente copia la función de cifrado y la clave desde el JavaScript del navegador para re-encriptar sus propios payloads antes de enviarlos.

---

## ¿Cómo debería diseñarse correctamente?

* **Usar HTTPS (TLS):** Para proteger los datos mientras viajan por la red frente a terceros, se utiliza el cifrado estándar de transporte (TLS), no cifrado casero en JavaScript.
* **Validar todo en el Backend:** El servidor debe descifrar los datos y aplicar controles estrictos sobre cada parámetro (tipos de datos, rangos positivos, permisos del usuario y precios reales desde la base de datos) sin asumir jamás que, por venir cifrado desde el frontend, el dato es confiable.
