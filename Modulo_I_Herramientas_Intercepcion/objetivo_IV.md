A continuación se detallan los pasos llevados a cabo para resolver los laboratorios prácticos de la plataforma **Software Seguro**

---

## 1. Apagar la IA

1. Analizando la aplicación, observamos que los archivos se alojan en la ruta `/codes/<id>` y que los identificadores que nos proporcionan tienen la estructura de un hash **MD5**.
2. Enviamos la petición al **Intruder** (de Burp Suite). Configuramos el tipo de payload como *Number* (del `9900` al `12000`) y aplicamos una regla de *Payload Processing* para que cada número sea hasheado automáticamente a **MD5**.
3. Filtramos las respuestas buscando aquellas que devuelvan un código de estado `200 OK`, lo que confirma que el ID existe y contiene un archivo válido.
4. Con la lista de todos los IDs válidos encontrados, ejecutamos un script para recorrer cada uno de estos archivos hasta encontrar el código numérico de 16 dígitos.
5. Una vez obtenido el número de 16 dígitos, lo hasheamos en **MD5** para obtener la solución final.

---

## 2. El mejor secreto

1. Contamos con un video y un archivo `.zip` protegido con contraseña. Como primer paso, analizamos el video.
2. Aunque en la grabación no se distinguen los dígitos exactos que se presionan, logramos identificar el patrón de repetición de teclas: `abccdaddddec`. Al tratarse de un teclado numérico, sabemos que cada letra corresponde a un dígito del `0` al `9`.
3. Utilizando este patrón como filtro, creamos un script para realizar un ataque de fuerza bruta reducido hasta dar con la combinación exacta.
4. Una vez que el script encuentra el código correcto, lo utilizamos para descomprimir el archivo `.zip` y obtener la solución.

---

## 3. Votación

1. El objetivo del laboratorio es lograr que la **UTN** alcance los **3100 votos** para superar a Harvard.
2. Al interactuar con la plataforma, notamos que solo nos permite emitir 1 voto, ya que el sistema nos asigna una cookie de sesión para restringir la acción.
3. Sabiendo esto, interceptamos la petición de voto y la enviamos al **Intruder**. Allí configuramos la herramienta para ir modificando un parámetro en cada petición, logrando que el servidor nos genere una cookie nueva cada vez y contabilice todos los votos como válidos.
4. Una vez que la UTN supera en cantidad de votos a Harvard, el laboratorio queda resuelto.

---

## 4. Votación (Nueva versión)

1. En esta versión buscamos sumar **101 votos** para la UTN. Al intentar repetir el método anterior, comprobamos que no funciona porque la plataforma ahora valida y bloquea según nuestra dirección IP.
2. Para evadir esta restricción, interceptamos la petición, la enviamos al **Intruder** y agregamos la cabecera HTTP `X-Forwarded-For:` seguida de una dirección IP cualquiera.
3. Configuramos el **Intruder** para que modifique uno de los octetos de esa IP 101 veces seguidas, simulando que cada voto proviene de una dirección distinta hasta alcanzar el objetivo.
